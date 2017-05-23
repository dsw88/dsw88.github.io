---
layout: post
title:  "Hello World!"
date:   2014-06-29 23:36:51
categories: jekyll
---

I got sick of managing a WordPress blog. It was just too big for what I wanted: A place to put my blog posts on programming-related topics. I've played around with static-site generators, including Jekyll, Octopress, and Pelican. Based on my exhaustive 5-minute survey, I've decided to go with Jekyll.

Why, you ask? Because it was the easiest to set up of the three. We'll see if I decide to go somewhere else once I have a bit of experience with Jekyll.

For now, however, I'm really liking Jekyll! It's got some great things that I like:

* Easy way to build the static site: jekyll build. It all gets built to a single folder, so I can easily verify the output.
* Good support for code snippets, which is a must for a programming blog.
* It's Ruby, and I love Ruby. I love Python too, though. :)


Speaking of the code snippets above, here's a cool example of a code snippet:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}
