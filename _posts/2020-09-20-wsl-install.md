---
layout: post
title:  "WSL: Your new daily development driver."
excerpt: "Setting up a wizardry was once painful on Windows, as easy it was on Linux. I recently switched to Windows Subsystem for Linux, to handle my light development activities, like this Jekyll blog. You won't hate it for sure."
date:   2020-09-20 19:57:42 +0530
---

Visual Studop Code has been around for all my development over Remote-SSH at my workplace. Windows development isn't just as comfortable, to someone whose mainline work has revolved around a lot of packages, libraries and builds. Sure, you could install all Windows packages for common utilities like Git, Bash, Diff tools and GCC, but something always felt disconnected doing that on Windows.

Now it is obvious to guess what else I could do: Install a VM (heck, that's slow) or Dual Boot. But wait, even though I am OK with fiddling the BIOS, I would try not to, atleast because not being the true asset owner. Knowing that Windows Linux Subsystem had been around for a while, I thought of giving it a shot.

Now to my surprise, WSL was fast, efficient, and just-what-I-needed to handle my light development activities. Your mileage may vary, but here are a few reasons why *you* would want to move to a unified Windows/Linux environment. So, if you:

1. Like VMs, but they're often too resource heavy and slow to start.
2. Like dual-boots, but either your BIOS/driver configurations hurt your chance of fiddling.
3. Don't know how to pull-off a dual boot properly. You fear screwing it up.
4. Just want some Linux-native apps on Windows.
5. Just love the terminal.

Then WSL is probably good for you. Steps to install are quite simple.

## Enabling WSL-1* in Windows
1. Now there is an implicit assumption made here. You need to be on a recent 64-bit Windows 10 to install WSL-1 (version 2004 and above for WSL-2).
2. Fire up `powershell`, as an `Administrator` and paste this command:
    ```bash
    dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
    ```
    If you prefer the GUI way:

    Start > Search "Turn Windows Features on or off" > Enable "Windows Subsystem for Linux" > OK.

    <div class="imgcap">
    <img src="{{ site.baseurl }}/assets/wsl/wsl_enable.png">
    <div class="thecap" style="text-align:center">Enabling WSL on Windows</div>
    </div>


    >"Wait, I don't see that option!?"

    You're probably on an older update of Windows 10. WSL came around in 2016. Time to update your OS, long time.

3. Reboot.
4. Go to the Microsoft Store. Search for any Ubuntu distro of your choice.
    If you are a novice, I'd recommend Ubuntu 18.04 LTS. You can even choose from Fedora, OpenSUSE etc.

5. Download. That is it.

It should be in your Start menu. Or use the decent search option right there. Set-it up as-per on-screen instructions, and you are good to go.

You'll be greeted with a home-feeling terminal, in your home directory. **Bonus:** If you have Visual Studio Code installed on Windows, you can even get started, right away.

```bash
# Use git to clone your project, start your own
# Navigate to your project directory
quark@karan:~/my-project-dir$ code .
# BAM!
```
This will open your VSCode editor, right on WSL!

## WSL-2

WSL-1 is more of a container running on top of an emulated Linux kernel, on top of the regular Windows kernel (Jeez! That's an inception moment right there).

This limits your ability to install kernel-specific tools like Docker (which uses Linux cgroups). WSL-2 was introduced in the Fast-Ring insider development of Windows.

For you, all that means is, WSL-2 shall become mainstream from Windows ver.2004 and upwards.

## What changes with WSL-2?

Newer is faster in terms of File IO, and uses full-fledged Linux kernels on top of Hyper-V. This allows you to install native Linux kernel applications like Docker.

Unless you're a performance freak, or have stuff to do closer to kernel and syscalls, you are just better-off using WSL-1 for lightweight development.

I shall update this blog to include WSL-2 install procedure soon.
