---
title: A Quick Rust tip (Comparing Strings)
layout: page
published: true
---

I have been learning [rust](https://www.rust-lang.org/en-US/) recently and so wanted to start tracking things that I've learned.

You can learn a lot more about strings in rust via [this explainer](http://www.steveklabnik.com/rust-issue-17340/) but one thing I got hung up on was comparing strings.

There are two types of strings in rust, `&str` and `String`.  It seems that rust is pretty good about deciding which type you want a `"some string"` to be, but I was having trouble doing something like this:

{% highlight rust %}
    let mut guess = String::new();

    io::stdin().read_line(&mut guess).expect("Failed to read line");
    
    if guess == "Q" {
        println!("Goodbye");
        break;
    }
{% endhighlight %}

I was worried that while `guess` was a `String` `"Q"` was a `&str`.  Turns out the issue was with the way the `read_line` formats the string when reading it.  By default there is a newline character at the end of the string so you need to use `trim()`.

{% highlight rust %}
    let mut guess = String::new();

    io::stdin().read_line(&mut guess).expect("Failed to read line");
    
    if guess.trim() == "Q" {
        println!("Goodbye");
        break;
    }
{% endhighlight %}
