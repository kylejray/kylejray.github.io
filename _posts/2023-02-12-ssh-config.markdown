---
layout: post
title: 'Make ssh as Painless as Possible'
subtitle:  "how to train your ssh config file"
permalink: /:title/

date:   2023-02-12-1:30
categories: work from home, ssh, remote computing, scp, bash
---

In the era of hybrid and remote work, I think we are probably all finding ourselves using remote computers more often than we might have previously. It used to be that I would occasionally log into the computing cluster to start up or set up large simulations, but most of the time I was running from whatever computer I happened to be nearest at the time-- wether that be my laptop or my lab computer. At this point though, "remoting" into another machine to pull down some new code or push changes I forgot to push last week is an almost daily event. So I am using SSH... a lot. 

### Part 1: SSH is already pretty simple

SSH is one of the simplest things to do in the terminal.  Ultimately all you need is internet and an account to log in on the **remote machine**. Just type:

{% highlight bash %}
ssh <REMOTE USER NAME>@<REMOTE MACHINE>
{% endhighlight %}
It will them prompt a password, or perhaps ask you to verify that the host machine is trusted and then ask fo a password. Boom! You are logged in with a terminal that can run stuff on the remote machine.

### Part 2: Simple: yes; annoying: also yes

While the above isn't really very complicated, it is kind of annoying to have to type in the username and remember the address of the remote computer and also the password every time. The solution here is to add frequently used servers into your ~/.ssh/config file. Add the following to the config file:

Host <SERVER NICKNAME>
	HostName <REMOTE MACHINE>
	User <REMOTE USER NAME>

Now, all you need to type is 

{% highlight bash %}
ssh <SERVER NICKNAME>
{% endhighlight %}
But, it is still going to ask for your password. This is nice, but let's take it to the next level.

### Part 3: Using an SSH key instead of a password
Check to see if you have a pair of files in ~/.ssh/ called something like XXXX and XXXX.pub. Here, the X are means to be wildcards. Mine are called id_rsa necasie of the type of key I used. If you have these files it means you already have an ssh key we can use, and you can skip the next step.

 If you don't have a public/private key pair, you'll need to make one using the [ssh-keygen](https://www.ssh.com/academy/ssh/keygen) command. The docs are linked there, if you want to do some research on what kind of key to use. For example, let's say you want to make a 2048 bit rsa key-- you would then enter:
    {% highlight bash %}
    ssh-keygen -t rsa -b 2048
    {% endhighlight %}
 - Even if you enter ssh-keygen with no arguments, you will be fine. Asking your system admin people if they need a particular kind of security is probably the best way to decide this kind of thing. The important thing here though, is to **NOT ENTER A PASSWORD**. The whole point is getting away from those, so just don't enter anything when it prompts you to set a password for the key (unless your system admin people tell you that you need to).

* Take the XXXX.pub key on your **local machine** and put it into a file called ~/.ssh/authorized_keys on the **remote machine**. There are several ways to do this, though scp is probably the most universal. 
* On the **remote machine** run the following command:

{% highlight bash %}
chmod 600 authorized_keys
{% endhighlight %}

This sets permissions so that the user can read and write, but others cannot. Once this is accomplished you should be able to make an SSH tunnel by simply typing 

{% highlight bash %}
ssh <SERVE NICKNAME>
{% endhighlight %}

with no need for a password at all. 

Depending on you setup, things can be a bit tricker. For example, your 
**remote machines** might be behind a firewall (ours are). In this case, you might need to log in through some other authorization system first. For this kind of indirect route, you will need to add a ProxyCommand line to the ~/.ssh/config entry we made earlier. This line will tell the computer what server it needs to get through in order to reach you ultimate destination. But, at this point-- you really will need to get info from your system admins since the details will likely vary from institution to institution.








