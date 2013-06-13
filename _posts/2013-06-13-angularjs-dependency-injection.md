---
layout: post
categories: javascript angularjs
tags: js angularjs dependency
---
A feature commonly unknow by the new users in [AngularJS](http://angularjs.org/) is the dependency injection, how does it works and most important, how many ways do I have to use it.

### [&para;](#param_injection)Param Injection

The first injection method you learn using angular is the one based on the function parameters, this is the easiest one because you don't have to do too much things, but be careful, this is not the best method.

{% highlight javascript linenos %}
function MyFormCtrl ($scope, $location) {
    $scope.casa = 'casita';
}
{% endhighlight %}

What Angular is going to do here is to read the name of the parameters you are requesting for, and if those names are already registered as services, it will call your function when it's invoked with those parameters. So this basically says, that you can request services to Angular in any order, just writting the right name. Cool right?

Ok, the magic behind this is provided by javascript, since in javascript you can convert everything into a string, they simply do a `MyFormCtrl.toString()` and that's going to return `function MyFormCtrl($scope, $location) { ... }` and with a simple regex, they'll read the parameters.

There are some problems with this, if you minify the script and that change the name of the parameters, lets say `MyFormCtrl(a,b)`, Angular is not going to find the service *a* and *b*, so you can use one of those 2 methods to use the dependency injection and minify your script happily.

### [&para;](#the_inject_property)The $inject property

This method is what Angular uses behind the scenes in the [Param Injection](#param_injection) but statically.

The trick in here is easy, Anngular find your function, it's going to search for a **$inject** property in the function, if it does exists, it's going to read the dependencies from there and call your function with those services (if they exist), and if it doesn't find the propery, it's going to create it using the `toString` method as descibed above. So you can have something like this:

{% highlight javascript linenos %}
function MyFormCtrl (scope, location) {
    scope.casa = 'casita';
}

MyFormCtrl.$inject = ['$scope', '$location'];
{% endhighlight %}

Note, that I'm changing the name of the parameters to scope and locations, insted of $scope and $location, because right now, it doesn't matters what is the name of the parameters, those are already annotated in the $inject property.

### [&para;](#array_syntax)Array Syntax

The last method to use the dependency injection, is using an array as the value instead of the method directly. Consider this case:

{% highlight javascript linenos %}
var MyFormCtrl = [
    '$scope', 
    '$location', 
    function MyFormCtrl (scope, location) {
        scope.casa = 'casita';
    }
];
{% endhighlight %}

Angular is going to determinate if the value is a function or an array, if it's an array, it's going to read all the indexes minus one (the last one) and use that as the dependency names, and use the last one as the function to call.

This is the one that I commonly see because you can use it in a single statement, but both are correct.

--------

As a tip, and if you use [grunt](http://gruntjs.com/) as your development task runner, you can also check the [ngmin](https://github.com/btford/grunt-ngmin) plugin for grunt, and configure it to annotate all your dependencies in the build task, so you don't have to worry about that in the development process.