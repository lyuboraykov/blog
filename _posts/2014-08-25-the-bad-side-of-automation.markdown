---
layout: post
title:  "The Bad Side Of Automation"
date:   2014-08-25 17:22/l05
categories: bash, devops
---

I *love* automation. 
I mean it. Not just in production where it is mandatory (you can't run the *continuous integration*
tests *by hand*, can you?), but during development too, hack I do it even when I have to download a song.
There is nothing better than making the computer
orchestrate all of your work (well ice cream might be better) and I don't think
I'm the only one that prefers writing a good script
to get the job done instead of doing it myself.
Just make a confession:
* If you have to click 150 links in a website will you click them or will you *make python do it*?
* You have to deploy an application server - will you follow the instructions or look in (Dockerhub)[https://hub.docker.com/]?
* You need a CentOS VM - will you download the iso and install it yourself or *look for an OVF*?
* You have to donwload songs for your friend's car audio - will you download the by hand or get them from youtube with a script?

How can you match the feeling when you're doing a `docker pull` 
instead of downloading the source for an application, building it,
configuring the database and then seeing it not working because you had to add
a rule to iptables?

But there is one bad side to so much automation - **the waiting**.
I can't count the times when I've opened 4 shells, typed in some commands and then
started staring out of the window. Have you ever wondered why (Hackernews)[https://news.ycombinator.com/]
has so many Devops and infrastructure related articles, because Puppet and Ansible can do the work
while the guys are blogging (now you know *why* I started the blog...)