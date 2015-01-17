---
layout: post
title: "git log Filter"
date: 2014-03-07 06:50
comments: true
categories: git
preview: 'Weitere Formatierungsmöglichkeiten für die Log von git.'
---

Nach dem ich bereits über einige Möglichkeiten geschrieben habe, mit der Log um zu gehen, möchte ich noch auf einige weitere Möglichkeiten eingehen, wie man die Ausgabe weiter restriktieren kann. 

<!-- more -->

Die folgenden Limitierungen, bzw. Filteroptionen, werden angewendet, bevor eine spezifizierte Formatierung statt findet. Das heißt, dass eine Regular Expression auf die gesamte Standardausgabe angewandt wird und nicht nur, auf die, mit **--format** eingeschränkte, Ausgabe.

Über **--since=$date** (alternativ **--after=$date**) hatte ich bereits geschrieben. Dies gibt die Commits aus, die nach dem angegebenen Datum statt fanden. Eine relative Datumsangabe, wie **2d** für zwei Tage, ist hier üblich. Aber natürlich sind auch absolute Datumsangaben möglich. Beispielsweise im Format **YYYY-MM-DD**:


{% highlight bash %}
$ git log --skip=18 --since=2014-03-05

9029665 Additional fixes on SVG stuff. Place some variable gradient stuff in paths
42ee614 some additional changes in about, style fixes and correction in sass source.
{% endhighlight %}


Dies sind die Commits #19 und #20 aus den letzten beiden Tage, respektive vom 5. März 2014 bis 7. März 2014. Passend zu diesem *neuer-als-Filter*, ist es auch möglich, Commits anzeigen zu lassen, die vor einem bestimmten Datum passiert sind: *(alternativ **--until=$date**)*


{% highlight bash %}
$ git log --date=relative --before=2014-03-05

bab314d dismissed some script stuff
0e9295d refactored sass
2f02101 fixed some scripting issues
{% endhighlight %}


Um nur Commits eines bestimmten Autors anzeigen zu lassen, nutze ich **--author=$name**. Das benutzte Pattern ist eine Regular Expression und die Verkettung des Filters ist möglich -- bei einer Reguluar Expression aber freilich unnötig. Das Äquivalent dazu ist **--committer=$name**. Hinweis: Wie bei Patterns in GIT üblich, ist auch hier das Matching *case-sensitive*.


{% highlight bash %}
$ git log --since=2d --author='Blei' --format="%<|(13)%cr %<|(32)%Cblue%s"

20 hours ago  Changed post.     
21 hours ago  Changed to-read book size and word count
2 days ago    Fixed jsHint notifications
{% endhighlight %}


Ist man in einem Projekt so weit, dass man Versionierung eingeführt hat und das Projekt entsprechend die Maße hat, um eine Versionierung zu begrüßen, kann man alle Einträge ab einem Versionierungssprung wie folgt anzeigen lassen:


{% highlight bash %}
$ git log v1.0..
{% endhighlight %}


Immer wieder kommt es zu Merges, deren Ausgabe wenig in einer History zu suchen hat. Sucht man beispielsweise nur Fixes, keine Merges:


{% highlight bash %}
$ git log --no-merges
{% endhighlight %}
