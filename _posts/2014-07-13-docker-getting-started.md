---
layout: post
title:  "Getting Started with Docker"
date:   2014-07-13 23:36:51
categories: jekyll
---

## Introduction

At my company, we've been looking into using Docker. This would solve some deployment issues some of our teams have, such as deploying applications that aren't part of our standard technology stack.

## Benefits of Docker
There are many potential benefits with Docker:
* Container-based deployments that run anywhere Docker does. You build it on your machine, and it runs the same as it will on AWS.
* You can put anything in a container: Use Java 8, run a JAR file, plus pretty much anything you can script and programmatically configure.
* Docker containers can be deployed in many different environments, across many service providers. They can be deployed internally or on a variety of different PaaS providers. All the big names (Microsoft, Amazon, Google) are building support for Docker containers into their clouds.
* You can reach a model where you're running multiple Docker containers on a single machine to better utilize the capacity of the instances.

## What is Docker
See [http://www.docker.com/whatisdocker/](http://www.docker.com/whatisdocker/) for a very brief explanation about what Docker is. See [https://docs.docker.com/introduction/understanding-docker/](https://docs.docker.com/introduction/understanding-docker/) for a much more detailed explanation about the Docker ecosystem. Docker is extremely new, so there are no books on Docker yet.

Docker released version 1.0 on June 9, 2014. In this release, they declare Docker to be production-ready for enterprises.

## Installing Docker
Docker's website has lots of different guides for installing Docker on various systems. However, most of these rely on packages that are out-of-date. For example, the Ubuntu Docker package is version 0.9, several versions older than the current release of 1.0.

Therefore, for Linux hosts I recommend installing the Debian package provided in the Docker Apt repository, or alternately you can install the binaries yourself.

### Installing Debian Package
To install the up-to-date Debian package, you must get it from Docker's own Apt repository:
{% highlight bash %}
#!/bin/bash
sudo sh -c "wget -qO- https://get.docker.io/gpg | apt-key add -"
sudo sh -c "echo deb http://get.docker.io/ubuntu docker main\ > /etc/apt/sources.list.d/docker.list"
sudo apt-get update
sudo apt-get install --assume-yes --force-yes lxc-docker
{% endhighlight %}

### Installing Binaries
You can also install the binaries yourself if you choose. Follow the binaries installation guide, which will get you the latest version of Docker on your Linux systems. There's also a guide to get it working on Windows, as well as a guide to get it working on a Mac, but I haven't tried either. Docker is completely Linux dependent, however, so installing on Windows or Mac means installing things like VirtualBox.

## Building a Docker Image
Docker images are built using a file called Dockerfile. The Dockerfile contains Docker instructions to create the image. As an initial test, I created a Dockerfile that just installs the Apache HTTP web server and lays down static HTML files in its WWW directory.  Here’s the Dockerfile:
{% highlight bash %}
FROM ubuntu:12.04

RUN apt-get update
RUN apt-get install -y apache2
RUN rm -rf /var/www/*

ADD . /var/www

RUN chown -R www-data:www-data /var/www

ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2

EXPOSE 80

CMD ["/usr/sbin/apache2", "-D", "FOREGROUND"]
{% endhighlight %}

Docker's website has lots of help for writing Dockerfiles. It has tutorials such as creating a MongoDB service in Docker. Additionally, it contains a complete Dockerfile reference for all the individual Dockerfile instructions.

Once you have a Dockerfile, you can build an image from it. I built my image using a command like the following:
{% highlight bash %}
# The -t option tags the image with a name. The convention is to use <username>/<imagename>
# The last option specifies where your Dockerfile is found. In this case, it was in my current working directory.
docker build -t dsw88/apachetest .
{% endhighlight %}

## Running a Docker Image Locally
To run a docker container from an image, I used a command like the following:
{% highlight bash %}
# The -d option specifies that the container will run as a background daemon
# The -p option maps a port on localhost to the port exposed by the docker continer.
# The last argument is the name of the image I want to use to run the container
docker run -d -p 43000:80 dsw88/apachetest
{% endhighlight %}

When the container runs, you can hit http://localhost:43000 to see the exposed service running inside your Docker container.

## Docker Hub
In addition to providing the open-source client used to generate and run Docker images, Docker provides a SaaS registry at hub.docker.com. On the hub, you can create repositories to store your images. The whole thing has a very GitHub-ish feel. You can create public and private repositories, and they have paid plans for more than one private repository.

Docker provides a registry at registry.hub.docker.com, which is a marketplace-type service where you can find Docker images for a variety of services. For example, there are Docker images available for PostgreSQL, Node.js, Nginx, and many thousands of others.
