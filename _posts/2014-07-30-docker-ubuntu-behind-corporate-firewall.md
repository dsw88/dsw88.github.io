---
layout: post
title:  "Using Docker in Ubuntu Behind a Corporate Firewall"
date:   2014-07-30 23:48:51
categories: docker ubuntu
---

Docker is awesome. I've been using it for a few months now, and it's just been great. I love how it's so lightweight, and how my configuration finally completely lives with my app.

I have run into some tough issues during my time experimenting with Docker, however. One such issue was when I tried to run Docker on an Ubuntu machine running behind a corporate firewall.

The Docker daemon installed successfully, and I wrote my first Dockerfile, which was very simple:

{% highlight bash %}
FROM ubuntu:14.04

RUN apt-get update && apt-get install -y wget
{% endhighlight %}

Building this Docker image didn't work. On the "RUN apt-get update..." command, it hung for a very long time when trying to fetch the package indexes. Eventually it timed out. This was very confusing because it worked fine running an apt-get update on my host OS.

Searching the internet helped me find the problem: In Ubuntu, the /etc/resolv.conf is auto-generated, and specifies your DNS servers as 127.0.0.1. Docker had problems with Ubuntu because of this, so they put in a patch to Docker that uses Google's nameservers when you try to build a container on Ubuntu.

I was sitting behind a corporate firewall, so Google's nameservers were not accessible. Instead, I wanted to somehow let the docker image build know about our corporate DNS servers so the "apt-get update" could work. Unfortunately the "docker build" command doesn't allow you to specify which DNS servers to use. Other commands like "docker run" do allow for that, but "docker build" does not.

So, after a bit of searching I found my answer: You can specify in the Docker daemon which DNS servers you want to use:

{% highlight bash %}
sudo docker -d -D -dns myDnsIp1 -dns myDnsIp2
{% endhighlight %}
