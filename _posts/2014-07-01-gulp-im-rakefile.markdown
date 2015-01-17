---
layout: post
title: "Gulp im Rakefile."
date: 2014-07-01 17:09
preview: 'Um Ressourcen zu sparen, habe ich einen Taskrunner in den Workflow von Octopress integriert.'
comments: false
categories: ruby rake gulp taskrunner
---

Da mein Blog nicht ganz so ressourcensparend ist, wie ich das gern möchte, habe ich darüber nachgedacht, einen *([node](http://nodejs.org/))* Taskrunner im Workflow zu integrieren.

<!-- more -->

Wie man ja sieht, ist mein Blog statisch. Den ganzen Rahmen setzt [Octopress](http://octopress.org/), das [Theme](https://github.com/iamsebastian/Octopress-Theme-Colerif) hatte ich als [Liquid](https://github.com/Shopify/liquid)-Template-Grundgerüst zusammen gesetzt und dann so lange modifziert, bis die Inhalte gepasst haben. Anschließend habe ich eine große Portion [SASS](http://sass-lang.com) *darauf geworfen* und nun sieht der Blog seit einer Weile aus, wie er eben aussieht. Um möglichst wenig Requests zu haben, schickte ich mich bereits damals an, das Template komplett um zu biegen. Die ganzen unnötigen Bibliotheken, wie jQuery, flogen raus und das [GitHub-Plugin](https://github.com/iamsebastian/iamsebastian.github.io/blob/master/javascripts/github.js) habe ich so weit runter gebrochen, dass es nicht viel mehr macht, als einen async. Call und das ankommende JSON in den DOM zu parsen.

Leider habe ich keine Möglichkeit gefunden, den GET an die GitHub [API](https://developer.github.com/v3/) weiter zu restriktieren, so, dass weniger Daten ankommen würden. Einzig die Anzahl der übermittelten Repository-Informationen lässt sich begrenzen. Im Moment sind das bei mir vier. Die größe des Requests *(rein der Body)* ließ sich so von ~ 55KB auf ~ 24KB runter brechen, was immerhin eine Ersparnis von ~ 55% ist. Lieber würde ich allerdings das JSON der API-Antwort auf Name, URL und Beschreibung einschränken, was dann nicht viel mehr als 15% Restverkehr bedeuten würde.

So komme ich zu dem Punkt, wo ich weiter an der Performanceschraube drehen kann - nämlich den anderen Requests. In dem Moment, als ich noch nichts weiter gemacht habe, hatte ich bei [*Googles Pagespeed Insights*](http://developers.google.com/speed/pagespeed/insights/) einen Score von 86 / 100.

Was mir spontan vorschwebte, war das Konkatieren von HTML und CSS *- wenn es geht natürlich auch JS*. Da meine Ruby-Kenntnisse zwar auf dem Vormarsch sind, aber noch lange nicht die Reife meiner JS Kenntnisse haben, hatte ich die Wahl zwischen Grunt und Gulp. Grunt hatte ich in einem Anderthalb-Quartals-Projekt zwischen den Jahren 2013 und 2014 verwendet und war keineswegs abgetan. Allerdings verwende ich im aktuellen Projekt Gulp, weswegen die Wahl auf eben diesen Taskrunner fiel.

Bei Octopress ist das *Rakefile* so aufgebaut, dass man zum *Generien* des Contents, `rake generate` aufruft und zum Deployen auf GitHub `rake deploy` - voraus gesetzt, alles ist entsprechend konifiguriert. Es würde sich also Anbieten, Gulp entsprechend *zwischen den Zeilen* zu platzieren. Aufgerufen würde das über `system "gulp"` im Rakefile, wenn sich die `gulpfile.js` im selben Verzeichnis befindet, wie das Rakefile. *system* ist [eine Implementierung](http://ruby-doc.org/core-2.1.2/Kernel.html#method-i-system) im ruby core, die den Kernel des Systems ansteuert - oder zumindest das, was die Virtual Machine davon übrig lässt. *system* gibt einen Bool *(true)*, entsprechend des exit code des aktuellen Aufrufes, zurück. Ruby, respektive, das Rakefile, wartet mit der Abarbeitung der Aufrufe, bis der jeweilige Prozess abgeschlossen ist. Daher sollte es kein Problem darstellen, *gulp* zwischen *generate* und *deploy* auf zu rufen.

Ich rufe den Prozess am Ende des *:generate* Tasks auf:



{% highlight rb linenos %}
task :generate do
  # ...
  system "compass compile --css-dir #{source_dir}/stylesheets"
  system "jekyll"
  system "gulp"
end
{% endhighlight %}



*gulpfile.js*

{% highlight js linenos %}
'use strict';
/* global require */

var dir = 'public',
  paths = {
    dest: dir,
    css: dir + '/stylesheets/screen.css',
    html: dir + '/index.html',
    js: dir + '/javascripts/github.js'
};

var fs = require('fs'),
    gulp = require('gulp'),
    replace = require('gulp-replace');

gulp.task('replace-css', function() {
  return gulp.src(paths.html)
    .pipe(replace(/<link(.*)screen.css(.*)>/, function(){
      var _file = fs.readFileSync(paths.css, 'utf-8');
      return '<style>\n' + _file + '\n</style>';
    }))
    .pipe(gulp.dest(paths.dest));
});

gulp.task('replace-js', function() {
  return gulp.src(paths.html)
    .pipe(replace(/<script(.*)github.js(.*)(>.*>)/, function(){
      var _file = fs.readFileSync(paths.js, 'utf-8');
      return '<script>\n' + _file + '\n</script>';
    }))
    .pipe(gulp.dest(paths.dest));
});

gulp.task('replace', ['replace-css'], function() {
  gulp.start('replace-js');
});

gulp.task('default', ['replace'], function() {  
});
{% endhighlight %}



Der Task ist nun vollständig im Workflow integriert, macht also keine zusätzliche Arbeit mehr und erspart mir zwei HTTP Requests. Bleiben also - voraus gesetzt die Fonts sind schon geladen - nur noch zwei Requests übrig: HTML inkl. Stylesheet und der GET Request an GitHub. Das *Inlinen* der Javascript-Datei sollte man allerdings nur vornehmen, wenn sie nicht blockiert, sie also nur eine kurze Rechenzeit braucht oder asynchron arbeitet.