---
layout: post
title: "big data with angular"
date: 2013-11-11 12:20
comments: false
categories: angular
published: false
---

If your app gets slow, it's maybe in cause of *big data* or *big data with ng-repeat*. The reason why your app gets slow: ng-repeat attaches *$watch*es to your *SPA*. This will break the performance. The solution: *binding* without *$watch*es.

<!-- more -->

The first proper solution would be, to write an own directive which iterates through the data and don't attach *$watch*es. But there will be the point, where we have to face the following: *data need to be existent, to get rendered in the view*. If we want to do correct render, we need to bind the data (still a watcher) or attach a *$watch*.

So, to do *high performance binding* with angular, we use [this](https://github.com/Pasvaz/bindonce) ([bindonce](https://github.com/Pasvaz/bindonce)) public repo, hosted on github *(MIT license)*, which will do the following: "*Bindonce can wait until the data are ready before to render the content*".

Have a look at the following snippet:

``html
<span my-custom-set-text="Person.firstname"></span>
<span my-custom-set-text="Person.lastname"></span>
...
<script>
angular.module('testApp', [])
.directive('myCustomSetText', function () {
    return {
        link:function (scope, elem, attr, ctrl) {
            elem.text(scope.$eval(attr.myCustomSetText));
        }
    }
});
</script>
```