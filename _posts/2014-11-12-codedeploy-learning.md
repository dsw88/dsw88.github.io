---
layout: post
title:  "CodeDeploy Initial Evaluation"
date:   2014-11-12 10:30:00
categories: aws codedeploy PaaS
---

## Introduction
We use a lot of AWS at work, and CodeDeploy was just recently announced by AWS,
so I thought I'd take a look at how it compares to existing services like
Elastic Beanstalk and CloudFormation.

It looks like CodeDeploy falls right in between CloudFormation and Elastic Beanstalk. CloudFormation is extremely flexible and extremely painful to use directly. Elastic Beanstalk is extremely inflexible and extremely easy to use. CodeDeploy feels very much like a Heroku-style model where you script the way your application should be installed and configured (Heroku buildpacks), then you can re-use configuration over and over again on different applications.

## CodeDeploy Concepts

#### Deployment Groups
CodeDeploy deploys straight onto EC2 instances. It has the concept of a deployment group that contains EC2 instances. You can also have an auto-scale group inside your deployment group, or can even have a combination of bare EC2 instances and auto-scale groups. You can also group EC2 instances into a deployment group by tags. For example, you could have all EC2 instances with the tag "Name=MyDeployGroup" be inside a deployment group together.

CodeDeploy doesn't actually provision any hardware. It leaves it completely up to you to provision EC2 instances, auto-scale groups, ELBs, and anything else you need. It just deploys code onto your instances once you've got them up and running in whatever configuration you want. Here's the general workflow:

1. Provision resources such as EC2 instances, auto-scale groups, and ELBs. This is probably best done using a parameterized CloudFormation template.
  * The EC2 instances must have the CodeDeploy Agent installed on them. See the CodeDeploy Agent section below for more information.
2. Deploy your code onto the provisioned instances using the CodeDeploy service.

The first item on that list is probably a one-time action, or at the very least it happens infrequently. The second item happens often, every time you make a change.

#### CodeDeploy Agent
These EC2 instances must have the CodeDeploy agent installed on them in order for CodeDeploy to be able to interact with them. See [the AWS docs](http://docs.aws.amazon.com/codedeploy/latest/userguide/how-to-set-up-new-instance.html) for installing the agent on instances. This would be a good thing to embed in a base AMI so that most all EC2 instances have the CodeDeploy agent on them already.

#### AppSpec
You tell CodeDeploy how to deploy your application via an AppSpec configuration. This is a YAML file called appspec.yml, and it looks like this:
{% highlight yaml %}
version: 0.0
os: linux
files:
  - source: /
    destination: /var/www/html/WordPress
hooks:
  BeforeInstall:
    - location: scripts/install_dependencies.sh
      timeout: 300
      runas: root
  AfterInstall:
    - location: scripts/change_permissions.sh
      timeout: 300
      runas: root
  ApplicationStart:
    - location: scripts/start_server.sh
      timeout: 300
      runas: root
  ApplicationStop:
    - location: scripts/stop_server.sh
      timeout: 300
      runas: root
{% endhighlight %}

As you can see above, you can use this file to specify files to lay down on the host, as well as scripts to run at various phases in the deploy process. It's all very much up to you what you put in these scripts, giving you a large amount of flexibility.

#### Deployment Phases
There are various phases in deployments in CodeDeploy. You can execute scripts in any of these phases by configuring them in your AppSpec. Here's a list of phases from [the AWS docs](http://docs.aws.amazon.com/codedeploy/latest/userguide/app-spec-ref.html):

1. ApplicationStop – occurs when the previous revision of your application is being shut down on the instance. This event can be hooked in the AppSpec file
2. DownloadBundle – the AWS CodeDeploy Agent downloads the application revision to /opt/codedeploy-agent/deployment-root/deployment-group-id/deployment-id/deployment-archive on an Amazon Linux or Ubuntu Server instance and to C:\ProgramData\Amazon\CodeDeploy\deployment-group-id\deployment-id\deployment-archive on a Windows Server instance.
3. BeforeInstall – occurs before AWS CodeDeploy begins to install your revision to the instance. This will be run before any files that you specified in the files section are copied. This event can be hooked in the AppSpec file.
4. Install – the AWS CodeDeploy Agent copies the file to the destination specified in your AppSpec file on the instance.
5. AfterInstall – occurs just after AWS CodeDeploy has installed your application revision on the instance. This is run after the files that you specified in the files section of the AppSpec file have been copied. This event can be hooked in the AppSpec file.
6. ApplicationStart – occurs just before your application revision is started on the instance. This event can be hooked in the AppSpec file.
7. ValidateService – occurs after the service has been validated. This event can be hooked in the AppSpec file.

#### Application Code Sources
CodeDeploy can pull applications from both S3 and GitHub. I imagine that the GitHub option is only viable for interpreted languages like PHP or Node since with Java you have to compile your artifact. Once you've configured your AppSpec and uploaded your artifact, it's as simple as telling CodeDeploy to deploy that bundle to a deployment group you've already specified.

#### Deployment Options
You can specify how your deployments proceed in your deployment group:
* Deploy to all instances at once.
* Do a rolling deploy to one instance at a time.
* Deploy to half the instances, then the other half.

You can also configure when to consider a deploy "failed", such as after the first failure or only after all instance deploys fail.

#### Rollbacks
CodeDeploy doesn't really support rollbacks very well. It tries to remove the files it placed on there, but it only removes the files it directly placed. It doesn't remove anything that was installed via scripts, so there will likely be a polluted system when doing rollbacks.

## Docker
This isn't a Docker-as-a-service solution, but I think Docker can go really well with this. You can embed CodeDeploy and Docker on an AMI together, then have an extremely simple and fast deployment process.

## Parting Thoughts
I really like CodeDeploy. It is so much nicer than trying to use CloudFormation
and cfn-init to do deployments. But it's also much more flexible than Elastic
Beanstalk. I think if I had my choice at work, we'd start using a combination of
CloudFormation for provisioning resources, then CodeDeploy for deploying onto
our EC2 instances.
