---
layout: post
title:  "First Jekyll Blog: How To"
date:   2020-09-20 18:34:42 +0530
categories: no-nonsense guides
---
So when a bunch of neurons fired to make me feel like starting a blog, it was tough to quickly digest the variety of methods the community used, just to start creating a GitHub blogsite.

Ok, ok, I hear you, I hear you, and those bunch of neurons came to a verdict.
```bash
If you are able to get this thing working.
Write the first blog on how to do what you just did: minus the clutter.
```
What a great time to be alive. Now you know why you are here. So let's get straight into it.

You need to know two things to make your blogsite:
1. How to write `markdown` files.
2. How to use keyboard and mouse.

Know already? Great. We're good to go.

If you are on Ubuntu/Linux, the steps are straightforward. If you are a Windows user, unfortun... noo we have an easier way. You can emulate (not really, but containerize) a Linux environment on Windows using WSL.

## Environment Setup

0. [Windows only] Install WSL: Windows Subsystem for Linux, branch to this [page](wsl-install.html) if you haven't done that before, and return.

1. Fire up a terminal (WSL or Linux, it should be the same).
2. Sequentially shoot these commands:

    ```bash
    sudo apt-get update -y
    sudo apt-add-repository ppa:brightbox/ruby-ng # add PPA to repository
    sudo apt-get update # refresh package list
    sudo apt-get install ruby2.5 ruby2.5-dev build-essential dh-autoreconf # install ruby, which will install gems (and later jekyll)
    ```
3. Set up your Ruby to run in non-sudo mode. This is sometimes required if you face permission errors. Better do it.
    ```bash
    # Append Ruby exports to bashrc
    echo "export GEM_HOME=$HOME/gems" >> ~/.bashrc
    echo "export PATH=$HOME/gems/bin:$PATH" >> ~/.bashrc
    ```
4. Update Ruby Gems and install Jekyll:
    ```bash
    gem update
    gem install jekyll bundler
    ```
5. This will take some time. Sit back, relax.
6. Once finished, navigate to your workspace.
    ```bash
    cd # Here I'm navigating to my $HOME dir
    jekyll new cliche_hello_world # This is where your webpages will be stored
    cd cliche_hello_world/
    bundle exec jekyll serve
    ```
7. Fire up your browser with the given address. Most cases it is the `localhost:4000`. This is how it should look like:
    ```bash
    Configuration file: /home/user/cliche_hello_world/_config.yml
            Source: /home/user/cliche_hello_world
       Destination: /home/user/cliche_hello_world/_site
    Incremental build: disabled. Enable with --incremental
      Generating...
       Jekyll Feed: Generating feed for posts
                    done in 0.543 seconds.
                    Auto-regeneration may not work on some Windows versions.
                    Please see: https://github.com/Microsoft/BashOnWindows/issues/216
                    If it does not work, please upgrade Bash on Windows or run Jekyll with --no-watch.
    Auto-regeneration: enabled for '/home/user/cliche_hello_world'
    Server address: http://127.0.0.1:4000/
    Server running... press ctrl-c to stop.
    ```

## Structuring Your Blogs

It's quite easy actually. Navigate to your newly created `cliche_hello_world` jekyll project.

1. Have a look at `_config.yml`. This has the one-time-info about your blogsite like email, handles etc.
2. There is a directory `_posts/` which is the abode of your blogs, written in `markdown`. (Heyy you know how to write markdowns, right? See, so easy it is!)
3. Name your blog markdown file in the format `2020-09-20-blogname.markdown`. Date is indicative of how jekyll will identify it.

Happy blogging!