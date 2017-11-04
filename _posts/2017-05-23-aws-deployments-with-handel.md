---
layout: post
title:  "AWS Deployments with Handel"
date:   2017-05-23 00:44:00
categories: aws deployments handel
---

It's been a while since I wrote in this blog! Much like a journal, my personal blog seems to get ignored for months (or years in this case) at a time.

# More AWS Work
I have a new job in the last few months, working at [Brigham Young University](https://www.byu.edu/).

One of the things I've been asked to do at BYU by my boss was figure out how to help our teams build and deploy their applications in AWS, which is where we're moving many of our services. People are currently doing a variety of deployment methods, ranging from manual deployments in the AWS Console to writing CloudFormation templates. 

Those that are using CloudFormation have been struggling with the difficulties of having to know tons about the IAM, VPC, and EC2 services in order to wire services up properly. I've heard that before. :) I'd say that one of the biggest barriers to cloud usage in general is the "data center layer" that developers aren't accustomed to thinking about.

At FamilySearch, we started using AWS about 4 years ago, and one of the goals was to make deployments easier for engineers. We created a build and deployment system called "Blueprints" that provided automated builds and deployments via a declarative spec file (the "Blueprint") that people would put in their repositories. The build system would then execute builds and deployments as the developers had specified.

I worked primarily on the build side of that, but also implemented quite a bit on the original deploy side too, so I got pretty familiar with CloudFormation and AWS Deployments in general.

# Handel
Blueprints at FamilySearch were nice to use, but they were a proprietary system that was built with many assumptions about how FamilySearch uses AWS. Their implementation also was too costly for BYU to support, since we didn't have nearly the number of engineers dedicated to support our AWS environments as they do.

So instead what I decided to do was port the concepts of Blueprints into a new package, which I named [Handel](http://handel.readthedocs.io/en/latest/). Why the name? Well, it's after this guy:

{% include youtubePlayer.html id="akb0kD7EHIk" %}

What better name for a deployment orchestration library than a fantastic composer? Also, the package name was available on NPM, so that influenced the choice a bit too... :)

# Handel Concepts
Handel behaves very similarly to Blueprints at FamilySearch. You put a file called *handel.yml* at the root of your project repository that contains the resources you want to deploy to AWS. Here's a simple example deploying a webapp to Elastic Beanstalk, using an S3 bucket for storage:

{% highlight yaml %}
version: 1

name: my-first-handel-app

environments:
  dev:
    webapp:
      type: beanstalk
      path_to_code: .
      solution_stack: 64bit Amazon Linux 2016.09 v4.0.1 running Node.js
      dependencies:
      - bucket
    bucket:
      type: s3
{% endhighlight %}

Handel uses CloudFormation templates under the hood to create the requested resources. 

The *dependencies* section in the example above is the special part of Handel. You use this section to specify which services need to communicate with each other. In the example above, you want your Beanstalk service to be able to read and write files from the S3 bucket. Handel then reads this dependency information to securely wire those services together for you using fine-grained privileges. 

This automatic security wiring greatly simplifies deployments. The authors of the Handel services go figure out that junk to wire services together once, then you don't need to figure out how to do them yourself when you go to deploy things.

# Handel-CodePipeline
Handel itself is just a CLI tool that you can run anywhere, either on your local laptop or on any build server. 

AWS provides a Continuous Delivery service (albeit a very basic one) called [CodePipeline](https://aws.amazon.com/codepipeline/). This service allows you to set up a pipeline for your project to automatically build and deploy to AWS when you commit your code. We are currently using this service at BYU, and have been mostly happy with it.

To facilitate easy setup of these pipelines using Handel, I wrote a companion tool called [Handel-CodePipeline](http://handel-codepipeline.readthedocs.io/en/latest/). This tool helps you set up pipelines using a declarative file called *handel-codepipeline.yml*. It provides many different phase types for build, test, and deployment options.

# More Info
[Handel's documentation](http://handel.readthedocs.io/en/latest/getting-started/tutorial-creating-an-app.html) is the best place to start for how to use this tool. You can go look at the [tutorial](http://handel.readthedocs.io/en/latest/getting-started/tutorial-creating-an-app.html) in the docs that walks you through deploying a simple "Hello World" app to AWS using Handel.

Handel supports quite a few AWS services, but does not yet support all of them. For most web-based projects, Handel should support most of the services you need. Handel has much less support for AWS services in the big data and analytics space.

Check out the source on GitHub for both [Handel](https://github.com/byu-oit/handel) and [Handel-CodePipeline](https://github.com/byu-oit/handel-codepipeline).