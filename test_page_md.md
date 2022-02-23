---
title: MD test
layout: default
---

# Testing Markdown Page Creation
This page tells you that I dont like HTML

## Because markdown is way easier

    as far as I know
        which isn't **really** very far

For example, why didnt **really** come out bold above?

- because it was **indented** ? \\
I suppose so. Indenting seems to put it in a little separate window.

## how to do a link:

{% highlight markdown %}
[Visit My GitHub!](https://github.com/kylejray/kylejray.github.io)
{% endhighlight %}

yields: 
[Visit My GitHub!](https://github.com/kylejray/kylejray.github.io) \\
the link can also be to an image or other media, like this:

{% highlight markdown %}
![Watch this Movie!](assets/images/ani.gif)
{% endhighlight %}

yields: 
![Watch this Movie!](assets/images/ani.gif) \\
but, this seems to only work with gifs and images, if you want it embedded (thats what the ! does).
For real videos, it looks like you want a piece of HTML:

{% highlight HTML %}
<video width="480" height="320" controls="controls">
  <source src="assets/images/oo.mp4" type="video/mp4">
</video>
{% endhighlight %}


<video width="480" height="320" controls="controls">
  <source src="assets/images/oo.mp4" type="video/mp4">
</video>

oh look, we included a little example code above, that was cool...

## how to do code snippets:
wrap the code snippet with the following syntax
{% raw %}
{% highlight language %} snippet {% endhighlight %}
{% endraw %}
for example, below I repalced language with python and snippet with the code

{% highlight python %}
def print_hi(name):
    print (f"Hi, {name}")
    return
print_hi('Tom')
#=> prints 'Hi, Tom'.
{% endhighlight %}







