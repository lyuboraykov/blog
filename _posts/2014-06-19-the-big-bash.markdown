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

![](http://www.bildschirmarbeiter.com/content/images/why-fu/why-fu-013.jpg)


It all has a reason, in the first case it tries to launch *FOO* and supply
*'='* and *'bar'* as parameters. 
In the second one it assings an empty string to *FOO* and tries to launch *bar*.
And in the last not working one it tries to parse *'=bar'* as a parameter to *FOO*.

Now it all makes sence, right? 

I'm not done yet.
We all know *if* blocks should end with *fi*.
For instance:

{% highlight bash %}
if [ ! -z $FOO ]; then
    echo $FOO
fi
{% endhighlight %}

Same goes for *case* - *esac*.

{% highlight bash %}
case $FOO in 
   bar )
    echo bar ;;
   bar2 )
    echo bar2 ;;
esac
{% endhighlight %}

Souds legit. And speaking of consistency - all loop code blocks (for, while, until)
should begin with *do* and end with *done*...

{% highlight bash %}
for i in $(seq 1 2 20)
do
   echo "foo $i"
done
{% endhighlight %}

![](http://www.bildschirmarbeiter.com/content/images/why-fu/why-fu-010.jpg)

## Time to get serious...

We can all and we have gotten over it. But there is one thing I can't
cope with in Bash - the sheer amount of syntax. Bare with me.

Look at the example I gave for the *if* conditional block.

{% highlight bash %}
if [ ! -z $FOO ]; then
    echo $FOO
fi
{% endhighlight %}

The same thing can be achieved like that:

{% highlight bash %}
if [[ ! $FOO = '' ]]; then
    echo $FOO
fi
{% endhighlight %}

ditto:

{% highlight bash %}
test ! -z $FOO && echo $FOO
{% endhighlight %}

I'm not done:

{% highlight bash %}
[ ! -z $FOO ] && echo $FOO
{% endhighlight %}

For function declaration you can use the *function* keyword or use *parantheses*.


For text manipulation you have *sed*, *awk*, *grep*, *expr*, *=~*, even *##*, *%*, *#* for substrings and both regular expressions and wildcards.
The list goes on and on...

![](http://www.bildschirmarbeiter.com/content/images/why-fu/why-fu-016.jpg)

## The fearsome shell

I'm aware of the **small tools** philosophy and it is genious, but
is it confusing and intimidating for a newcomer. It sure is flexible, 
but intuition has no place in the code of a Bash developer. You'll say that it's not 
meant for big projects, but why shouldn't it be, it has so many capabilities.

Or as a friend of mine once said:

> I read articles about 'Best practises in Bash' everyday, but in the end of the day,
 I say to myself: "Get over it - it's Bash".