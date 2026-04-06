---
layout: post
title: "The Downfall and Enshittification of Microsoft in 2026"
date: 2026-04-06
categories: blog
tags: [microsoft, windows-11, copilot, github, apple, linux]
---

For years, Microsoft survived on inertia. Windows was the default. Office was the default. GitHub, after the acquisition, became the default forge for software teams. That kind of position lets a company coast for a long time.

In 2026, Microsoft looks less like a platform steward and more like a company trying to squeeze every surface it owns for one more strategic advantage, usually AI-shaped, often at the expense of product quality. That is the heart of enshittification: the gradual replacement of user value with company priorities, while still insisting the user is being helped.

## Windows 11: The Written Apology Arrived Before the Fixes

The clearest sign that Microsoft knows it has a problem is that it effectively published an apology. In March, Pavan Davuluri and the Windows Insider team posted "Our commitment to Windows quality," promising a renewed focus on performance, reliability, and "well-crafted experiences."[^windows-quality]

That post is remarkable not because it is visionary, but because it openly acknowledges years of user frustration. Microsoft promised:

- more taskbar customization, including vertical and top taskbars
- fewer forced-update disruptions and more control over restarts
- a faster, more dependable File Explorer
- quieter widgets and less distraction
- fewer unnecessary Copilot entry points

Those are not moonshots. They are repairs. They are admissions that Windows 11 shipped for years without satisfying basic expectations from people who use a desktop computer all day.

That is the indictment. In 2026, Microsoft is promising to restore ordinary desktop affordances while many users still cannot do basic things cleanly: place the taskbar where they want, trust File Explorer to feel snappy, avoid update nagging, or navigate an interface that is not split between old control panels, half-finished redesigns, AI surfaces, and ad-like recommendations.

The company is asking for credit simply for saying it will stop making the operating system worse.

## Copilot Everywhere, Whether You Asked or Not

If Windows 11 feels unfocused, Copilot is a big reason why. Microsoft spent the last two years trying to position Copilot not as one feature, but as the center of the entire company. Windows was reframed as an AI operating system. Office became Microsoft 365 Copilot. GitHub became GitHub Copilot plus agents plus cloud agents plus code review agents. Even the official Windows language started sounding like a distribution channel for Copilot instead of the other way around.[^ai-pc]

What makes this especially damaging is not just the existence of AI features. It is the feeling that core product quality was allowed to stagnate while executive attention chased AI placement. Microsoft's own March post all but says this by promising to "reduce unnecessary Copilot entry points" in apps like Snipping Tool, Photos, Widgets, and Notepad.[^windows-quality]

That wording matters. You only promise to reduce unnecessary Copilot entry points if you already added too many unnecessary Copilot entry points.

The result is a suite that often feels upside down. Instead of making Windows, Office, and GitHub excellent first and then adding optional AI where it helps, Microsoft repeatedly treated AI as the strategy and the products as vessels.

## GitHub: Still Dominant, Increasingly Annoying

GitHub is not collapsing, but it is drifting into the same pattern: more incidents, more Copilot-centric complexity, more friction around a product category where reliability should be boring.

The public status page does not support the strongest anti-GitHub claim floating around online. Core `Git Operations` still shows `99.78%` uptime over the past 90 days, and APIs, Issues, and Pull Requests remain above 99% as well.[^github-status] If you are talking specifically about raw version-control availability, "below 90%" is not what the public numbers show.

But the broader frustration is still understandable. In just the recent incident history, GitHub logged disruptions across Pull Requests, Actions, Code Search, audit logs, and multiple Copilot services, sometimes on back-to-back days.[^github-status] One March 24 incident even reported the Microsoft Teams GitHub Notifications app peaking at `90.1%` failed requests during the outage window.[^github-status] That is not the same thing as git itself going sub-90, but it does reinforce the perception of a platform becoming noisier and less dependable as it grows more layered.

For infrastructure that sits in the middle of software delivery, that matters. Developers do not judge availability only by whether `git push` works. They judge it by whether pull requests load, checks start, webhooks fire, search indexes fresh code, and the surrounding workflow remains trustworthy. By that standard, GitHub in 2026 has had too many bad days.

## The Mood on X

The public conversation reflects the same pattern: users and Windows commentators are not merely annoyed by bugs, they are annoyed by the sense that Microsoft had to be bullied into rediscovering product basics.

Some representative posts circulating on X in 2026:

- Zac Bowden wrote that Microsoft appeared to have shelved plans to bring Copilot deeper into Windows 11 shell surfaces like notifications and Settings, adding that the company was reevaluating where Copilot belongs in the OS.[^zac]
- Windows Central posted that Microsoft was already scaling back Copilot in Windows 11 and reducing some Copilot access in Microsoft 365 scenarios, a notable reversal after spending so much time trying to put Copilot everywhere.[^windows-central-scaleback]
- Windows Central also framed the company as betting the future of Windows on Copilot while usage data told "a very different story."[^windows-central-usage]

Even when the criticism comes from ecosystem journalists rather than random posters, the theme is the same: Microsoft over-rotated toward AI, then had to retreat toward basic usability.

## Apple's Opening: The $599 MacBook Neo

Microsoft's timing is especially bad because Apple finally moved downmarket in a meaningful way. In March, Apple introduced the MacBook Neo at a starting price of `$599`, or `$499` for education, calling it its most affordable laptop ever.[^macbook-neo]

That changes the conversation.

For years, Windows defended a lot of its rough edges with one obvious point: it was everywhere, on every price tier, and often cheaper. But a fanless Apple laptop with all-day battery life, a good display, and the usual tight hardware-software integration at $599 is exactly the kind of product that makes average consumers ask an uncomfortable question: why am I tolerating Windows nonsense at all?

Apple is not perfect, and Apple Intelligence has its own hype layer. But there is a meaningful difference between shipping AI on top of a coherent laptop experience and shipping AI while your users are still begging for a sane taskbar and a calmer update flow.

## The Linux Desktop Is Still Small, But It Is No Longer Just a Meme

Linux has not suddenly conquered the desktop, but the old joke that it is permanently irrelevant looks weaker every year. Statcounter's worldwide desktop numbers for March 2026 put Linux at `3.16%`.[^statcounter] That is still small, but it is large enough to matter, especially when the modern Linux desktop is no longer synonymous with driver roulette and hand-written `xorg.conf` files.

Fedora, Ubuntu, Mint, Pop!_OS, Bazzite, and various Arch-based setups are increasingly usable for ordinary people and very attractive to developers. Steam Deck and Proton changed expectations around Linux gaming. WSL's continued importance is also a weird admission by Microsoft itself: Linux is central enough to modern development that Windows has to embed it.[^windows-quality]

And while Statcounter splits Apple's desktop family into `OS X` and `macOS`, their combined share is already far above Linux and still growing pressure on Windows from the premium and mainstream sides.[^statcounter]

In other words:

- Apple is pressuring Windows from above and now from below with the MacBook Neo
- Linux is pressuring Windows from below and from the developer class
- Microsoft is responding by promising to fix problems it created while pushing Copilot into everything

That is not a position of strength.

## What Microsoft Could Do to Stop the Slide

The solutions are not mysterious.

First, Microsoft needs a product moratorium on AI sprawl. Copilot should be optional in Windows, optional in Office, optional in developer workflows, and easy to disable without registry edits, policy hacks, or enterprise SKU caveats.

Second, Windows needs a boring year. No grand vision. No reinvention theater. Just shipping the desktop basics people have asked for, making them fast, stable, and coherent.

Third, GitHub needs to treat reliability as a product feature, not a status-page footnote. That means fewer cascading service dependencies, clearer incident communication, and real restraint around turning every developer workflow into a Copilot upsell surface.

Fourth, Microsoft needs to stop confusing "we heard feedback" with "we fixed the product." Blog posts about listening are not substitutes for software that feels finished.

## Is This the Year of the Linux Desktop or Apple's Majority Moment?

Probably not in the literal sense, not yet.

Windows still has enormous institutional lock-in. Enterprises move slowly. Games still bias toward Windows. A huge amount of consumer hardware still ships with Windows by default. "Year of the Linux desktop" has become a punchline precisely because predictions outrun reality.

But 2026 does feel like a threshold year.

If Microsoft keeps turning products into Copilot delivery mechanisms while Apple undercuts the entry price for decent laptops and Linux keeps gaining legitimacy among developers, students, and cost-conscious users, then the center of gravity starts to move. Not all at once. Then all at once enough to matter.

So maybe this is not yet the year of the Linux desktop, and maybe it is not yet the year of an Apple desktop majority either.

It may be the year Microsoft forced people to seriously imagine both.

## Sources

[^windows-quality]: Pavan Davuluri and Windows Insider Program Team, ["Our commitment to Windows quality"](https://blogs.windows.com/windows-insider/2026/03/20/our-commitment-to-windows-quality/), Windows Insider Blog, March 20, 2026.
[^ai-pc]: Microsoft Windows Blog, ["Making every Windows 11 PC an AI PC"](https://blogs.windows.com/windowsexperience/2025/10/16/making-every-windows-11-pc-an-ai-pc/), October 16, 2025.
[^github-status]: GitHub, [GitHub Status](https://www.githubstatus.com/), accessed April 6, 2026.
[^macbook-neo]: Apple, ["Say hello to MacBook Neo"](https://www.apple.com/newsroom/2026/03/say-hello-to-macbook-neo/), March 4, 2026.
[^statcounter]: Statcounter Global Stats, [Desktop Operating System Market Share Worldwide](https://gs.statcounter.com/os-market-share/desktop/worldwide), March 2026 data, accessed April 6, 2026.
[^zac]: Zac Bowden, [post on X](https://x.com/zacbowden/status/2033211622203007108), March 15, 2026. The linked post was not directly retrievable in this environment because X blocks unauthenticated rendering, but its indexed snippet states Microsoft appeared to have shelved plans to bring Copilot to shell surfaces like notifications and Settings.
[^windows-central-scaleback]: Windows Central, [post on X](https://x.com/WindowsCentral/status/2033921922191303034), March 17, 2026. Indexed snippet describes Microsoft scaling back Copilot in Windows 11 and reducing some Microsoft 365 Copilot access.
[^windows-central-usage]: Windows Central, [post on X](https://x.com/WindowsCentral/status/2025933511937376462), February 23, 2026. Indexed snippet describes Microsoft calling Copilot the "king of productivity" on Windows 11 while usage data suggested weaker adoption.
