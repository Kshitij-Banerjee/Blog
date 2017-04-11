---
layout: post
title:  "A cheat sheet to get the angular basics in 10 minutes"
description: "Code explains best. No Long descriptions. The basics, the important stuff laid out."
name: "Code explains best. No Long descriptions. The basics. The important stuff laid out."
author: Kshitij Banerjee
avatar: "img/authors/K_icon.png"
img: "angular_js.png"
date: 2017-04-10
color: bfbf40
tags: Tutorial, AngularJS
typej: Tutorial, AngularJS
---

## Angular JS
- - - - - - - - -

### How the view looks - Template - View

~~~~ html
<html ng-app> // Tells angular to manage this html section
  <head>
    <script src= "angular.js" ></script> // Load the script to make it work
    <script src= "controllers.js" ></script>
  </head>
  <body>
    <div ng-controller= 'HelloController' >
      <input ng-model= 'greeting.text' >   // the model and the text are automatically data
                                          // binded and refresh on change automagically
      <p> {% raw %} {{greeting.text }} {% endraw %}, World </p> // expression between double braces is evaluated
    </div>
  </body>
</html>
~~~~


### Controller

~~~~~ javascript
// the $scope is the model, and the angular DI gives this just by asking
// The $scope is also scoped and given, to conform to difference namespaces
function HelloController ( $scope )
{
  $scope.greeting = { text : 'Hello' };
}
~~~~~

### Modules

~~~~~~~ javascript
// Angulars way of defining namespaces and how you modularize your code
var myAppModule = angular.module( 'myApp' , []); // define a module myApp

// add a controller to the module
myAppModule.controller ( 'TextController' , function ( $scope ) {
  var someText = {};
  someText.message = 'You have started your journey.';
  $scope.someText = someText;
});
~~~~~~~

### Filters

~~~~~ html
// Filters apply a transformation to a text and you can pipe them together
<span> {% raw %}{{item.price * item.quantity | currency }} {% endraw %}   </span>
~~~~~

There’s a bundled filter called `currency` that is doing this dollar formatting for us.
Note that the filter has no idea about the parent html tag. So don't expect formatting with filters.
To create your own filter.

~~~~~ javascript
angular.module('myReverseFilterApp', [])
.filter('reverse', function() {
  return function(input, uppercase) {
      // ... code to reverse honoring uppercase if true
  };
})
~~~~~

~~~~~ html
// can be used as
{% raw %} {{greeting|reverse}}
{{greeting|reverse:true}} {% endraw %}
~~~~~

### Dependency Injection

~~~~~~ javascript
// provide a factory for creation of serviceId, which inturn depends on depService
angular.module('myModule') // note the missing [], means we're using an existing module not creating one.
  // the array notation helps the injector inject the dependencies by looking at the names before the function call to know what to instantiate;
  // to get depService, it will call its depFactory method.
  .factory('serviceId', ['depService', function(depService) {
  //... your service code here.
}]):
~~~~~~

~~~~~ javascript
// You can use a injector as so.
var injector = angular.injector(['ng', 'myAppModule']);
var greeter = injector.get('serviceId');
~~~~~


#### Testing with DI.

When testing a service, you need to ask the injection framework to inject different objects.
This can be done by the `$provide` service of angular.

~~~~~ javascript

// in your beforeEach ..
    module('warehouse.assettracking', function($provide) {
          $provide.value('urlService', mockurlService);
          $provide.value('Restangular', mockRestangular);
          $provide.value('progressBarService', mockprogressBarService);
    });
~~~~~

### Events

~~~~~ html
<button ng-click="callNotify(message);">NOTIFY</button>
~~~~~

### Routes and States
Using `ui-router` to tell angular the various states (possibly nested) that your application is structured into.
Angular then knows how to move to different states based on various events.
Each state can have multiple views. Each view corresponds to a `ui-view` in the base template.
~~~~~ html
<body>
  <div ui-view="filters"></div>
  <div ui-view="tabledata"></div>
  <div ui-view="graph"></div>
</body
~~~~~

~~~~~~~ javascript

.state('home', {
  url: '/home',
  templateUrl: 'partial-home.html'
})

// nested list with custom controller
.state('home.list', {
  url: '/list',
  templateUrl: 'partial-home-list.html',
  controller: function($scope) {
    $scope.dogs = ['Bernese', 'Husky', 'Goldendoodle'];
  }
  views: {
    'filters': {
      templateUrl: 'report-filters.html',
      controller: function($scope){ ... controller stuff just for filters view ... }
    },
    'tabledata': {
      templateUrl: 'report-table.html',
      controller: function($scope){ ... controller stuff just for tabledata view ... }
    },
    'graph': {
      templateUrl: 'report-graph.html',
      controller: function($scope){ ... controller stuff just for graph view ... }
    }
  }
})
~~~~~~~


And then use like with `ng-sref`
~~~~~ html
<a ui-sref=".list" class="btn btn-primary">List</a>
~~~~~

