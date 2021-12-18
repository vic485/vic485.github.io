---
layout: post
title: "Fixing Localized Linux Folders"
tags: blog code
description: Sometimes there are things you shouldn't localize. Folder paths in linux being one of them.
---

## Fixing Localized Linux Folders
A visually pleasing part of modern operating systems is the localization of frequently accessed folders along with the UI. If you're not familiar with what localization is, think of it basically as translating while making it feel natural in the target language. For quite a while now, I have been using Windows in Japanese due to some games I play and for work purposes. Recently, I've been wanting to switch to Linux for various reasons, but in a test install I quickly noticed a big issue. Namely in the difference in how the two operating systems handled localizing certain folders.

Taking a look at windows, we can see how in the file explorer, certain folders show as being in the language installed on the system, but on disk they are all still in English.
![Windows Paths](https://i.imgur.com/Nj4GrkE.png)
In Linux, we see a very different picture. The folders in the home directory are actually in the system language on disk!
![Linux Home Directory](https://i.imgur.com/ACGwXQr.png)

This poses two big problems with usability on Linux out of the box:
1. **Having type a different language for these folders is annoying.** This may seem petty, but it doesn't make a lot of sense to have to change inputs just to type these folder names. Especially from a design standpoint this is confusing, as they are the only folders in the entire system that aren't in English.
2. **Most distros do not install an IME by default.**  Yes, without an IME or secondary layout installed it is typically impossible to type both languages at the same time. This means you won't be able to work with the directories until you install it yourself, or you will have to copy the names from a directory listing each time.

Fortunately, with Linux being as customizable as it is, there is a pretty easy fix in the form of the command `xdg-user-dirs-update`. Below is a quick example of how it would be used.
```bash
mkdir ~/Documents
xdg-user-dirs-update --set DOCUMENTS $HOME/Documents
```
This can become tedious to do for every user directory however, especially if you have to keep (re)installing pcs. To combat this, I made a quick script to automatically change each folder to the English counterpart, which I put on [Github](https://github.com/vic485/Random-Shell-Scripts/blob/main/change-folders.sh). The one point to watch for when running this script is to make sure it is on the desktop so it can copy the `.desktop` file properly if it exists. I have only seen this file on Manjaro so far, but there may be other distros that also have it.

There is one more step that the script doesn't cover, which is changing the shortcuts in your file explorer. Some may do this automatically, however dolphin I know does not. In dolphin, you can easily right click the link, edit, and change the path to be the new folder.

Now in the end, you might be asking:

> Why go through all this? Couldn't you just install Linux in English and then change the language pack later?

To which I will say, sure that *sort of* works. However it is not very consistent with certain desktop environments. I'm using KDE Plasma with Manjaro, and when changing the language pack after installing it will apply to the desktop and applications, but the login shell remains in the originally installed language. So far I have found no solution to this, with many places pointing to still open issues.
