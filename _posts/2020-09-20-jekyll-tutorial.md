---
layout: post
title:  "First Jekyll Blog: How To"
date:   2020-09-20 18:34:42 +0530
excerpt: "It has been quite a while to do some full-tilt blogging. Tried (once) on medium, but eventually prepared myself to do it with the intent of (also) making an open source contribution. I started on Jekyll."
---

So when a bunch of neurons fired to make me feel like starting a blog, it was tough to quickly digest the variety of methods the community used, just to start creating a GitHub blogsite. The other option was to write on Medium, which I attempted once, [here](https://medium.com/@karanshah1698/yet-another-installing-tensorflow-gpu-on-windows-2d7fc64af394). Medium is great for articles, don't get me wrong. But making an open source contribution seemed not a bad idea afterall. Not to mention a bit of web development that I would be taking along with Github pages.

Promise, I'll make this tutorial as easy as I can. Because when I got it up and running for the first time, I took up a TODO.

```bash
If you are able to get this thing working.
Write the first blog on how to do what you just did: minus the clutter.
```

What a great time to be alive. So let's get straight into it.

You need to know two things to make your blogsite:
1. How to write `markdown` files.
2. A bit of patience.

If you are on Ubuntu/Linux, the steps are straightforward. If you are a Windows user, unfortun... wait; we have an easier way. You can have a Linux environment, baked right into your Windows, hiding from you... a Linux environment on Windows using WSL. Branch [here](wsl-install.html) if you're into installing that. It's quick!

#### Environment Setup

1. Fire up a terminal (WSL or Linux, its now the same).
2. Sequentially shoot these commands:

    ```bash
    # refresh package list
    sudo apt-get update -y
    # add PPA to repository. This tells ubuntu where to search for Ruby
    sudo apt-add-repository ppa:brightbox/ruby-ng
    # refresh package list, again
    sudo apt-get update
    # install ruby, which will install gems (Ruby package manager, of sorts)
    sudo apt-get install ruby2.5 ruby2.5-dev build-essential dh-autoreconf
    ```

3. Set up your Ruby to run in non-sudo mode. This is sometimes required if you face permission errors. Better do it. These two commands tell Ruby as to where to search for *gems* (packages), every time you spawn a terminal.
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
    quark@karan:~$ cd # This takes you to $HOME

    # This is where your webpages will be stored
    quark@karan:~$ jekyll new cliche_hello_world
    quark@karan:~$ cd cliche_hello_world/

    # Build the sample/template as-is
    quark@karan:~/cliche_hello_world$ jekyll build

    # Serve your website locally
    quark@karan:~/cliche_hello_world$ jekyll serve
    ```
7. Fire up your browser with the given address. Default is the `localhost:4000`. This is how it should look like:
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
                    If it does not work, please upgrade Bash on Windows or run Jekyll
                    with --no-watch.
    Auto-regeneration: enabled for '/home/user/cliche_hello_world'
    Server address: http://127.0.0.1:4000/
    Server running... press ctrl-c to stop.
    ```

#### How do you start?

Navigate to your newly created `cliche_hello_world` jekyll project.

1. Have a look at `_config.yml`. This has the one-time-info about your blogsite like email, handles etc. 'Global variables' of sort.
2. There is a directory `_posts/` which is the abode of your blogs, written in `markdown`. Writing markdown files is quite easy. Go through [this](https://www.markdownguide.org/basic-syntax) to understand how to format in markdown. If you ever wrote HTML in your life, the transition is really easy.
3. Name your blog file in the format `yyyy-mm-dd-blogname.markdown`, for example: `2020-09-20-jekyll-tutorial.markdown`.
    1. Date tells jekyll to identify and sort your blogs on your blog home.
    2. Name in this file is for your correspondence.
    3. Title of your blog, as it would appear on title bar/webpages, is defined in a small header on top of your markdown file. Here is a sample:
    ```markdown
    ---
    layout: post
    title:  "First Jekyll Blog: How To"
    date:   2020-09-20 18:34:42 +0530
    ---
    ```

Happy blogging!