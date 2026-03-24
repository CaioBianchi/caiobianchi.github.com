---
layout: post
title: "Omarchy on a Kamrui Pinova P2: Two Hundred Dollars of Chaos and Joy"
date: 2026-03-23
tags: [linux, omarchy, hyprland, arch, mini-pc, devtools]
---

A few weeks ago I bought a Kamrui Pinova P2 off Amazon for around two hundred and fifty dollars. AMD Ryzen 4300U, 16GB of DDR4 (single channel, socketed and upgradeable), 512GB M.2 NVMe. The whole thing is about the size of a thick paperback. It came with Windows 11 Pro installed, which I booted exactly once to confirm the hardware worked, then immediately reached for a USB stick.

The plan was to install Omarchy.

If you haven't heard of it: Omarchy is DHH's "opinionated" Arch Linux setup built around Hyprland, the Wayland tiling compositor that the r/unixporn crowd won't shut up about. Version 2.0 shipped with an actual ISO installer and dropped the AUR dependency, which made it a lot more approachable than the original script-based approach. I'd been following it since the initial release in June and finally had an excuse to try it properly on dedicated hardware rather than a VM.

---

## Getting In

The first snag was the BIOS. Kamrui ships these things with Secure Boot enabled and a boot order that will fight you. Getting into the BIOS means hammering `Delete` or `F7` during POST, which on the Ryzen 4300U happens faster than you'd expect. Once in, you need to disable Secure Boot (it's buried under Security, not Boot, because of course it is), then drag your USB stick to the top of the boot priority list.

Omarchy's ISO booted fine. The installer is a clean ncurses-style interface that walks you through partitioning, locale, timezone, and user creation. It's not Calamares, it's not Anaconda. It's closer to what you'd recognize from a manual Arch install, but with the tedious parts scripted away. I went with a simple layout: EFI partition on `/boot/efi`, ext4 for root, no swap (the 4300U handles light dev workloads just fine with 16GB and I wasn't going to hibernate this thing anyway).

The installation itself took about eight minutes on a decent WiFi connection. pacstrap pulls down the packages, the installer writes the bootloader, and you reboot into a working Hyprland session. The first time Hyprland initializes and you see the custom Omarchy wallpaper and the Waybar status bar rendered correctly, with working animations and that screensaver DHH mentions, it's a genuinely impressive moment for what amounts to someone's dotfiles plus an Arch base.

---

## The WiFi Situation

Here's where it got annoying.

The Pinova P2 uses a MediaTek MT7921 wireless chip, which is generally well-supported on Linux but has a known quirk with the `mt7921e` driver on some kernel versions: power management gets aggressive in ways that cause the card to drop off the bus intermittently. On the kernel that shipped with the Omarchy ISO at the time (6.11.x), my 5GHz connection would associate cleanly, pass traffic for a few minutes, and then go silent without any useful error in `dmesg` beyond a vague timeout.

The fix is a one-liner in `/etc/modprobe.d/mt7921.conf`:

```
options mt7921e disable_aspm=1
```

After a rebuild of the initramfs with `mkinitcpio -P` and a reboot, 5GHz has been stable since. Getting to that point required plugging in via Ethernet first (the Gigabit port worked out of the box with `r8169`, zero fuss), searching the Arch wiki, and a bit of patience reading through kernel bug reports. If you know what you're looking for it's about fifteen minutes of work. If you don't, it'll feel like the machine is haunted.

This is hardware lottery stuff that Omarchy can't really be blamed for. The MT7921 is popular enough that the community coverage is decent, but you do need to know where to look and have a fallback connection method. If you're doing this on a machine with no Ethernet port, budget some time for the wireless debugging phase.

---

## The Environment

Once it's running cleanly, Hyprland on this hardware is a revelation for what it costs. Animations are smooth. Workspace switching with `Super+1` through `Super+5` is instant. The Omarchy configuration ships with sensible keybindings, a `rofi`-based launcher, `kitty` as the terminal, and Neovim as the default editor (obviously). The bar uses Waybar with a custom theme that doesn't look like a generic i3 setup from 2019.

The whole thing idles at around 2-3% CPU and uses maybe 800MB of RAM sitting at a terminal. Compare that to a stock GNOME session on Ubuntu, which will happily claim 1.2GB just to show you a desktop. One pleasant surprise with the 4300U's integrated Radeon graphics: Hyprland's hardware-accelerated rendering via `wlroots` is noticeably smoother than what you'd get from an Intel UHD chip at the same price point. Workspace animations don't stutter. Screen sharing via `xdg-desktop-portal-hyprland` works without manual intervention.

Neovim with a full LSP setup for Ruby (via `solargraph` or `ruby-lsp`, take your pick) runs without any perceivable lag. The 4300U is not fast by any absolute measure, but it's more than adequate when you're not wasting cycles on a compositor that does 3D shadows and blur effects by default.

---

## AUR-Free by Default

One of the things the 2.0 release made a point of was removing AUR dependencies from the base install. This matters more than it sounds. The AUR is great for availability but introduces a trust surface you have to think about, especially on a machine you're actually going to use for work. Omarchy now ships its own package repository for things that would have previously required AUR helpers.

The tradeoff is that occasionally you want something that's only on the AUR anyway. `yay` installs in about two minutes. But the point is you make that choice deliberately rather than having it baked in from the start.

---

## Daily Use

I've been using this machine for a few weeks now as a secondary workstation. It lives next to my main machine, VESA-mounted behind a monitor. The use case is: run a Rails dev environment, keep a `tmux` session with a few panes open, write code, push commits.

The Hyprland tiling model maps well onto how I already work. I have Neovim in one workspace, a split `tmux` session with a running server and a Rails console in another, a browser in a third. Switching between them is a keypress. Nothing overlaps. There's no alt-tab juggling. After a day or two of getting the muscle memory in place it stops feeling like a workflow and starts feeling like just... using a computer.

One thing I didn't expect to care about: Omarchy ships with a custom Chromium fork that supports live theme switching so the browser matches your Hyprland color scheme. It sounds cosmetic and it mostly is, but after using it for a while you notice how much visual noise a chrome-default browser adds to a carefully configured desktop. It's a small thing. It's a nice thing.

---

## On the Hardware Choice

A Kamrui Pinova P2 is not exciting hardware. It's not a Framework. It doesn't have an interesting story. It's a tidy little box built around a Zen 2 APU that AMD was shipping into laptops four years ago, and it costs about as much as a GPU upgrade you'd forget about in six months.

But that's sort of the point. Putting Omarchy on it closes the gap between "cheap box" and "machine I actually want to use." The Radeon integrated graphics are just capable enough that Hyprland runs beautifully, and Omarchy's curation means you're not spending a week configuring things before you can work. The combination of a rolling Arch base, a modern Wayland compositor, and an opinionated but coherent default configuration turns $250 of generic x86 into something that feels intentional.

I wouldn't use it for anything compute-heavy. Build times for a large Rails app are noticeable. Video encoding isn't happening. But as a dedicated workstation for writing and running code, reading documentation, and staying in a terminal most of the day, it's completely adequate and it costs almost nothing.

If you have a box like this sitting around collecting dust, it's worth an afternoon.
