---
layout: post
title:  "Docker cleanup commands on Windows using PowerShell"
date:   2015-05-20 20:00:00
categories: docker windows powershell
---

First off, I was really excited to get a native Windows Docker client a little
while ago! I hated using boot2docker on Windows before that, but now it's a
really nice experience.

Docker doesn't provide a great way to cleanup old containers and images on your machine. When you reassign a tag to a new image, the old image layers pointed to don't go away but instead have the name <none> assigned to them. These are effectively useless and need to be cleaned up from time to time.

On Linux these commands are fairly well known, but I couldn't find a Windows equivalent, so I made some.  Here they are:

#### Cleanup Old Docker containers
{% highlight powershell %}
docker ps -a | ForEach-Object {($_ -split '\s+')[0]} | ForEach-Object {docker rm $_}
{% endhighlight %}

#### Cleanup Old Docker Images
{% highlight powershell %}
docker images | Select-String -Pattern "<none>" | ForEach-Object {($_ -split '\s+')[2]} | ForEach-Object { docker rmi $_ }
{% endhighlight %}

You can save these as PowerShell functions so you don't need to go find them every time. Just put the function definitions in your PowerShell profile file at ~\Documents\WindowsPowerShell\Profile.ps1:
{% highlight powershell %}
function Remove-DockerOldContainers
{
  docker ps -a | ForEach-Object {($_ -split '\s+')[0]} | ForEach-Object {docker rm $_}
}
{% endhighlight %}

By the way, I think PowerShell is one of the best things to ever happen to Windows. It's very different than Bash, and I still prefer Bash, but I think PowerShell is just fantastic to use. The documentation it has blows Linux man pages out of the water in terms of readability.
