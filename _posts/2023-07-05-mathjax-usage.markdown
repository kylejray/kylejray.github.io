---
layout: post
title: 'Getting Nice LaTeX Equations in the blog'
subtitle:  "using mathjax in a jekyll created github-pages hosted website"
permalink: /:title/

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

Of course, this method can be applied without the redundancy of adding {% raw %}{% include mathjax.html %}{% endraw %} to every page. You can simply download the base.html layout from your jeckyll theme of choice and add the mathjax one-liner just above where the page content is created. As an example, here is how you would edit the minima theme base.html to allow mathjax on every page.


{% highlight html %}
    <main class="page-content" aria-label="Content">
      <div class="wrapper">
        {% raw %}{% include mathjax.html %}
        {{ content }}{% endraw %}
      </div>
    </main>
{% endhighlight %}

But, like I said above-- this will come at the cost of having more stuff to maintain yourself. You are no longer using the minima theme's base.html file but your own custom one.