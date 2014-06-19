---
layout: post
title:  "The Big Bash"
date:   2014-06-19 17:22/l05
categories: bash
---

Have you seen the countless posts that take on Javascript.
They make fun of its *randomness* and type miscellaneous commands
in the console expecting the unexpected. And they are right, but most of the
stuff happen on rare occasions and we encounter them **very scarcely**. 

But that's not the case with ***Bash***...
> **Note:**

>This is the most opinionated and nonobjective blog post you'll see on the internet.

### The Real Deal
Now that we've cleared that out let's take a look at some examples.
Let's start with the basics and assign value to a variable:

{% highlight bash %}
FOO = bar

#outputs:
#FOO: command not found
{% endhighlight %}

Intuitive right? And *FOO* will be unasigned. But we are Bash gurus
and we all know that the spaces are the issue.
So let's try:

{% highlight bash %}
FOO= bar

#If you are on Ubuntu will output:
#The program 'bar' is currently not installed.
{% endhighlight %}

We haven't given up:

{% highlight bash %}
FOO =bar

#And again:
#FOO: command not found
{% endhighlight %}

Wait for it...

{% highlight bash %}
FOO=bar

echo $FOO

#outputs bar
{% endhighlight %}

It all has a reason, in the first case it tries to launch *FOO* and supply
*'='* and *'bar'* as parameters. 
In the second one it assings an empty string to *FOO* and tries to launch *bar*.
And in the last not working one it tries to parse *'=bar'* as a parameter to *FOO*.

Now it all makes sence, right?