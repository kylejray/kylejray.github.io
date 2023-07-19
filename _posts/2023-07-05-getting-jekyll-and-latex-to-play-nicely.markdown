---
layout: post
title:  "using LaTex in a jekyll created github-pages hosted website"
date:   2023-07-05
categories: jekyll, github, github-pages, Latex, mathjax

---
{% include mathjax.html %}

I am by no means a jekyll expert. So much so, that I only very recently found a way to incorporate LaTex into these blog posts. I've certainly tried a few times to figure it out... but most solutions I found would have required me to download the jekyll theme layouts/includes into my website repository and then edit them. This really is not a great option as far as I am concerned, because one of the benefits of using this jekyll/github-pages combo is that I do not have to do anything to maintain or update my theme. As long as I am happy with what comes in the box, I can trust the experts to format things for me. And, if I want to change my theme on a whim... all I have to do is type a new theme in the config file.

What I finally figured out is that if I accept a little bit of redundancy, I can get LaTex into the blog without having to pull down any layouts from jekyll. I can't claim this is the most elegant solution. But it does appear to be working, and it is exceptionally simple to do! Here is how you do it

1. create a mathjax.html text file in your site's _includes/ directory containing the following:
{% highlight html %}
<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
{% endhighlight %}
{:start="2"}
2. At the beginning of the page you want ot use LaTex in, right under the front matter write:

    {% raw %}{% include mathjax.html %}{% endraw %}
3. Now, when you write `$$ x+1=3 $$`, it will come out looking like $$x+1=3$$.




