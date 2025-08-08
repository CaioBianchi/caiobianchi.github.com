---
layout: post
title: "My Complicated Relationship with Ruby"
date: 2025-08-08 12:00:00 -0400
categories:
  - blog
  - coding
---

I'll say it upfront: I genuinely enjoy writing Ruby. Its focus on developer happiness is real. The language is elegant, expressive, and the Ruby on Rails framework is a productivity powerhouse. But no language is perfect. After years of working with it, certain "features" and quirks have gone from minor annoyances to significant frustrations.

So, let's talk about the things I don't like about Ruby.

---

## The `Gemfile.lock` Merge Conflict Nightmare

The `Gemfile.lock` is supposed to be our friend. It ensures every developer on a team, as well as the production server, is using the exact same version of every gem. It promises consistency and repeatable builds. And it delivers on that promise... right up until you have to merge a branch.

If you've worked on a busy Rails app, you've felt this pain. Two developers add or update different gems in separate branches. The result? A horrifying `Gemfile.lock` merge conflict. Resolving these isn't just a matter of picking one side or the other; you have to run `bundle lock` or `bundle update` on specific gems to regenerate the file correctly. It's a constant, tedious interruption that plagues teams and adds zero value. While other ecosystems have lockfiles, Ruby's seems uniquely prone to creating these frustrating scenarios.

---

## The Elephant in the Room: Performance 🐘

Ruby is slow. There's no gentle way to put it. As an interpreted language, it was never going to win a drag race against compiled languages like Go or Rust. But it also lags behind JIT-compiled languages like modern JavaScript (V8) or Java.

For many standard web applications, this isn't a deal-breaker. Your bottleneck is usually database queries or network I/O, not raw CPU speed. But the moment you need to do anything computationally intensive—image processing, complex data analysis, a high-throughput API endpoint—you feel the ceiling.

Yes, projects like **YJIT (Yet Another Ruby JIT)** are making incredible strides in speeding things up. But for now, the performance limitations often force developers to build performance-critical services in other languages, creating a polyglot architecture that adds its own complexity.

---

## The Wild West of Code Formatting

In the Go world, you have `gofmt`. For Rust, `rustfmt`. In JavaScript, Prettier is the de facto standard. These tools automatically format code with zero configuration, ending all arguments about style. They are official, opinionated, and universally adopted.

Ruby has... **RuboCop**.

And while RuboCop is an incredibly powerful linter and formatter, it's not a simple, built-in solution. It comes with hundreds of configurable "cops," leading to endless debates about which rules to enable. Every company has its own `.rubocop.yml` file, a unique fingerprint of its stylistic biases. This lack of a single, blessed formatter means more configuration, more bike-shedding, and less consistency across the ecosystem. I just want to write code, not debate the merits of single versus double quotes for the hundredth time.

---

## Monkey Patching: The Double-Edged Sword 🐒

One of Ruby's most "magical" features is open classes, which allow you to modify any class at runtime—even core ones like `String` or `Integer`. This is called **monkey patching**, and it's what powers much of Rails' convenience, like being able to write `5.days.ago`.

It's clever, but it's also dangerous. When a library you depend on decides to change the behavior of a fundamental Ruby class, it can have bizarre and unpredictable side effects on your own code. It breaks encapsulation and makes code incredibly difficult to reason about. You might call a standard method and get unexpected behavior because a gem, loaded ten levels deep in your dependency tree, decided to "improve" it. It’s a powerful tool that is too often used like a footgun.

---

## Concurrency and the GIL

Ruby's approach to concurrency is hampered by the **Global Interpreter Lock (GIL)**. In simple terms, the GIL is a mechanism that ensures only one thread can execute Ruby code at any given moment within a single process.

This means that even on a fancy 16-core processor, your multi-threaded Ruby program can only use one core at a time for CPU-bound work. You get I/O concurrency (great for waiting on database calls or HTTP requests) but not true parallelism.

The community has clever workarounds, like running multiple processes (e.g., Puma in cluster mode) or using the newer Ractor model for parallel execution. But these are more complex than the straightforward multi-threading available in languages like Go, Java, or Rust, making the GIL a significant architectural constraint.

```

```
