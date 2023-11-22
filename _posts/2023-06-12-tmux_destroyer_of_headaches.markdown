---
layout: post
title:  "tmux, destroyer of SSH headaches"
date:   2023-06-12-2:30
categories: python, jupyter, remote computing, HPC, anaconda, ipython, tmux, brew
---

### The problem with remote computing.
If you read [my last post](https://kylejray.github.io/python,/jupyter,/remote/computing,/hpc,/anaconda,/ipython/2023/05/01/using_jupyer_remotely.html), you will know that I have been doing more of my day to day coding on remote machines-- to take advantage of the extra power you get from a desktop computer over a laptop. I used to just do this for really large simulations, but I have found that even doing exploratory work in a notebook can be twice as fast if I run it remotely. And, jupyter lab has a really great web interface already, so it makes for a painless transition. The one problem with the method I described is that while you are using  the hardware of the remote machine-- the jupyter lab is being run off of a termiinal on the local one. This doesn't seem to be a problem speed wise, but it is a major problem if you want to move locations while keeping a computation going. And it introduces extra hassle in that you need to [maintain the SSH tunnel](link to tunnel) or the whole thing shuts down.

### tmux saves
The solution here is to use a tool called tmux, which can be installed with brew in the usual way:
{% highlight bash %}
brew install tmux
{% endhighlight %}

This handy tool can be used to dettach terminal sessions from you main window, so that when the main window closes, the session continues. It can do plenty of other stuff too, by looking at the docs-- but we just need it for this very simple use case. Here is the process for getting a jupyter notebook started on the remote server in a way that it wont shut don if you loose your ssh tunnel or your terminal window.

1. ssh into the **remote machine**
2. type tmux into the terminal to start up a tmux session
3. start the jupyter lab instance with --no-browser (keep the port number and key handy)
4. press "*ctrltm* + b", release the keys, and then press "d". This should dettach the tmux session you just started, and you should be able to close the terminal window without interrupting it.
5. The jupyter instance should keep going until you manually close it by opening up another ssh tunnel to the **remote machine** and close it by using the tmux kill-session command. Here is a handy [tmux cheatsheet](https://tmuxcheatsheet.com/).
