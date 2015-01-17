---
layout: post
title: "git log formatieren"
date: 2014-03-06 17:29
comments: true
categories: git
---

Anschließend, zum vorherigen Post, ist es natürlich auch möglich, die GIT Log frei selbst zu gestalten. Dafür gibt es Massen an Platzhaltern, die das Verfassen eines eigenen Skriptes & Aliases nicht nur möglich, sondern auch sinnvoll machen.

<!-- more -->

Für den Daily Standup erscheint es mir beispielsweise äußerst sinnbehaftet, eine Ausgabe der letzten zwei Tage aus den Logs zu drücken.

```sh
git log --since=2d
```

Oder eine sehr kurze Zusammenfassung, mit just den letzten drei Commits:

```sh
git log -3
```

Es ist auch möglich die Ausgabe mit Platzhaltern zu versehen, um einen bessere Übersicht zu gewinnen. Der Platzhalter des *Platzhalters* -- also im wörtlichen, dimensionalem Sinne -- ist `%<|(n)`, wobei **n** dabei für die frei zu haltende Länge steht. Die Angabe der Länge erfolgt absolut in Abstand zum Promptbeginn. Beispielsweise ergibt der folgende Aufruf eine Kolumne mit drei Reihen:

```sh
git log --format="%<|(13)%cr %<|(32) %cn %s"

9 hours ago    Sebastian Blei    fixed IE8 build
26 hours ago   Sebastian Blei    IE8 - set as my car - fix
27 hours ago   Sebastian Blei    debug build
```

**%cr** ist der Platzhalter für ein relatives Datum zum Zeitpunkt des Commits, **%cn** ist der Platzhalter für den Namen der Person, die das Commitment erstellt hat und **%s** ist für die Ausgabe des Strings zuständig, also für die Commitment Nachricht. Um das ganze noch etwas übersichtlicher zu gestalten, kann man sich auch farblich austoben. *git log* unterstützt von Haus aus drei Platzhalter für Farben: **%Cred** Rot, **%Cgreen** Grün und **%Cblue** Blau. Eine Marke, also eine Art Tabbing, stellt **%m** dar. 

Meine *Shortlog* (ohne Zuhilfenahme von weiteren Shell Befehlen) sieht wie folgt aus:

```sh
$ git log --since=2d --format="%<|(13)%cr %<|(32)%Cred%cn %Cblue%s"

9 hours ago   Sebastian Blei     fixed IE8 build
27 hours ago  Sebastian Blei     IE8 - set as my car - fix
27 hours ago  Sebastian Blei     debug build
27 hours ago  Sebastian Blei     timeline competitor fix
2 days ago    Sebastian Blei     reconstructed & finished timeline
```

Im nächsten Post habe ich vielleicht noch kurz Gelegenheit, darauf ein zu gehen, wie man die Log weiter spezifiziert. Beispielsweise, lässt man nur Commits aus der Log ausgeben, die man selber verfasst hat, oder spart *merges* aus.