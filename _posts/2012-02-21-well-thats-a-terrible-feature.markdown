---
title: Well That's a Terrible Feature
layout: page
---
I recently discovered a really terrible feature now integrated into most modern browsers. The javascript window object is assumed appended to all javascript variables.

What does this mean?

A lot of developers, myself included until recently, probably do not know of this feature. Essentially assuming the window operator means that

{% highlight javascript %}
window.some_id_name
{% endhighlight %}
can be shorted to just

{% highlight javascript %}
some_id_name
{% endhighlight %}
to get any html DOM object. Go ahead, open up Chrome and type 'test' in the javascript console.  You just got the DOM object of this paragraph.  Seem pretty nifty right?

It’s a great feature until you run into an error like the one that led me to discover this. The problem here is that this means that any DOM id you use on a page is now a protected object in javascript (but for some edge cases that I still haven’t fully worked out).

I discovered this feature when I tried doing this:

{% highlight html %}
<input onclick="’comment_reason(“$info[‘comment’]”)’/" type="’button’">
<input id="’comment_reason’/" type="’hidden’">
{% endhighlight %}
and

{% highlight javascript %}
function comment_reason(id){}
{% endhighlight %}
I know real daring of me

When run in chrome this trigger gives “Uncaught Type Error: object is not a function”. In firefox it gives the slightly more helpful “function not defined”. After working the error back to the trigger I talked to some other people.  Most were as flummoxed as I.  That is, until I found someone who had run into the same error and pointed me to the source of the problem.

The comment\_reason namespace was already occupied.  It was occupied by the comment\_reason object or rather the `window.comment_reason` object that was instantiated when the dom was loaded.

So is this a feature or a bug?

I say that the bugs this causes far out weigh the feature of not having to write window. Further limiting dom naming and imparting those naming restrictions on javascript seems completely unnecessary.

Unfortunately this doesn’t seem to be going away. [This stackoverflow entry](http://stackoverflow.com/questions/1415747/javascript-function-and-form-name-conflict) explains what’s going on somewhat, and it would seem that this has been adopted as a core feature of html5. So for better or for worse it’s here to stay.

If anyone can give a more full explanation of why this is a worthwhile feature I'm all ears.
