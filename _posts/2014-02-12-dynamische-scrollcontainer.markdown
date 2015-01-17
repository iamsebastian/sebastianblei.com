---
layout: post
title: "Dynamische Scrollcontainer"
date: 2014-02-12 17:12
comments: true
categories: angular
---

In einem aktuellen Projekt häuften sich die Lauf- und Ladezeiten im *IE8* auf ein unerträgliches Maß. Die ersten Schritte -- in denen es um die Konfiguration der Applikation geht --, erfuhr man noch erträgliche Ladezeiten, trotz großer Datenmengen. Doch nach Schritt Drei der Konfiguration, wo der eigentliche, interaktive Informationsgehalt dargestellt wird, hatte man durchaus Zeit, Kaffee kochen zu gehen.

<!-- more -->

Ein Container, der gezeichnet werden musste, umfasste sechs Views. Die erste View war als Header mit wichtigen Informationen permanent sichtbar; von den verbleibenden fünf Views war jeweils eine sichtbar war. Freilich lädt der *IE8* alles in einem *Schwung* *-- wobei das zügig klingt, ohne beabsichtigt zu sein --*, während *WebKit* und *Gecko* sich erst mit dem Zeichnen der Views aufhalten, wenn diese auch tatsächlich im Sichtfeld sind. Knackpunkt dabei war die letzte View, die eine drei-spaltige Übersicht -- die allesamt separat scrollbar sind -- mit hunderten Modellen darstellt. Ein Modell umfasst dabei etwa acht Knoten im *DOM*. Mit einem Model-Array von etwa 900 Objekten, kamen wir damit auf eine Ladezeit von etwa 90 Sekunden im *IE8*. *WebKit* und *Gecko* begnügten sich hierbei mit etwa vier Sekunden. Da dies einem Kunden nicht zumutbar ist, habe ich eine dynamische Direktive geschrieben, die die Ladezeit im *IE8* auf etwa zehn Sekunden runterbricht.

Die Idee, die einzelnen, scrollbaren Spalten dynamisch in der Anzahl der Einträge zu machen, entstand dabei aber nicht erst an dieser Stelle. Bereits an einer vorherigen Stelle, wo man sich durch alle Modelle suchen kann und gleichzeitig alle Treffer in der Suchliste hervorgehoben werden, habe ich eine Direkte verwendet, die mitwächst.

Zu Anfang stehen in den drei Spalten je etwa zehn Einträge -- wobei dies abhängig von den Filterkriterien ist. Zehn Einträge sind also die initiale Obergrenze, die möglich ist. Scrollt man nun in dem Container nach unten, erscheint mit jedem gefeuerten Scroll-Event, ein zusätzlicher Eintrag. Gleich, ob das mit dem Mausrad, per Trackpad oder per Klick am Scrollbalken des Containers geschieht.

Die Scope wird mit der dynamischen Obergrenze initialisiert:

```js
$scope.dynamicLimit = 10;
```

Die Direktive wird initialisiert:

```html
<div filter-scroll="dynamicLimit">
  ...
</div>
```

... und bi-direktional gebunden:

```js
app.directive('filterScroll', function () {
  return {
    restrict: 'AE',
    scope: {
      limit:'=filterScroll'
    },
    link: function($scope, element, attrs) {
      angular.element(element).bind('scroll', function() {
        $scope[attrs.scroll] = true;
        $scope.limit++;
      });
    }
  };
});
```

Die Iteration erfolgt mit `ng-repeat`, sowie dem instanziierten `limitTo`-Filter von Angular:

```html
<div ng-repeat="foo in bar | limitTo:dynamicLimit">
 ...
</div>
```