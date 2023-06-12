---
layout: post
title:  "leveraging the sleep command"
date:   2023-04-21-1:30
categories: git, bash, ssh, remote computing
---

### sleep is good

My experience using bash is near zero, but I have been playing around with a couple of simple bash commands that I find pretty useful. The workhorse in these is the "sleep" command combined wth "while true; do". The combo is incredibly simple, but I find uses for it all the time. Here are some examples of how I am using it:

#### Keep the SSH Alive Script
{% highlight bash %}
 while true; do
        date
        sleep 900
done
{% endhighlight %}
This will do nothing more than print the date every 15 minutes. I find that it helps keep SSH tunnels open while waiting for results. They tend to just shut themselves off if they get bored. 

 
#### Auto Updating Script
We can also package this same idea into a script that can be saved into a file and run externally. First, put the following into a plain text file

{% highlight bash %}
#!/bin/bash
repo_url='https://github.com/<USER NAME>/<REPO NAME>'
branch='HEAD'
while true; do
        git -C ~/source/ pull $repo_url $branch
        sleep 3600
done
{% endhighlight %}
To run this script, you first make it executable by running 
{% highlight bash %}
chmod +x <FILE NAME>
{% endhighlight %}

If you run this script simply by calling its name in the terminal, it will automatically pull down from the HEAD branch on the given repo every hour. I find this very useful to run on some remote machines that I rarely make code on, but often run code on.


