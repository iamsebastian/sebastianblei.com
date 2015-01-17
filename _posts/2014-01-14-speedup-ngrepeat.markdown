---
layout: post
title: "speedup ngRepeat"
date: 2014-01-14 13:21
comments: true
categories: angular
published: false
---

Imagine, you have a dataset with a count of ~ 700. An array of objects, filled with 20 properties each. You iterate through the dataset with ng-repeat ... And maybe, it will be there. The fear, the no response ... A lag. *Your browser does not respond*. After this situation, it's time for a change. Without $watches - maybe. Here it comes:  

<!-- more -->

[bindonce](https://github.com/Pasvaz/bindonce). It's called a *high performance binding for angularJS*. With *bindonce*, you can prevent your app from performance issues. All details are explained at the github repo.

I will attach two screenshots here, but I won't explain timings and memory usage. You would get it for yourself.  

Before I've used bindonce:

[![Pre](http://i.imgur.com/Y0vX69u.png)](http://i.imgur.com/Y0vX69u.png)

After the change:

[![Post](http://i.imgur.com/J3jm7ND.png)](http://i.imgur.com/J3jm7ND.png)

Nothing more to say than: much of non-interactive data? *bindonce* is your solution - maybe.