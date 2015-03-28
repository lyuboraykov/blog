---
layout: post
title:  "About Code Maintainability"
date:   2014-10-03 14:22/l05
categories: shell-scripting
---

Have you ever played with command line arguments in shell scripting (Bash)?
One day I decided to write a simple script that I would paste in my doings and use it to parse arguments instead of sourcing one.

The thing is, there is *no* standard way of doing it.
There is `getopt` but it doesn't support long argument names, unless you use the GNU one.
But then it becomes less portable and you can't easily define mandatory arguments.
That's why I came up with this:

{% highlight bash %}
#!/usr/bin/env bash

# Console-line parameter parsing script.
# Specify options in the OPTS variable in the following format:
# whitespace sepparated values
# OPTS="--<argument_name> --<argument_with_value>= --!<mandatory_argument>="
# Then it creates variables in the format: OPTS_<ARGUMENT_NAME_IN_CAPS>
# If the argument doesn't need value to be provided assings 0 or 1.
# If the argument needs value but none is provided a 0 is assigned

#SCRIPT START
OPTS="--argument --anotherarg --!thirdarg= --fourtharg="

IFS='--' read -ra args <<< "$OPTS" && for i in "${args[@]}"; do
   if [ ! -z $i ]; then
      ARG_NAME="${i//[=! ]/}"
      ARG_VALUE=$(printf -- '%s\n' "${@}" | grep -- "--$ARG_NAME")
      if [[ "$i" =~ ^.*=\ ?$ ]]; then
         if [ -z "${ARG_VALUE##--$i}" ]; then
            [[ "$i" == *!* ]] && echo "$ARG_NAME is mandatory!" && exit 1
            printf -v "OPTS_${ARG_NAME^^}" "0"
         else
            printf -v "OPTS_${ARG_NAME^^}" "${ARG_VALUE##--$ARG_NAME=}"
         fi
      else
         [ -z "${ARG_VALUE##--$i}" ] ; ARG_VALUE=$?
         printf -v "OPTS_${ARG_NAME^^}" "$ARG_VALUE"
      fi
   fi
done
# SCRIPT END


echo "OPTS_ARGUMENT: $OPTS_ARGUMENT"
echo "OPTS_ANOTHERARG: $OPTS_ANOTHERARG"
echo "OPTS_THIRDARG: $OPTS_THIRDARG"
echo "OPTS_FOURTHARG: $OPTS_FOURTHARG"

{% endhighlight %}

### What's wrong
It's great for what it does. It lets me define arguments in a simple manner, having args with value and mandatory ones.
But then, 1 month later I had to edit it and then I remembered all the articles about code maintainability I've seen. 
The beest thing I could do is write one myself. I'll even not go into any reasoning for mainainable code.

There it is, the *code snippet* - all the reasoning you need.