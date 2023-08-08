---
layout: post
title:  "Flutter on Apple Silicon Mac: Cocoa-pods Issue"
date:   2023-06-22 09:15:10 +0530
categories: [CrossPlatform, Flutter]
tags: [Mac rosetta, Mac M1]
author: Saurav Nagpal
pin: true
published: true
sitemap: true
content: true
---
## Step 1: 
Flutter requires the rosette must be installed on your machine. 
Rosetta is a translation library which allows users to run apps that contain X86_64 instructions on Apple silicon Mac.

{% highlight bash %}

sudo softwareupdate --install-rosetta --agree-to-license

{% endhighlight %}

## Step 2: 

Cocoa-pods require a terminal using UTF-8 encoding.  Sometimes only setting "LANG=en_US.UTF-8" not works. you need to perform the following steps:


1. Open Terminal
2. 
{% highlight bash %}
nano ~/.zshrc ("or nano ~/.profile if you don't use zsh")
{% endhighlight %}

3. Apeen the following in the editor:

{% highlight bash %}
export LANG=en_US.UTF-8
export LANGUAGE=en_US.UTF-8
export LC_ALL=en_US.UTF-8
{% endhighlight %}

4. Save the file and quit the terminal.
5. Open the file again and verify.



Step 3 (Optional)

if you still see an error related to "ffi" then reinstall the gem "ffi" in the system with the following command.

{% highlight bash %}
sudo gem uninstall ffi && sudo gem install ffi -- --enable-libffi-alloc
{% endhighlight %}
