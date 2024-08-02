---
layout: post
title: "Fixing Jellyfin Autoplay Through Tailscale"
tags: blog computers
description: Figuring a small but big issue with running jellyfin over tailscale.
---

## Fixing Jellyfin Autoplay Through Tailscale
Out of the box, jellyfin works great over tailscale. Tailscale as a mesh, nat hole punch VPN has been great for me to securely access my home server when away from home. When my ISP changed recenly I was put behind a double NAT and haven't been able to get a public IP pointed at me by said ISP. One major issue I started running into however, has been the auto playing of content on devices connected via tailscale. Namely I noticed that trying to play music on my phone would not auto play the next song unless the jellyfin application was in focus.

It took a little bit of work to figure out the issue and remedy. The issue only happened on my phone, and only when connected over tailscale. An old post on reddit I found mentioned making sure that the "Allow Remote Connections" settings was turned on, which I had. However in this I noticed another setting which I thought may help. There is a "LAN Networks" section which mentions taking a list of addresses and subnets to treat as local. It took a little bit of calculation to find the approximate subnet from devices on my tailscale network, but after placing both my home router and tailscale subnets, auto play of music worked! Below is an example setup in the jellyfin 10.9 settings.
![Jellyfin settings](https://imgur.com/nn9AdXj.png)
