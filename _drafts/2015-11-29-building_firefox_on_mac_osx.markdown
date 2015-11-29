---
title: Building Firefox on Mac OSX
layout: page
---
I was recently setting up a local build of the mozilla firefox browser and ran into a few issues that were not entirely straight forward to resolve, so I though I would run through them and how to resolve them for people who run into them in the future.

### Before you do anything else:
Your first step should be to install xcode and homebrew.

You can download and install xcode from (here)[https://developer.apple.com/xcode/]

Be sure to also install command line tools when installing xcode.

You can download and install homebrew by following the instructions  at [http://brew.sh/]
I recently discovered a really terrible feature now integrated into most modern browsers. The javascript window object is assumed appended to all javascript variables.

What does this mean?

A lot of developers, myself included until recently, probably do not know of this feature. Essentially assuming the window operator means that

{% highlight javascript %}
window.some_id_name
{% endhighlight %}

