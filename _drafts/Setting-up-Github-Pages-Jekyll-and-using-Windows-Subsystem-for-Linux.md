---
layout: post
author: gareth
category: blog
tags: [jekyll,release-notes]
---
## Setting up Github Pages, Jekyll and using Windows Subsystem for Linux

The 1st *how to* for setting up the blog and tools needed to edit it. I'm a Windows fan (I love my Surface devices), but I'm also starting to appreciate Linux, so this is a perfect opportunity to setup my Surface Book to use [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/faq), which allows you command line access to Linux. I'm assured this is not a virtual machine, but a [syscall mapping between Windows and Linux itself](https://blogs.msdn.microsoft.com/wsl/2016/04/22/windows-subsystem-for-linux-overview/), to actual Linux binaries themselves.

I'm going to provide the instructions on how I setup *my* Surface Book. You may wanty to tweak these to your needs. 

NB: I originally posted this blog post **December 2018** so if anything has changed or doesnt work, let me know.

### WSL Setup

Windows Subsystem for linux needs setting up. If you have done this previously, then skip to [code instructions](#visual-studio-code)

1. Use Powershell Windows Subsystem for Linux (WSL), make sure you Run as Administrator:

    ```Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux```

2. The prompt will ask you to restart your machine, so make sure you save anything before pressing **Y**:

    ![Powershell Screenshot](/assets/img/posts/release-notes_powershell.png)

3. Use the Windows Store to install your favourite Linux Distro. I chose *Debian*. More information here:

	[](https://docs.microsoft.com/en-us/windows/wsl/install-win10#windows-10-fall-creators-update-and-later-install-from-the-microsoft-store)

### Visual Studio Code

Again if you have installed this previously, then jump to [Jekyll](#install-jekyll-on-wsl).

I'm a great VSCode advocate, mainly from using .NET Core and C#. But I'm using TypeScript alittle and using it more and more. I actually get alittle sad face when I realise I need to use Visual Studio Enterprise for anything now (Usually because Razor support isnt the best in Code, but improving).

1. I'm going to use Visual Studio Code to allow me to compose and edit markdown. Its the perfect editor because:

    a. The IDE is cross platform, so you can look after your blog within the IDE across any platform.
    b. It has git out of the box.
    c. Various Markdown extensions for extending the experience.
    d. Markdown Preview, so you can view how your post will look

    I will do a separate post on








