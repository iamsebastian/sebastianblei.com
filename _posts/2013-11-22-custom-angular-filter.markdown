---
layout: post
title: "custom angular filter"
date: 2013-11-22 17:41
comments: false
categories: angular
published: false
---

If you want to setup your very first custom angular filter, there are some steps to follow. Create a new filter, inject'em and use it in your view or $scope. It's on yor own, what you do with it. But you should give it a try.

<!-- more -->

At first, you need to scaffold out your filter with yeoman: 

```sh
$ yo angular:filter animalfilter
```

This will output something like this:

```sh
$ create app/scripts/filters/animalfilter.js
$ create test/spec/filters/animalfilter.js
```

**animalfilter** will be the name of your filter (as file and as service in angular).

If you're not familiar with yeoman, you can also create the filter by your own. To do so, create a `myfilter.js` and place it in your scripts-directory. Next, let your HTML know about the new filter and declare it as a script.

```html  
<script src="./scripts/animalfilter.js"></script>
```

Now, the skeleton of the filter should look like this:

```js
'use strict';

var app = angular.module('myApp');
app.filter('animalfilter', function () {
    return function (input) {
      var arr = [];
      for(var i = 0; i < input.length; i++) {
        if(input[i].weight > 3) {
            arr.push(input[i]);
        }
      };
      return input;
    };
  });
```

In the controller, we create some data:

```js
$scope.animals = [
    snake: {
        length: 137,
        weight: 3.23
    },
    lizzard: {
        length: 46,
        weight: 2.54
    }
];
```

... and bind it in the view:

```html
<div ng-repeat="a in animals">
    <p><span>Length: </span>a.length</p>
    <p><span>Weight: </span>a.weight</p>
</div>
```

While you should see your complete data, we now handle the data to the filter and bind only filtered data output to our view:

```html
<div ng-repeat="a in animals | animalfilter">
    <p><span>Length: </span>a.length</p>
    <p><span>Weight: </span>a.weight</p>
</div>
```

The data of the object **animals** is bound to the filter while the filter just returns animals, which are heavier than *3*.

You also can inject the filter in your controller and filter data there.

```js
app.controller('appCtrl', function ($scope, animalfilterFilter) {

    });
```

**Attention**: If you want to inject the filter in your controller, you need the attach 'Filter' to the name.