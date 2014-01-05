---
title: The Lesser-Known Contributors Who Are Trying To Make PHP Good Again
layout: page
alias: /blog/the-lesser-known-contributors-who-are-trying-to-ma/
---

Php gets a log of hate, and a lot of that hate is [not](http://php.net/manual/en/types.comparisons.php) [without](http://me.veekun.com/blog/2012/04/09/php-a-fractal-of-bad-design/) [merit](http://www.codinghorror.com/blog/2012/06/the-php-singularity.html).  But I have recently started adopting some tools at work that make php development a lot more enjoyable, and thought I would share.

#### 1) Composer:

Composer is a pretty great package management system in the style of npm.  It provides package management at the project level by providing a autoloader that is compiled on package install.  This allows developers the ability to have multiple package versions installed on one machine and have them mapped to the projects they are specified for.

Furthermore, composer allows package maintainers a fresh start from the mess that is the Pear package manager.

A projects dependencies can be set up simply by including a composer.phar file and setting up a composer.json file like so:

{% highlight json  %}
  {
    "name": "My-New-Project",

    "require-prod": {
        "doctrine/orm": "2.2.1"
    },
    "require-dev": {
        "doctrine/orm": "2.*"

    "require":{
        "facebook-sdk": "master"
    },

    "repositories": {
        "package": {
            "name": "facebook-sdk",
            "version": "master",
            "source": {
                "url": "https://github.com/facebook/php-sdk.git",
                "type": "git",
                "reference": "master"
        }
    },

  }
{% endhighlight %}

#### 2) Phake:

One of the major reasons that ruby and python have become tremendously popular over the past five to ten years is because they provide very easy paths to test drive and maintain high coverage of your code base.  The same cannot be said for PHP and its PhpUnit framework.

Phake is a mocking framework that aims to fix a lot of the problems that PhpUnit’s mocking framework introduces.

Simple mocking and stubbing in Phake has far less unnecessarily verbosity.  Where a mock in PhpUnit requires this:

{% highlight php  %}
  <?php
  $mock = $this->getMockBuilder('MyClass')
      ->disableOriginalConstructor()
      ->setMethods(array('some_method'))
      ->getMock();

  $mock->expects($this->any())
      ->method('some_method')
      ->with('some_value')
      ->will($this->returnValue('something'));
  ?>
{% endhighlight %}

Phake only requires this:

{% highlight php  %}
  <?php
  $mock = Phake::mock('MyClass');
  Phake::when($mock)
    ->someMethod('expected value')
    ->thenReturn('something');
  ?>
{% endhighlight %}

Additionally, Phake provides a more sane solution to running verifies.  In phpunit’s verify system you are required to state your verify before you state the code you’re testing.  This makes the test much harder to read.

{% highlight php  %}
  <?php
  $mock->expects($this->any())
    ->method('some_method')
    ->with('some_value')
    ->will($this->returnValue('something'));

  $tested_class = new TestedClass();
  $tested_class->setMock($mock);
  $tested_class->methodThatCallsMockedMethod('some_value');
  ?>
{% endhighlight %}

In Phake you are able to verify after your mock is injected like so:

{% highlight php  %}
  <?php
  $mock = Phake::mock('MyClass');
  $mock->someMethod('some_value');

  $tested_class = new TestedClass();
  $tested_class->setMock($mock);

  //verify after calling the code being tested.
  Phake::verify($mock)->someMethod('some_value');
  ?>
{% endhighlight %}

#### 3) Php 5.4 Additions:

There are several additions that have been made to php in 5.4 that provide tools that I am use to having access to in other web development environments.  Namely, short syntax arrays, method chaining directly from an instantiated object and building a web server on the fly.  Try some of these commands out:

{% highlight php  %}
  <?php
  //new array syntax
  $my_array = [100, 'a', 299, 'hello', 300, 'world'];

  //chain off object instantiation
  $my_class = new MyClass()->someMethod()->someOtherMethod();

  //instantiate a webserver from the command line
  php -S localhost:8000

  //instantiate a webserver with webroot defined
  php -S localhost:8000 -t /path/to/your/files
  ?>
{% endhighlight %}
Php still has its faults and there is likely little that can be done to erase them.  But the developers of these new tools deserve some serious recognition for the work they are doing to move a language they love in the right direction.

