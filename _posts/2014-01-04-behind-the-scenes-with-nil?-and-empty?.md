---
title: Behind the Scenes with nil? and empty?
layout: page
---

At their base level the ruby methods nil? and empty? are just contents checks with slightly different focuses.  But I thought it would be interesting and maybe even useful to look at how each actually opperates.

#### Why are you writing this

Mostly just for shits and giggles, and because I was currious about the core implementations of these methods.

#### nil?

First, let's take a look at `nil?`.  It is the most simplistic of the three methods and is easy to understand at a source code level.  First we should know what the method does.  Calling `nil?` simply checks the object is is called on and returns whether it is of the nil object type or not.

{% highlight ruby %}
  nil.nil? # => true
  "".nil? # => false
  0.nil? # => false
  [].nil? # => false
  {}.nil? # => false
  NilClass.nil? # => false
{% endhighlight %}

If you accept that `nil?` will only return true if it's owner is nil, this everything in this result set should make sense.  The only thing that might give you pause is `NilClass.nil? # => false`, but this should be quickly cleared up by seeing that NilClass is the class of the singleton object nil in ruby.  This simply means that NilClass can only instantiate to the instance nil, and is only instantiated through the keyword `nil`.

To implement this functionality, ruby does four things in its source:

{% highlight c %}
  // 1) define a function to return the ruby object false
  static VALUE
  rb_false(VALUE obj)
  {
    return Qfalse;
  }

  // 2) define a function to return the ruby object true
  static VALUE
  rb_true(VALUE obj)
  {
    return Qtrue;
  }

  // 3) define the Object class and assign functionality to the nil? method
  rb_mKernel = rb_define_module("Kernel");
  rb_define_method(rb_mKernel, "nil?", rb_false, 0);

  // 4) define the NilClass and assign functionality to the nil? method
  rb_cNilClass = rb_define_class("NilClass", rb_cObject);
  rb_define_method(rb_cNilClass, "nil?", rb_true, 0);
{% endhighlight %}

Most of this is pretty straight forward but there are a few things to make note of.  `VALUE` is a pointer to an object, so it's use in this code is simply stating that both methods take in an object and return an object.  More specifically for the `rb_true` and `rb_false` methods they take take in an instance of the base ruby Object class and return an either an instance of TrueClass or FalseClass.

`Qfalse` and `Qtrue` are instances of  `VALUE` and represent the singleton objects of the ruby classes TrueClass and FalseClass.  This is significant only in that it is useful to note that ruby's true and false are not the same thing as c's true and false, although they can be converted.

Next, you'll probably note the strange method `rb_define_module`.  This function creates a module that is used in this case by the Object class which is defined by `rb_cObject = rb_define_class("Object", rb_cBasicObject);`.  Methods that are core to the Object class and don't extend to other classes are defined directly on the object class where as methods that do extend are defined on the kernal module.

Lastly `rb_define_method` takes an class definition, a stringified name for the method being defined, a function pointer to deliver it's returned value and the number of arguments expected to be passed to the method.

#### empty?

`empty?` is a core ruby method, but it is only implemented on certain ruby object types. It is not implemented on the ruby ObjectClass and then extended to all other clases.  Instead it is implemented only on classes that can have a lenght or count.  First a few examples.

{% highlight ruby %}
[].empty? # => true
[1].empty? # => false
{}.empty? # => true
{ a: 1 }.empty? # => false
"".empty? # => true
"hello".empty? # => false
:hello.empty? # => false
:"".empty? # => true
{% endhighlight %}

Again, going through how these results are generated will give a deeper understanding of what is actually happening when we make even these basic method calls.

First, let's look at the source codes for the `empty?` methods.
{% highlight c %}
// String#empty?
static VALUE
rb_str_empty(VALUE str)
{
  if (RSTRING_LEN(str) == 0)
    return Qtrue;
  return Qfalse;
}
//...
rb_define_method(rb_cString, "empty?", rb_str_empty, 0);
{% endhighlight %}

This looks relatively familiar to what we saw before with the `nil?` definition.  There's a little more logic but it's really not too hard to work through.

First we see that the function takes in a `VALUE` pointer object and returns a `VALUE` pointer object.  In this case it takes in a pointer to an object that is expected to be a string and returns a the `Qtrue` or `Qfalse` `VALUE` instances.  When we look at the logic inside the function we can see that is does exactly what we'd expect it to do.  It checks the string's length using `RSTRING_LEN`, a custom string length method implemented for ruby, and return `Qtrue` if that length is 0.

{% highlight c %}
// Array#empty?
static VALUE
rb_ary_empty_p(VALUE ary)
{
  if (RARRAY_LEN(ary) == 0)
    return Qtrue;
  return Qfalse;
}
//...
rb_define_method(rb_cArray, "empty?", rb_ary_empty_p, 0);
{% endhighlight %}

Next, looking at the array empty method we see very similar code.  Again, the function takes in and returns `VALUE` and again it uses a custom string length function, this time `RARRAY_LEN` to check the length of the object. Again, `RARRAY_LEN` is simply a wrapper for getting the memory allocated for the array in question.  It is worth noting that an empty array in ruby does not coorespond to an empty array in C.

{% highlight c %}
// Hash#empty?
static VALUE
rb_hash_empty_p(VALUE hash)
{
  return RHASH_EMPTY_P(hash) ? Qtrue : Qfalse;
}
//...
rb_define_method(rb_cHash,"empty?", rb_hash_empty_p, 0);
{% endhighlight %}

Again, in the hash version of the function everything looks very similar.  However, there is a small diference.  This time we aren't checking that the hash has no length, instead we are actually checking that it is empty.  This is simply because it is more expensive to get the length of a hash than it is to get the length of an array.  With an array you can simply check how much memory is allocated, where as with a hash you have to actually count the number of entities.

{% highlight c %}
Looking at
// Symbol#empty?
static VALUE
sym_empty(VALUE sym)
{
  return rb_str_empty(rb_id2str(SYM2ID(sym)));
}
//...
rb_define_method(rb_cSymbol, "empty?", sym_empty, 0);
{% endhighlight %}

Finally, looking at the function for `Symbol#empty?` we start to see why it works the way it does.  The short answer is that it simply casts the symbol to a string before checking whether it is empty.  But this has a few implecations to it.  First casting a symbol to a string atomatically increases the memory allocation for the variable.  It's also worth noting that casting a symbol actually requires a chain of function calls.  First the symbol's value must be retrieved from the global symbol table.  This is what `SYM2ID` is doing.  Next that value is cast to a string, and finally the strings memory allocations is counted.

In the grand scheme of things none of this is particularly costly, but it is enlightening to see what is actually going on behind the scenes.




