---
layout: post
title: "Shell: Pfadgrößen anzeigen"
date: 2014-02-07 17:09
comments: false
categories: shell zsh
---

Freitag, am Morgen. Die Robot Tests sind abgeschlossen, das Projekt wird für's Deployment vorbereitet. Der letzte Durchlauf vom *grunt* steht an. Der *task runner* startet ... und bricht ab. Die nagelneue SSD ist voll. *Verdammt! Wo ist der freie Platz nur hin?* Eine zügige Lokalisierung der Platzverschwendung würde sich jetzt anbieten. Und zwar schleunigst. Mit einem Script.

<!-- more -->

```sh
$ df -h
Filesystem      Size   Used   Avail Capacity  iused  ifree %iused  Mounted on
/dev/disk0s2    120Gi  120Gi  0.0Gi   100% 14734031    975   100%   /
```

Ok. Die Platte ist offensichtlich voll. Ins *home*-Verzeichnis springen und verschiedene Verzeichnisse durchwühlen. Hier und da. Und da. Und da, und da, und da. Hmm. *Wo könnte noch was versteckt sein. Ach, da wurde noch was herunter geladen. Oder hier. Ach, wo war das nur?* Wir könnten jedes einzelne Verzeichnis durchwühlen. Oder wir haben ein Script. Und einen Alias.

```sh
$ fatfiles
4K      ./music.md
14,8M   ./tmp
114,3M  ./Desktop
312,9M  ./Pictures
521,2M  ./Documents
764M    ./Projects
1003,8M ./GIT
3,2G    ./Downloads
5,3G    ./Music
6,8G    ./Library
9,2G    ./Movies
Total: 27,3G
```

Yeah! Nette Zusammenfassung. Sie spart eine Menge Zeit. Doch was macht das Script? Fangen wir mit *du* an:

```sh
du -sk ./*
```
**du** stellt die Plattennutzung dar, **-s** spezifiziert einen einzelnen Eintrag für jedes Element und **-k** setzt die anzuzeigende Blockgröße auf 2^10 Bytes (=== 1 KByte) ein. Als nächstes übermitteln wir den Erguss von *du* per stdout nach *sort*:

```sh
sort -n
```

**sort** sortiert Einträge und **-n** ist die Kurzfassung für **--numeric-sort**, das *sort* anweist, nach numerischen Werten zu sortieren.

Als letztes nehme man den Output von *$ du -sk ./ | sort -n* und schicke es per Pipe zu *awk*. *awk* ist dafür da, den Output aufzuhübschen. Dies geschieht mit unterschiedlichen Algorithmen, die ähnlich funktionieren wie der Modulo-Operator, um menschenlesbare Größen anzuzeigen. Als abschließende Linie wird dem Eintrag eine Zeile angehangen, die die Gesamtgröße des aktuellen Verzeichnisinhaltes darstellt. 

```awk
awk 'BEGIN{ pref[1]="K"; pref[2]="M"; pref[3]="G";} 
{ total = total + $1; x = $1; y = 1; 
  while( x > 1024 ) 
  { x = (x + 1023)/1024; y++; } 
  printf("%g%s\t%s\n",int(x*10)/10,pref[y],$2); } 
  END { y = 1; while( total > 1024 )
    { total = (total + 1023)/1024; y++; }
    printf("Total: %g%s\n",int(total*10)/10,pref[y]); }'
```

Das Script am Ende:

```sh
#!/bin/sh
du -sk ./* | sort -n | awk 'BEGIN{ pref[1]="K"; pref[2]="M"; pref[3]="G";} { total = total + $1; x = $1; y = 1; while( x > 1024 ) { x = (x + 1023)/1024; y++; } printf("%g%s\t%s\n",int(x*10)/10,pref[y],$2); } END { y = 1; while( total > 1024 ) { total = (total + 1023)/1024; y++; } printf("Total: %g%s\n",int(total*10)/10,pref[y]); }'
```