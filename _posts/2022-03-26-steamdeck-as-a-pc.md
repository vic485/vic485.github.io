---
layout: post
title: "Using a Steam Deck as a PC"
tags: blog computers
description: Fuller instructions for getting full pc access on SteamOS
---

## Using a Steam Deck as a PC
Since the beginning, Valve has touted "the steam deck is a pc" in their marketing. This has remained true with them giving easy instructions for accessing the boot loader, and even providing drivers for Windows. The out-of-box SteamOS install also allows you to enter "desktop mode" which drops you into a standard KDE desktop environment. With this, I wanted to extend my desktop mode experience and see about installing packages and software on the system. Valve's support pages mention that using KDE Discover (flathub) to install software is officially supported, however I needed some system packages not available this way. As it turns out, getting packages and software through pacman is a bit more work.
>Warning 
{: .note .warning}
To reiterate Valve's words, you should not do the following unless you know what you are doing. As I am writing this blog post on my second day of actually having my deck, I cannot confirm whether the following will introduce issues on your device going foward. Perform these actions at your own risk.

### Enabling root access
Out of the box the default user account `deck` has no password. Like this we can not perform any root-level actions even though the account is an administrator. This is farily simple to fix by running the command:
```bash
passwd
```
Enter and confirm a new password for the account, and you are ready to move forward.

### Setting up pacman
Here's where I hit some hurdles when I first went at it. Valve has set up some things in a way that can make it a little confusing if you aren't too familiar with arch systems already. First, the root partition is mounted as read only, preventing pacman from downloading and installing anything, so we will need to disable that.
```bash
sudo steamos-readonly disable
```

Next comes the real trick. Since all updating of the system gets handled through the console mode, the PGP keyring has not been setup. To fix this and get to installing packages, we need to run a few commands
```bash
sudo pacman-key --init
sudo pacman-key --populate archlinux
sudo pacman -Sy archlinux-keyring
```

With this your steam deck should now essentially be able to run as a full arch-based distro.

Big thanks to the following for helping me figure all of this out
- [Steam Deck Desktop FAQ](https://help.steampowered.com/en/faqs/view/671A-4453-E8D2-323C)
- [Mario García on Dev.to](https://dev.to/mattdark/signature-is-unknown-trust-arch-linux-on-vbox-3452)
