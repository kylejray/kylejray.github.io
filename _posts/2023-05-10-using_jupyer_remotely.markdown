---
layout: post
title:  "using a jupyter notebook to run code on a remote server"
date:   2023-05-01-1:30
categories: python, jupyter, remote computing, HPC, anaconda, ipython
---

### Part 1: How to run Jupyter Lab on a remote computer

Now that I am fully immersed in the hybrid workflow, I find myself juggling different computers more and more. For example, I rarely go into the lab on Mondays-- but might find myself wanting to do some higher powered computing in a notebook. Symbolic manipulation is, for example, at least two times faster on my iMac in the lab than my laptop. It turns out that running a jupyter notebook on a remote server is super easy, allowing you to use a remote computer's superior processing power with the convenience of a portable local machine. Honestly, it makes me wonder if I should ever be hosting notebooks on my laptop anymore...

Before doig this, make sure you can set up a an ssh tunnel into the **remote machine** from your local one. Anyway, here is what you do:

1. SSH into the **remote machine** ; start jupyer lab on the remote server using:

{% highlight bash %}
jupyter lab --no-browser
{% endhighlight %}
If everything ent according to plan, the terminal will give you a URL to access the jupyter lab you just started. Look for something like:

{% highlight bash %}
http://localhost:<PORT NUMBER>lab?token=<TOKEN>
{% endhighlight %}

Keep the port number and token handy for now, because using this URL will not currently work. We must move on to step 2

2. On your **local machine**, youll need to open up an ssh tunnel to port \<PORT NUMEBR\> on the **remote machine** with the following command:

{% highlight bash %}
ssh -L <PORT NUMBER>:localhost:<PORT NUMBER> <REMOTE USERNAME>@<REMOTE MACHINE>
{% endhighlight %}

Note that you might need to put in a password depending on your ssh settings for both machines. (if you have a server listed in your ~/.ssh/config, you can just use the server name rather than the whole \<REMOTE USERNAME\>@\<REMOTE MACHINE\> deal).

3. We're done now.  Almost. Go to a brower on your local machine and try the URL:

{% highlight bash %}
http://localhost:<PORT NUMBER>
{% endhighlight %}

Did it work? Probably not, because it is going to prompt you to enter the \<TOKEN\> from earlier. Once you do that, you should be home free.

### Part 2: Is this useful?

Well, if you are trying to do some prototyping that requires any cells that would be sped up by the remote machine then I suppose the answer is yes. Though, if none of your cells take longer than a handful of seconds, this might be offset by the extra lag time of using a remote machine. But, what about using this for larger runs of code like a large suite of simulations using MPI. You can actually manage these kinds of runs with a notebook without too much trouble, the major hurdle here is making sure that the notebook uses the right environment. This can be done with proper configuring of your **remote amachine**: installing an ipython kernel for the environment you want to run in. In order to do so, you need to install ipykernel:

{% highlight bash %}
conda install -c anaconda ipykernel
{% endhighlight %}

Once you have ipykernel, and an anaconda environment you want to run the notebook on; follow these steps:

1. activate the environment you want to run a notebook in
{% highlight bash %}
conda activate <ENV NAME>
{% endhighlight %}

2. install the environment as an ipython kernel
{% highlight bash %}
python -m ipykernel install --user --name=<ENV NAME>
{% endhighlight %}

Now, when you go to the select kernel dropdown in jupyter lab, you should be an option for the environment you installed.

Troubleshooting note: I made sure that my environment had both jupyter and iython installed on them before I tried this-- but I am not sure if it is strictly necesarry. The issue I mostly ran into was not activating the target environment before trying to install it as a kernel.








