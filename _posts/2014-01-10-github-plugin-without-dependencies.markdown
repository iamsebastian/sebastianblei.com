---
layout: post
title: "github plugin without dependencies"
date: 2014-01-10 08:21
comments: true
categories: github, octopress
published: false
---

The included github plug-in, which comes with octopress to list your public github repositories isn't bad overall. But it has a dependency I don't want to include on an initial pageload: `jQuery`. I will show, how you could talk with the github API without jQuery.

<!-- more -->

The [github API v3](http://developer.github.com/v3/) is well documented. An example URL to request some data:

`https://api.github.com/users/USERNAME/repos`

At this endpoint, you get a stringified response with informations about some of your repos. There are the possibility to send some options with your request. For example, if you want to get your repos sorted, you could specify your request with `repos?sort=pushed`. The github plug-in that comes along with a default octopress installation, will also request this sort by default.

`https://api.github.com/users/USERNAME/repos?sort=pushed`

Now we would send a *XML HTTP Request* to the github API and log the request to your browsers console.

```js
var x = new XMLHttpRequest();
x.onload = function() {
  console.log(this.response);
}
x.open('GET', 'https://api.github.com/users/iamsebastian/repos?sort=pushed', true);
x.send();
```

The output on your log should something like this:

```js
[{
  id: 13784303,
  name: "octopress",
  description: "Octofox.",
[...]
```

We need to `JSON.parse()` the request:

```js
var x = new XMLHttpRequest();
x.onload = function() {
  var r = JSON.parse(this.response);
  ...  
}
x.open('GET', 'https://api.github.com/users/iamsebastian/repos?sort=pushed', true);
x.send();
```

At this point, feel free to play around with the github API or to paint the response in your view. By now, this is my `github.js` with the skeleton of the old jQuery one:

```js
var github = (function(){
  function render(target, repos){
    var i = 0, fragment = '', t = document.querySelector(target);

    console.log('repos',repos);
    for(i = 0; i < repos.length; i++) {
      fragment += '<li><span class="githubrepo"><a href="'+repos[i].html_url+'">'+repos[i].name+'</a></span><p>'+repos[i].description||''+'</p></li>';
    }
    t.innerHTML = fragment;
  }
  return {
    showRepos: function(options){
      var x = new XMLHttpRequest();
      x.onload = function() {
        var r = JSON.parse(this.response);
        var repos = [];
        for (var i = 0; i < r.length; i++) {
          if (options.skip_forks && r[i].fork) { continue; }
          repos.push(r[i]);
        }
        if (options.count) { repos.splice(options.count); }
        render(options.target, repos);
      };
      x.open('GET', 'https://api.github.com/users/iamsebastian/repos?sort=pushed', true);
      x.send();
    }
  };
})();
```