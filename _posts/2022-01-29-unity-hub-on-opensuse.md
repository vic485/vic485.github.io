---
layout: post
title: "Install Unity Hub on OpenSUSE"
tags: blog
description: Unity has moved away from providing a packaged hub appimage, leaving OpenSUSE users like myself mostly without official support.
---

## Installing the Unity Hub on OpenSUSE
With the full release of version 3.0 of the Unity Hub, Unity has stopped listing the old appimage installer in favor of repositories for certain distros and package managers. Currently there are very few (literally two or three) officially supported distros, which leaves OpenSUSE users like me mostly out of luck. They do have a CentOS/RHEL rpm repo hosted, so with a little configuring we can install on SUSE.

### Zypper Instructions
From Unity's instructions, we will create a repo file in the same way as they do for CentOS, but send it to our zypper repos instead of yum.
```bash
sudo sh -c 'echo -e "[unityhub]\nname=Unity Hub\nbaseurl=https://hub.unity3d.com/linux/repos/rpm/stable\nenabled=1\ngpgcheck=1\ngpgkey=https://hub.unity3d.com/linux/repos/rpm/stable/repodata/repomd.xml.key\nrepo_gpgcheck=1" > /etc/zypp/repos.d/unityhub.repo'
```
Next, refresh the zypper database and import the gpg key.
```bash
sudo zypper --gpg-auto-import-keys refresh
```
And finally, install.
```bash
sudo zypper in unityhub
```
Zypper may warn you that it can't find a source for libuuid. You can ignore this by entering `2` at the prompt. I haven't noticed any issues with running the hub and editor without this library.

The above was adapted from the official [Unity docs](https://docs.unity3d.com/hub/manual/InstallHub.html#install-hub-linux). For those still prefering the appimage and installer, I have found the [beta](https://public-cdn.cloud.unity3d.com/hub/prod/UnityHubBeta.tar.gz) archive is currently still hosted on the [Japanese Unity](https://unity.com/ja/download) download page. Newer appimages may be able to be extracted/created from the official repositories for unsupported distros.