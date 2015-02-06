---
layout: post
categories: javascript es6
tags: javascript harmony es6
title: Understanding ES6 classes
---
Today in a talk with a friend about what exactly ES6 classes are, I found that this can be commonly misunderstood.

First of all, if javascript is your first language and you have no prior experience with more truly object oriented languages with classes, instances, inheritance and all those things, there's a bunch of great sites you can google to find that; but hey! don't do that just for now, you're better if you don't know about that just yet.

If you are like me, and have experience working with languages that commonly use classes and modules for code organization, then ES6 classes might not be what you think they are. Why? Because under the hood, Harmony classes are just a skin in code of EcmaScript prototype functionality. So all those hacks you probably did when you tried to work with classes in javascript are essentially what you are doing when using the new *class* keyword in EC6.

## Is this bad?

No, not at all, you just need to know how this works, know how to use it, and the most important thing is, know you're not working with JAVA classes.

Is also good to mention, for all those haters that now say that people are screwing javascript by including classes, that we don't need them and a bunch of negative comments about it, THIS MAKES NO SENSE, again, this is just a skin of the prototype features javascript already has.

Let's get back a little bit and forget we have this new *class* keyword, and review how prototype works.

*Disclaimer*: I use the spanish words casa (house), casita (little house), perro (dog), perrito (puppy) for test variables instead of boring foo/bar hello world. Don't get confuse by them :)

So let's suppose we have the function Perro, and we want to define it some methods and properties, like number of legs, actions like walk and bark. We have 3 ways of do it:

- Define the things for the function ([static properties](#static-properties))
- Define the things for the instance ([instance properties](#instance-properties))
- Define the things as a template for further instances ([prototype properties](#prototype-properties))

### Static Properties

A static property/method will only be available for the creator function, and will not be included in the instance. This can work as a helper method for the function you're creating, or properties that are not related with the instance at all.

{% highlight javascript linenos %}
function Perro () {}
Perro.definition = "The dog is the man's best friend";
{% endhighlight %}

In this case, the definition will only be accessible by calling `Perro.definition`. No matter if you instantiate this function, it will not be transmitted to the instance. So this is a good place to put methods and properties related to the factory function, but not the children.

### Instance Properties

An instance property/method is the one that will be copied to all the instances of this function. They will be independent from the definition and other instances, this is your private are and here you do what you want for your unique instance.

It can be defined in 2 ways, from inside an instance method using the `this` keyword, or from the instance name.

{% highlight javascript linenos %}
function Perro (props) {
  this.color = props.color;
}

var max = new Perro({color: 'brown'});
max.name = "Max";
{% endhighlight %}

Now max has its unique properties like color and name, and even when you create new instances of this function, max will still be brown. Easy right?

### Prototype Properties

This is what makes javascript fun and different, Prototype (well, one of the things actually). So this is our solution to the lack of classes, this is how we get inheritance to work.

{% highlight javascript linenos %}
function Perro () {}
Perro.prototype.bark = function () {};
Perro.prototype.walk = function () {};

// Instance the variables
var max = new Perro();
var toby = new Perro();

// Compare functions
max.bark === toby.bark; // true
max.walk === toby.walk; // true

max.hasOwnProperty("walk"); // false

max.walk = function () {};
max.walk === toby.walk; // false, max.walk is an instance property now
Object.getPrototypeOf(max).walk === toby.walk; // true
max.__proto__.walk === toby.walk; // true, but be careful

// Define properties in future, still work on instances
Perro.prototype.eat = function () {};
max.eat === toby.eat; // true
{% endhighlight %}

As you can see, prototype is very flexible, it's not copied to the instances of the function, it's just a reference. If you try to ask with hasOwnProperty the existence of any prototyped property (or method) it will return false, because that property is not part of the instance, is part of its prototype.

You can however, create instance properties with the same name of the prototyped property. This will not erase the prototyped property, but it will be not accessible in the same direct way. When you do that, the instance property will be the direct access, but you can still access the prototype using the `__proto__`. Please notice that I used `Object.getPrototypeOf`, this is the secure method in ES5 because the use of __proto__ was normalized just until ES6, so you can get weired behaviors, see [MDN - __proto__](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/proto).

You can also define prototype properties in the future, after creating the instances. Since it's just a references to the `__proto__` it will be accessible in all the instances already created from this class.

### ES6 Classes

Ok so now we're back to ES6 Harmony classes, with a much more clear vision on how prototype works. And you will see there's no magic in here, is the same thing that we used in prototype.

{% highlight javascript linenos %}
class Perro {
  constructor (props) {
    this.color = props.color
  }

  walk () {

  }

  static definition () {
    return "The dog is the man's best friend";
  }
}

var max = new Perro({color: "brown"});
max.name = "Max";

var toby = new Perro({color: "white"});

max.walk === toby.walk; // true
Perro.definition(); // "The dog is the man's best friend"
{% endhighlight %}

I'm expecting at this point you are being able to see how this is working under the hood. I have some extra info to provide.

Sadly the class structure can only contains methods, not properties, so if you see the definition, I replace it with a method, because you can have properties definition in the prototype in the class structure, take a look at [this question](http://stackoverflow.com/a/22986568/1450411).

## Why not continue using prototype instead?

You still can do it, but using the class structure has some benefits. When using prototype if you want to do inheritance from another function, you'll start doing hacks to clone the prototype of one function into another one, and that's a complete mess, I don't recommend it.

{% highlight javascript linenos %}
class Perro {
  constructor (props) {
    this.color = props.color;
  }

  bark () {
    alert("woof");
  }
}

class Perrito extends Perro {
  constructor (props) {
    super(props);
  }

  bark () {
    super();
    alert("wouf");
  }
}

var puppy = new Perrito({color: "brown"});
puppy.bark();
{% endhighlight %}

We have a nice feature that we used to use a hack. `super()` will call the inherited method (constructor or any other polymorphic method).

Now you have the basics to start creating classes in Harmony knowing what you're doing. But hey, don't start using ES6 classes as Java classes, they are not the same, javascript is a completely different language and use different design patterns, all this helpers are just that, helpers, not a must to for all your code.
