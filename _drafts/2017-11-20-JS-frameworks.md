---
layout: post
title:  "Comparing JS frameworks"
date:   2017-11-05 19:45:31 -0300
categories: [article]
tags: [ruby, ror, gems, log, logging, debug, assert]
author: Marcelo Foss
---
AngularJS
EmberJS
BackboneJS
React
http://natehunzaker.com/javascript/backbone/2011/02/11/learning-backbonejs-chapter-1-a-quick-overview.html
http://feras.dito.com.br/posts/criando-uma-aplicacao-com-angularjs-e-ruby-on-rails-parte-1/
http://andrewaustin.com/an-overview-of-angularjs-for-managers/

http://sporto.github.io/blog/2013/04/12/comparison-angular-backbone-can-ember/

# AngularJS
AngularJS is a JavaScript framework made by Google for building complex client-side applications. Angular’s killer feature is ‘directives’ that allow you to extend HTML by creating tags and attributes. Angular projects have a somewhat different structure than other JavaScript MVC frameworks, but it can be highly modular and easy to maintain once you understand the structure.

## AngularJS philosophy
### Data first
In spite of focusing on DOM manipulation, and then updating data based on that, when writing Angular, you will mostly be updating data, and AngularJS updates the DOM for you, with a tiny amount work on your end.

### Highly testable
AngularJS was designed to be testable end-to-end. It uses Dependency Injection to make mocking of objects simple. It is also designed to encourage you to break the functionality of your application into several smaller, more testable parts.

### Declarative HTML
Angular is designed to extend HTML to make it the language you need for building complex web applications. Adding custom tags and attributes allow you to write simple HTML tags that do very complicated things.

### Data Binding
AngularJS uses two-way data binding

### Modules
Modules are used to organize the objects in an AngularJS application. Modules can either be used as the ‘core’ of an application, and contain all of the classes used for it, or they can be used to group several objects that have similar functionality.

### Controllers
Controllers tie into particular HTML elements. They contain data and functions that the HTML can interact with, and can interact with other service objects, which can handle things such as communicating with the server.
Here we’ve used the ng-controller directive to bind our controller function to a div. Controllers get one argument by default which is called $scope.
$scopecontains all of the data that the HTML can interact with. When you reload the page, you will notice that the input now starts with the field filled in with ‘Default Name’, Because we set that variable in the controller and then the page loads that variable on load.

Functions can also be applied to $scope and then called inside the HTML. Angular contains several directives for handling various events.
Now that we have seen how we can manipulate data inside the JavaScript let’s look at how we can manipulate the DOM, by creating our own directives

### Directives
You have already seen directives in use in several places inside the application. You can also define your directives as part of your application. Let’s create a directive called alertable directive that will allow you to set a message to be alerted whenever you click on the element.

As you can see, directives return an object that will define the directive. There are several other optional arguments, but let’s look at the two we used here:

restrict: This tells your directive what kind of directive it will be. restrict is required and there are four possible arguments that can be passed to it:
E: Element. example usage: <my-directive></my-directive>
A: Attribute. example usage: <div my-directive></div>
C: Class. example usage: <div class='my-directive'></div>
M: Comment. example usage: <!-- directive:my-directive -->
link: The link function is responsible for adding event listeners and updating the dom.
You can read about some of the other possible options in the Angular directive documentation.

### Services
Services are classes that can either contain business logic or handle data. Now we are going to refactor our previous example and create a service that can handle the data in our list of people.

You’ll notice that services are created slightly differently than controllers. Controllers are just a function, where services are a function that returns an object. This allows you to add private methods to your service if you would like.

Now we need a way for our controller to have access to the service we just created. Since they are in the same module, this is very easy to do. This also involves one of the more “magical” features of Angular. To give the controller access to our service, we just have to add it as an argument to our controller, like so:

What’s really surprising is that if you change the order of the arguments to the controller function, it will not change how the controller function operates. So, if we did this instead:

Our function would behave in exactly the same way. This is because AngularJS looks at the names of the arguments of a function to decide what to pass to them. Now, you may be concerned that this could break if you were to minify your JavaScript, and you would be right. Luckily, Angular provides an alternate way of declaring functions to prevent issues when minifying your code. It involves passing an array of strings to tell angular what the arguments should be, with the actual function as the last element of the array. So now your controller would look like this:

When writing services, Angular provides some helpful modules for communicating with the server. Some of the most useful are ngHttp and ngResource. ngHttp is used to make HTTP requests. ngResource is an extension of ngHttp designed to work exclusively with REST APIs. This is another reason why when you are declaring a service, you are asked for a function that returns an object; We are able to use this function to extend an existing object like ngResource and return it.

### Routing
Angular also provides support for routing with URLs. Routing is performed by using the config function of the module. Let’s use this to structure separate pages for our main page, and give each person in our person list a profile page. Here is what the route provider will look like:

### Filters
Filters are a smaller, but often useful part of AngularJS. Filters change the format of data on the page. Like directives, you can build them yourself, and Angular also includes several useful ones built-in.
