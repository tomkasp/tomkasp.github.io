---
layout: post
title: Javascript functions invocation types.
---

There are four ways how you can invoke functions in javascript. If you want to develop high quality code it's crucial to
understand all of them. I'm going to explain what are the differences between them and when to use them.


## Invocation as a function.

This is the basic way. Let's see the example:

{% highlight js %}

    //1. as a function
    function shinnyFunction(){
        return this;
    }

    var returnObj = shinnyFunction();
    if(returnObj === window){ //true
        console.log("this contex is window")
    }

{% endhighlight %}


The most important thing from this example is that when you invoke function this way function context is global window
context. When you refer to the <font color="#FE9A2E">this</font> inside such a function you refer directly to the window object.

## Invocation as a method

{% highlight js %}

    //2. as a method

    var object = {
        runTheWorld: function(){
            return this;
        },

        saveTheWorld: function(){
            return this;
        }
    }

    returnObj = object.runTheWorld();

    if(object === returnObj){
        console.log("context returned from function invoked as a method is an object which function is invoked on")
    }

{% endhighlight %}


This time first we need to create javascript object with at least one function inside. Next type object reference and run
function as in first example but on our object. The bigest difference between first and second execution type is
with function context. In second example <font color="#FE9A2E">this</font>  is our object. In different way - everything within { } is our context
(our context is <font color="#FE9A2E">this</font> )
Thanks to above javascript can be used as an object oriented language.

## Invocation as a constructor

To invoke function as a constructor you just need to run your function with new keyword

See this example:

{% highlight js %}

    //3. as a Constructor
    function Ninja(){
        this.sword = function(){
            return this;
        }
        this.black = function(){
            console.log("My context is: " + this);
        }
    }

    var ninja = new Ninja();

    console.log(ninja)
    console.log(ninja === ninja.sword()); //true


    //When you forget to run our function as a constructor context
    //of function references to the window object.

    //running as a function
    ninja = Ninja();

    //sword property is assigned to the window
    window.black()

    function HandicapNinja(){
        return this;
    }
    var handicapNinja = HandicapNinja();

    console.log(handicapNinja === window); //true

    //Use this type of function invocation only when you want to create and init new object.

{% endhighlight %}

Why it's different than other types? Guess what - again difference is with context of our function. When you
invoke it as a constructor few things are happening:
- Empty object is created
- Our Empty object is passed to our function as a <font color="#FE9A2E">this</font> and becomes function context
- in case of lack return statement <font color="#FE9A2E">this</font> is returned

You can see all examples here: [my github repository](https://github.com/tomkasp/javascript)


Last method is with invoking apply() and call() methods and I'm going to cover it in separate blog post

