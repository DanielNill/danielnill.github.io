---
title: The differences between blank?, nil? and empty?
layout: page
---

At their base level the ruby methods nil? and empty? and the rails method blank? are just contents checks each with a slightly different focus.  But as you look deeper into how each one opporates it becomes apparent that each method is segnificanly different both in intent and in performance.

#### The Basics

First lets take a quick look at how each method opporates.  `blank?` is a content check implemented by ruby on rails and returns values for all types.

{% highlight ruby %}
  nil.blank?  #=> true
  false.blank? #=> true
  true.blank? #=> false
  "".blank? #=> true
  "hello world".blank? #=> false
  5.blank? #=> false
  [].blank? #=> true
  [1].blank? #=> false
  {}.blank? #=> true
  {key: 'value'}.blank? #=> false
  # and so on and so on
{% endhighlight %}

If we take a look at the source code for `blank?` it becomes quickly apparent why it was added, and why it behaves the way it does

{% highlight ruby %}
  def blank?
    respond_to?(:empty?) ? empty? : !self
  end
{% endhighlight %}

So all that `blank?` is really doing is calling the ruby method `empty?` if it is available and otherwise casting the object in question to a boolean type and returning the inverse of it.  This means that `blank?` exists simply to extend `empty?` to all ruby objects.  Specifically, to extend it to `nil`, `true` and `false`.

Knowing that `blank?` is extending `empty?` means we would be best to next examin what `empty?` does.  `empty?` is a core ruby method, but it is only implemented on certain ruby object types. And as we just learned, it returns the same boolean values as `blank?` but only for a subset of types.  If we look at the source code for the implementation of `empty?` on the array object we can start to see what's going on under the hood.

{% highlight c %}
// put source for Array#empty? here
{% endhighlight %}
