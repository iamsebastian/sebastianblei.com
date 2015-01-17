---
layout: post
title:  "SVGs als Sprites"
date:   2015-01-09 18:19:00
categories: svg css
preview: 'Da viele Browser nun die Möglichkeit besitzen, SVGs zu nutzen, bietet sich die Möglichkeit an, diese als Sprites zu benutzen. Zum Beispiel mit dem <b>:target</b> Selektor.'
---

Bei den Crowds begann dieses Jahr mit einigen Neuerungen. Beispielsweise wurde die erste Woche des Jahres genutzt, um einen, eine Woche andauernden, HackDay zu gestalten. Da das Monitoring unserer API in den letzten Wochen von 2014 etwas ins Hintertreffen geriet, machte ich mich mit einem anderen Crowd daran, ein Dashboard zum Monitoren unserer API zu bauen. Grundlage dafür waren Sockets, SVGs, node.js und PM2.

Die Anforderungen waren so gewählt, dass wir am Wochenende — *heute* — ein Werkzeug besitzen, dass unsere *Production* API überwacht, die Auslastung der einzelnen Instanzen darstellt, sowie als Steuerung der Prozesse genutzt werden kann.

Nach langem hin und her und letztendlich dem Rauswurf von angular-charts aus unserem Projekt, münzte ich mehr und mehr SVG-Inhalte auf das Dashboard ein. Eine praktische Gelegenheit, zu testen, ob ich alle SVGs in einem SVG halten kann, da ich SVGs regelmäßig im Editor *zeichne*, bzw. animiere.

Ich wählte die Möglichkeit, ein Objekt des SVG-Models via `:target` Pseudoselektor dar zu stellen. Konkret funktioniert das so: Man *versteckt* alle Objekte (einer Klasse) in einem SVG, zum Beispiel via `display:none`, und setzt ein validen display-Wert, wenn die ID des SVG-Objekts als *target* angewählt wurde.

Ein Beispiel:

{% highlight html %}
<svg viewBox="0 0 512 512" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">

    <defs>
        <style type="text/css">
            svg .sprite {
                display: none;
            }
            svg .sprite:target {
                display: block;
            }
        </style>
    </defs>

    <g id="green" class="sprite">
        <path d="M0,287.01159 L0,96.3195674 L165.606517,0 L329.023807,96.4151866 L329.322578,287.01159 L166.398752,383.403986"  fill-opacity=".3" fill="#3CC970"></path>
    </g>

    <g id="red" class="sprite">
        <path d="M0,287.01159 L0,96.3195674 L165.606517,0 L329.023807,96.4151866 L329.322578,287.01159 L166.398752,383.403986"  fill-opacity=".4" fill="#E3244E"></path>
    </g>
</svg>
{% endhighlight %}

In den `defs` (definitions) ist das entsprechend CSS enthalten, dass die Objekte der Klasse `sprite` versteckt und bei einem `:target`-Selektor wieder darstellt.

Ruft man nun, beispielsweise in der Adresszeile des Browsers, das SVG auf und setzt das Target `#green` dahinter, wird das SVG Gruppenobjekt mit der id `green` dar gestellt.

Beispiel: Adresszeile im Browser: 

{% highlight text %}
file:///Users/sblei/GIT/pm2-interface/client/src/images/sprites.svg#green
{% endhighlight %}

Animationen und Clip-Paths funktionieren ebenso.

Nun kann man das Objekt auch als `background-image` im CSS nutzen:

{% highlight css %}
.knopf {
	background-image: url('sprites.svg#green');
}
{% endhighlight %}

Leider ist das ganze zwar unglaublich praktisch, momentan wird es aber nur (noch) von der Gecko-Engine im FireFox unterstüzt, da die Blink-Engine dies unter anderem mit der Begründung auf eine möglich Cross-Side-Scripting Attacke im Juli 2012 aus der Funktionalität gestrichen hat.