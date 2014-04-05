---
layout: post
title:  "The Pretty Logger"
date:   2014-04-05 14:22/l05
categories: python
---

Do you remember the first time you tried to 
understand *recursion*. I sure do remember mine. You stared
at a typical fibonacci example and you couldn't figure out
what **magic** was driving it. And all of a sudden it came clear
when you drew the function calls tree.
Or maybe it was only me.


###So Here Comes Logging
Recently I came across python's _sys.settrace()_
and I saw a possibility. You supply a function to it and it is called
whenever function calls, returns, exceptions and so on occur. I thought I
could use it to create a logger that writes the function calls in a tree representation.
And so came the *Pretty Logger*.


###Spoiler Alert!
Just to clear the idea out, suppose we have the following simple program:
{% highlight python %}
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)

if __name__ == '__main__':
    fibonacci(3)
{% endhighlight %}

If we use the PrettyLogger we would want an output that looks like this:
{% highlight python %}
\ 
 (1) [15:25:51] fibonacci(3) 
  |\ 
  | (2) [15:25:51] fibonacci(2) 
  |  |\ 
  |  | (3) [15:25:51] fibonacci(1) 
  |  |  |
  |  | (3) [0:00:00] 
  |  |\ 
  |  | (3) [15:25:51] fibonacci(0) 
  |  |  |
  |  | (3) [0:00:00] 
  |  |
  | (2) [0:00:00] 
  |\ 
  | (2) [15:25:51] fibonacci(1) 
  |  |
  | (2) [0:00:00] 
  |
 (1) [0:00:00]
{% endhighlight %}


####How It's done.
I looked at [sys.settrace](https://docs.python.org/2/library/sys.html#sys.settrace)
in the Python documentation and it said that to start using it you would need
to supply a tracing function. But it isn't a very good approach
to make the user call _sys.settrace(prettylogger)_ whenever he wants to start logging.
So I took an [OOP](http://en.wikipedia.org/wiki/Object-oriented_programming) approach 
to the problem.

{% highlight python %}
import sys
from datetime import datetime, date

class PrettyLogger(object):
    def __init__(self, logfile=sys.stdout, max_indent=0):
        pass
    def start_logging(self):
        pass
    def stop_logging(self):
        pass
    def _tracefunc(self, frame, event, arg):
        pass
{% endhighlight %}

So this is the logger's class structure. It's obvious why to import _sys_,
but for the _datetime_ module you'd have to trust me that it'll come in handy.
We define the constructor to accept two parameters:

*   logfile - object that implements the write method to be used for logging.
*   max_indent - the maximum depth of the logging tree. Zero for full tree.

As for the **_tracefunc**, we'll come by it later.


Now we need to add some functionality to the methods.

{% highlight python %}
import sys
from datetime import datetime, date

class PrettyLogger(object):
    def __init__(self, logfile=sys.stdout, max_indent=0):
        self.logfile = logfile
        self.indent = 0
        self.time_stack = []
        self.escaped_functions = ['_remove', '__call__']
        self.max_indent = max_indent

    def start_logging(self):
        sys.settrace(self._tracefunc)

    def stop_logging(self):
        sys.settrace(None)

    def _tracefunc(self, frame, event, arg):
        pass
{% endhighlight %}

We attach the _logfile_ and the *max_indent* to the class. *self.indent* is 
the current indent of the tree, we'll use the *time_stack* to monitor the time 
of each function call and *escaped_functions* are the functions we do not want to trace.
*start_logging* registers our *_tracefunc* and *stop_logging* removes it (that's a little friendlier to use).


###Now we can start logging!
Let's first look at the parameters that the the tracing function accepts.

*   frame - the current python [Frame](http://docs.python.org/2/reference/datamodel.html#types) object.
*   event - can be *'call'*, *'return'*, *'line'*, *'exception'*, *'c_call'*, *'c_return'* or *'c_exception'* depending on the function action.
*   arg - depending on the event, None for the usages we have.

Now we'll start with a simple variables declaration:

{% highlight python %}
    def _tracefunc(self, frame, event, arg):
        function_name = frame.f_code.co_name
        if function_name in self.escaped_functions:
            return
        parameters = frame.f_locals.values()
        parameters_string = ''.join(map(lambda p: '{}, '.format(p), 
                                       parameters))
        parameters_string = parameters_string[:-2]

        current_time = datetime.now().time().replace(microsecond=0)
{% endhighlight %}
This bit is pretty much self-explanatory so we'll move on to the interesting bit.


Now we have to handle function calls. And we do that by checking if the _event_ has a value of
*'call'*:

{% highlight python %}
def _tracefunc(self, frame, event, arg):
    function_name = frame.f_code.co_name
    if function_name in self.escaped_functions:
        return
    parameters = frame.f_locals.values()
    parameters_string = ''.join(map(lambda p: '{}, '.format(p), 
                                   parameters))
    parameters_string = parameters_string[:-2]

    current_time = datetime.now().time().replace(microsecond=0)
    
    if event == 'call':
        self.indent += 1
        if self.indent <= self.max_indent or self.max_indent == 0:
            log_line = self.indent * '  |'
            log_line = log_line[:-3] + '\\ \n'
            self.logfile.write(log_line)
            log_line = log_line[:-3] 
            log_line += ' ({}) [{}] {}({}) \n'.format(self.indent, 
                                                current_time,
                                                function_name,
                                                parameters_string)
            self.time_stack.append(current_time)
            self.logfile.write(log_line)
{% endhighlight %}

In this bit we make a branch of the tree when a new call occurs and 
write the indent level, the name of the function, the arguments that are given to it and the current time.
But if we leave it like that it will go deeper in the tree and the branches won't go back
to their ancestors. So we have to handle *returns.*

{% highlight python %}
if event == 'return':
    if self.indent <= self.max_indent or \
                            self.max_indent == 0:
        started_time = self.time_stack.pop()
        time_delta = datetime.combine(date.today(), 
                                    current_time) - \
                datetime.combine(date.today(), started_time)
        log_line = '  |' * self.indent
        self.logfile.write(log_line + '\n')    
        log_line = '  |' * self.indent
        log_line = log_line[:-2]
        log_line +=  '({}) [{}] \n'.format(self.indent,
                                             time_delta)
        self.logfile.write(log_line)    
    self.indent -= 1
{% endhighlight %}
Now if a function returns we decrease the indent and write it's indent level and 
the time it took to complete.


But one more thing... We need to handle exceptions. When something goes wrong
all hell will break loose in the logger so we'll have to stop it from logging
the exception handling python is doing. And for all of it to work we'll
have to return the function itself.

{% highlight python %}
elif event == 'exception':
    exception_string = '{0} Exception at {1}({2}) {0} \n'.format(
                                                10*'#',
                                                function_name,
                                                parameters_string)
    self.logfile.write(exception_string)
    self.logfile.write('Stopping logging... \n')
    self.stop_logging()

return self._tracefunc
{% endhighlight %}

Now we are ready to start logging. Our initial program with logging enabled
would look like this: 
{% highlight python %}
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)

if __name__ == '__main__':
    p = PrettyLogger()
    p.start_logging()
    fibonacci(3)
{% endhighlight %}


####Download:
You can download the full PrettyLogger with the *docstrings* (always write docstrings!)
here: [prettylogger.py](https://www.dropbox.com/s/w36dlivaqbmk07q/prettylogger.py)