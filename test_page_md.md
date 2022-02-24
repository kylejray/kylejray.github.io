---
title: MD test
layout: default
---

# Testing Markdown Page Creation
This page tells you that [I dont like HTML](/test_page_html.html)

## Because markdown is way easier

    as far as I know
        which isn't **really** very far

For example, why didnt **really** come out bold above?

- because it was **indented** ? \\
I suppose so. Indenting seems to put it in a little separate window. But, so does wrapping text in the `"left apostrophe"` thing right below tilde (or 3 of them for multiple lines). See below

``` 
djsakld
djaskdasld
djkal
```

I actually cannot figure out how to replicate the separate box from just indenting...
##### maybe it's the heading?
    indented
        also indented
        same as above
    same as the first
*on the next line, but not indented at all*
            indented 3 times

not indented, but also with a line break between the last thing. 
    indented again
Ok. it seems like it must be that indenting after a heading makes a box. Strange indeed...

### Looks like markdown isn't easier after all
Though it does seem more bearable to experiment with.



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
`{% highlight language %}` 
snippet 
`{% endhighlight %}`
{% endraw %}

if you replace `language` with `python` and snippet with some python code, you get:

{% highlight python %}
def print_hi(name):
    print (f"Hi, {name}")
    return
print_hi('Tom')
#=> prints 'Hi, Tom'.
{% endhighlight %}







