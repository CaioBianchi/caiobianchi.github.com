---
layout: post
title: "Migrating from Sidekiq to Solid Queue in Rails 8+"
date: 2026-03-12 12:00:00 -0400
categories: blog
---

For over a decade, Sidekiq has been the undisputed king of background job processing in the Ruby on Rails ecosystem. Powered by Redis, it offered blazing speed, reliability, and an excellent ecosystem. But with the release of Rails 8, the "omakase" menu has officially changed. DHH and the Rails core team introduced **Solid Queue**, a DB-backed background job processor that leverages modern RDBMS features to eliminate the need for Redis.

If you are looking to simplify your infrastructure, reduce moving parts, and take advantage of transactional consistency between your app data and your job queue, migrating to Solid Queue is the logical next step.

In this extensive guide, we will walk through the technical steps, challenges, and strategies for migrating a production Rails application from Sidekiq to Solid Queue.

## Why Migrate?

Before we start ripping out code, let's understand the *why*:

1. **Infrastructure Simplification:** Dropping Redis means one less service to monitor, secure, update, and pay for. 
2. **Transactional Consistency:** With your jobs living in the same database as your application data (or a separate database but still standard SQL), you gain ACID guarantees. You can enqueue a job inside a database transaction and know for a fact that the job won't be picked up by a worker before the transaction commits.
3. **Advanced RDBMS Features:** Solid Queue utilizes `FOR UPDATE SKIP LOCKED`, a feature now standard in PostgreSQL, MySQL, and SQLite, allowing highly concurrent queue polling without locking contention.

## Prerequisites

To follow this guide, you should be on **Rails 8.0 or higher**. While Solid Queue works on Rails 7.1, Rails 8 embraces it natively, and tools like `mission_control-jobs` integrate seamlessly.

## Step 1: Installation and Configuration

First, let's get Solid Queue into your application. Since Rails 8 includes it by default for new applications, you might only need to add it if you are upgrading an older app.

```ruby
# Gemfile
gem "solid_queue"
gem "mission_control-jobs" # For the UI, replacing Sidekiq Web
```

Run `bundle install` and then install Solid Queue:

```bash
bin/rails generate solid_queue:install
bin/rails db:migrate
```

This generates a config file (`config/solid_queue.yml`) and creates several tables: `solid_queue_jobs`, `solid_queue_executions`, `solid_queue_scheduled_executions`, etc. These tables use a modern schema designed for rapid ingestion and dispatching.

Next, configure your environments. In `config/environments/production.rb`:

```ruby
# Change from :sidekiq to :solid_queue
config.active_job.queue_adapter = :solid_queue
```

## Step 2: Replacing Sidekiq-Specific APIs

If you've strictly adhered to the `ActiveJob` API, you are in luck. Changing the adapter is mostly plug-and-play. However, Sidekiq provides specific features that bypass ActiveJob. You need to refactor these.

### 1. Job Invocations

**Old Sidekiq way:**
```ruby
MyWorker.perform_async(user.id)
MyWorker.perform_in(5.minutes, user.id)
```

**New ActiveJob / Solid Queue way:**
```ruby
MyJob.perform_later(user.id)
MyJob.set(wait: 5.minutes).perform_later(user.id)
```

### 2. Retries and Error Handling

Sidekiq handles retries implicitly. Solid Queue relies on ActiveJob's retry mechanisms. You'll need to explicitly define your retry logic inside your job classes.

```ruby
class MyJob < ApplicationJob
  queue_as :default

  # Retries 5 times, with an exponential backoff
  retry_on StandardError, wait: :exponentially_longer, attempts: 5

  def perform(*args)
    # Do work
  end
end
```

### 3. Concurrency and Unique Jobs

If you used Sidekiq Enterprise or `sidekiq-unique-jobs` to prevent duplicate jobs, you now have native ActiveJob concurrency controls in Rails 8.

```ruby
class ReportGeneratorJob < ApplicationJob
  limits_concurrency key: ->(user) { user.id }

  def perform(user)
    # Only one job per user ID will run concurrently
  end
end
```

## Step 3: The Zero-Downtime Migration Strategy

You cannot simply swap the adapter, deploy, and turn off Redis. In-flight jobs and scheduled jobs sitting in Redis would be lost. Here is the safest deployment strategy:

### Phase 1: Dual Running

1. Add Solid Queue to your app, but **keep Sidekiq as your default adapter**.
2. Deploy the Solid Queue worker processes alongside your existing Sidekiq workers. At this point, Solid Queue workers are idling.
3. If you have any specific jobs you want to migrate first, you can explicitly set their queue adapter:

```ruby
class LowPriorityJob < ApplicationJob
  self.queue_adapter = :solid_queue
  # ...
end
```

### Phase 2: The Flip

1. Change `config.active_job.queue_adapter = :solid_queue` in your production environment.
2. Deploy.
3. Now, *all new jobs* are being written to PostgreSQL/MySQL and processed by Solid Queue. 
4. **CRITICAL:** Do not shut down your Sidekiq workers yet! They need to stay alive to drain any jobs still sitting in Redis, especially scheduled jobs (`perform_in`).

### Phase 3: The Drain

Monitor your Sidekiq Web UI. Depending on how far out you scheduled jobs, this could take hours, days, or weeks. 

*Pro-tip:* If you have jobs scheduled months into the future in Sidekiq, write a one-off Rake task to read from the Sidekiq `ScheduledSet`, enqueue them into Solid Queue, and delete them from Redis.

```ruby
# Example Rake task to migrate scheduled jobs
Sidekiq::ScheduledSet.new.each do |job|
  # Parse job.args and enqueue via ActiveJob
  # Then job.delete
end
```

### Phase 4: Cleanup

Once the Sidekiq queue is at absolute zero, you can safely:
1. Stop the Sidekiq worker processes.
2. Remove `sidekiq` from your Gemfile.
3. Remove the Redis dependency (if it's not used for caching or ActionCable).

## Step 4: Monitoring with Mission Control

Sidekiq Web is legendary. To replace it, Rails provides `mission_control-jobs`. It gives you a beautiful, native dashboard for Solid Queue.

Mount it in your `config/routes.rb`:

```ruby
Rails.application.routes.draw do
  # Secure this in production!
  authenticate :user, ->(user) { user.admin? } do
    mount MissionControl::Jobs::Engine, at: "/jobs"
  end
end
```

Mission Control allows you to inspect queues, retry failed jobs, and manage your workers directly from your Rails app, without needing an external Redis GUI.

## Performance Considerations and Database Load

Moving from Redis (in-memory) to a Relational Database (disk-backed) introduces new performance dynamics. 

Solid Queue is incredibly fast because it heavily utilizes `SKIP LOCKED`. However, your database *will* see an increase in `INSERT`, `UPDATE`, and `DELETE` operations.

**Tips for scaling Solid Queue:**
- **Separate Database:** Rails 8 supports multiple databases out of the box. The best practice for high-throughput applications is to point Solid Queue to its own database.
  
  ```yml
  # config/database.yml
  production:
    primary:
      # app db config
    queue:
      # solid queue db config
      migrations_paths: db/queue_migrate
  ```

- **Connection Pooling:** Ensure your worker processes have a sufficient connection pool size configured in `database.yml`.
- **Vacuuming (Postgres):** Because Solid Queue deletes rows frequently when jobs complete, ensure PostgreSQL's autovacuum daemon is tuned properly for the Solid Queue tables to prevent bloat.

## Conclusion

Migrating from Sidekiq to Solid Queue in Rails 8 is a paradigm shift. It brings us back to the majestic monolith, reducing infrastructure overhead while maintaining high performance. 

By carefully transitioning your ActiveJob API usage and executing a multi-phase deploy to drain your legacy Redis queues, you can achieve a zero-downtime migration and bid farewell to your Redis bill forever.