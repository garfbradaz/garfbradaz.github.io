---
layout: post
author: gareth
category: blog
tags: [jekyll,release-notes,how-to]
excerpt_separator: <!--more-->
---
## Setting up Github Pages, Jekyll and using Windows Subsystem for Linux

The 1st *how to* for setting up the blog and tools needed to edit it. I'm a Windows fan (I love my Surface devices), but I'm also starting to appreciate Linux <!--more-->, so this is a perfect opportunity to set up my Surface Book to use [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/faq), which allows you command line access to Linux. I'm assured this is not a virtual machine, but a [syscall mapping between Windows and Linux itself](https://blogs.msdn.microsoft.com/wsl/2016/04/22/windows-subsystem-for-linux-overview/), to actual Linux binaries themselves.

I'm going to provide the instructions on how I set up *my* Surface Book. You may want to tweak these to your needs.

NB: I originally posted this blog post **December 2018** so if anything has changed or doesn't work, let me know.

## WSL Set up

Windows Subsystem for linux needs setting up. If you have done this previously, then skip to [code instructions](#visual-studio-code)

1. Use Powershell Windows Subsystem for Linux (WSL), make sure you Run as Administrator:

    ```Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux```

2. The prompt will ask you to restart your machine, so make sure you save anything before pressing **Y**:

    ![Powershell screen shot](/assets/img/posts/release-notes_powershell.png)

3. Use the Windows Store to install your favourite Linux Distro. I chose *Debian*. More information [here](https://docs.microsoft.com/en-us/windows/wsl/install-win10#windows-10-fall-creators-update-and-later-install-from-the-microsoft-store)

## Visual Studio Code

Again if you have installed this previously, then jump to [Jekyll](#install-jekyll-on-wsl).

I'm a great VSCode advocate, mainly from using .NET Core and C#. But I'm using TypeScript a little and using it more and more. I actually get a little sad face when I realise I need to use Visual Studio Enterprise for anything now (Usually because Razor support isn't the best in Code, but improving).

1. I'm going to use Visual Studio Code to allow me to compose and edit markdown. Its the perfect editor because:

    - The IDE is cross platform, so you can look after your blog within the IDE across any platform.
    - It has git out of the box.
    - Various Markdown extensions for extending the experience.
    - Markdown Preview, so you can view how your post will look.

    I will do a separate post on my Visual Code extension set up.

2. Download and install Code [here](https://code.visualstudio.com/download).

## Install Jekyll on WSL

As I mentioned previously, I installed Debian, so the instructions I'm giving are for Debian. More more information see [here](https://jekyllrb.com/docs/installation/).

1. Firstly open a bash Window. I fired up Debian app I just installed.

2. Install dependencies for Ruby:

    `sudo apt-get install ruby-full build-essential`

3. Now, I got the following error when I ran this on Debian:

    > Reading state information... Done
    > E: Unable to locate package ruby-full
    > E: Unable to locate package build-essential

4. Essentially the *sources.lst* is missing the *deb-src* links to the repository.

    ![deb-src](/assets/img/posts/deb-src.png)

5. Use `sudo nano /etc/apt/sources.list` to edit the file and add the missing entries. Here is my file

    ![deb-src](/assets/img/posts/deb-src-added.png)

6. Run `sudo apt update`.

7. Re-run step 2. If this works, move on.

8. Add the environment variables for gems for when bash is run interactively:

    ```bash
        echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
        echo 'export GEM_HOME=$HOME/gems' >> ~/.bashrc
        echo 'export PATH=$HOME/gems/bin:$PATH' >> ~/.bashrc
        source ~/.bashrc
    ```
9. Install Jekyll and bundler using `gem install jekyll bundler`.

10. Confirm you have *GCC* and *Make* available:

    ```bash
        gcc -v
        g++ -v
        make -v
    ```
## Install Git on WSL

You may already have *Git for Windows* set up on your Windows instance, but you will need to make sure Git is on WSL as well. Follow these instructions.

1. Install git:

    `sudo apt install git`

2. To validate it has worked, run `git version`.

3. Set up standard email and username details:

    ```bash
        git config --global user.email "your@email.com"
        git config --global user.name "your username"
    ```
4. As described [here](https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration#_code_core_autocrlf_code), need      to set your line endings correctly.

    `git config --global core.autocrlf input`

## Create Github Repository

1. There are different steps that need taking when creating your repository. Firstly the repository names needs to be a      specific format:

    > your-github-username.github.io

   You can find your username my navigating (top right) click Your *Avatar->Your profile*. Example mine is garfbradaz:

    ![me](/assets/img/posts/me-github.png)

   So mine is *garfbradaz.github.io*.

2. You can select a Jekyll theme as well also. This is saved to the `_config.yml` file in your repository. This can be       changed at a later date.

    ![github options](/assets/img/posts/theme-github.png)

3. Once the site is published, your repository name is your URL for your new site:

    [my site](https://garfbradaz.github.io)

## Update Github with generated SSH using WSL

1. Run the following in WSL bash terminal to generate a key, replacing the email with yours that you use for Github:

    `ssh-keygen -t rsa -b 4096 -C  <your@githubemail.com>`

   Press **Enter** to accept the default directory, and add a pass phrase.

2. Add the SSH private key to *ssh-agent* so that you dont need to write the pass phrase each and every git commit:

    `ssh-add ~/.ssh/id_rsa`

   **Note:** This is using **~** which is fine as we are within a Linux bash terminal.

3. Run the following to Print tne RSA key to the screen. Then using your mouse, copy to clipboard:

    `cat ~/.ssh/id_rsa.pub`

4. Navigate to github in your browser of choice.

5. Click on **Your profile**.

6. Click on **SSH and GPG keys**.

7. Click on button **New SSH Key**.

8. Give the key a meaningful title.

9. Paste RSA key.

10. Click on **Add RSA key**.

## Clone the Site to WSL

1. Firstly run *Visual Studio Code*.

2. Open a Terminal within Visual Studio code. One option for doing that is **Menu->Terminal->New Terminal**.

3. Then run bash.

4. Something important to note. After you run bash, you will be greeted with a directory prefixed with `/mnt/c`. This        means the drive is mounted and mapped to `C:\`. You can prove this by doing a `ls`. Look at all those familiar Windows    files:

    ![WSL Bash Directory Listing](/assets/img/posts/windows-files-bash.png)

   **Note:** `cd ~ && pwd ` isn't your traditional home directory, so dont place any files in here you want Windows to access. This **DOES NOT** map to your Windows Home directory. If you edit this Linux Only Windows files, you will corrupt your Linux install.

   Basically it is safe to use the `/mnt/c`.

5. With that in mind, navigate to the folder you wish to clone your Jekyll repository, here is an example for mine (You      do this in the same bash terminal you created in Visual Studio Code):

    `cd /mnt/c/code/blog`

   This is the following Windows directory:

   > C:\code\blog

6. Git clone into this directory:

    `git clone <your-repository-url> .`

   And because you are within Visual Studio Code, the files will appear straight away:

   ![files appear in VSCode](/assets/img/posts/git-in-vscode.png)

7. Now create a file called *Gemfile* and add the following (root of the directory):

    > source 'https://rubygems.org'
    > gem 'github-pages', group: :jekyll_plugins

    ![files appear in VSCode](/assets/img/posts/gem-in-vscode.png)

   As you can see VSCode recognises up its a Gemfile and uses a nice Ruby icon.

8. Now create the dependencies.  Within the bash terminal within Visual Studio code run:

    `bundle install`

   **Note:** At this stage for Debian, I found there was an error installing **nokogirl-1.8.5** dependencies.

   I resolved this by running the following, then re-running `bundle install`.

    ```bash
    sudo apt-get install libpng-dev
    sudo apt-get install --reinstall zlibc zlib1g zlib1g-dev
    ```

## (Optional) Modify Layout of Theme

If you want to amend the layout; add your own styles or navigation, then you need to check your themes repository for instructions on how to update (each may have different instructions).

The URL scheme for the repositories is:

> <https://github.com/pages-themes/your-selected-theme>

So mine is:

[https://github.com/pages-themes/minimal](https://github.com/pages-themes/minimal)

## Update _config.yml

Update your title and description for your blog.

    > title: Gareth Bradleys Blog
    > description: garfbradaz new blog, hosted on github. Rambling about coding, running and > any other of my ramblings.


## Release, Release, Release

After you have proof read it and checked obviously. The beauty of Jekyll is you to **publish** your blog, you just use the git skills you (may) already have, so run the following in bash while in your repository where your `.git` folder is:

    ```bash
    git add .
    git commit -m "Start of life for my awesome blog - Arise!"
    git push origin
    ```

## Next time...

I will talk about *How to Add Google analytics* and *How to publish your first post* all in Visual Studio Code.

I haven't written this blog post without help. I would like to shout out to the following blogs and documentation  for assisting me:

[Dave Rupert](https://daverupert.com/2018/04/developing-on-windows-with-wsl-and-visual-studio-code/)

[Github Pages](https://pages.github.com/)

[Github Docs - SSH](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)

[Microsoft WSL Blog](https://blogs.msdn.microsoft.com/commandline/2016/11/17/do-not-change-linux-files-using-windows-apps-and-tools/)

[Jekyll Docs - Ubuntu](https://jekyllrb.com/docs/installation/ubuntu/)

[Jekyll Docs](https://jekyllrb.com/docs/)
