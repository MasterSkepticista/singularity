---
layout: post
title:  "No-Nonsense Guide to Installing WSL"
date:   2020-09-20 19:57:42 +0530
categories: no-nonsense guides
---
Why would you want to install it? In a nutshell, WSL opens doors (not literally) to your very comfortable Linux/Ubuntu OS, without the overhead of VMs, or, the painful process of Dual-Boot (This gets especially intense if you're using Intel Optane acceleration). Hence, think of WSL like a container running Ubuntu. All of Linux, minus the overhead and effort.

What you need to know: How to use a mouse and keyboard.

Ofcourse this requires you to be on Windows.

## Enabling WSL-1* in Windows
1. Now there are certain implicit assumptions made here. You need to be on a 64-bit Windows 10, version 1903 and above.
2. Fire up `powershell`, as an `Administrator` and paste this command:
    ```bash
    dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
    ```
    If you like GUIs:

    Start > Search "Turn Windows Features on or off" > Enable "Windows Subsystem for Linux" > OK.
3. Reboot.
4. Go to the Microsoft Store. Search for any Ubuntu distro of your choice.

    If you are a novice, I'd recommend Ubuntu 18.04 LTS.
5. Download. That is it.

It should be in your Start menu. If not, use the decent search option right there. Set-it up as-per on-screen instructions, and you are good to go.

*On the WSL version: WSL-2 is faster in terms of File IO, kernel level changes (allowing near-usual Linux Kernels) and much better virtualization capabilities. Unless you're a performance freak, or have stuff to do closer to kernel and syscalls, you are just better-off using WSL-1.

I shall update this blog to include WSL-2 install procedure soon.
