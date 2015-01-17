---
layout: post
title:  "neo4j dump via SSH"
date:   2015-01-14 10:45:21
categories: ssh neo4j
preview: 'Wie ich regelmäßig die neo4j Datenbank von einem Test-Server mit einem lokalen Server abgleiche, um optimale Bedingungen zum Debuggen zu schaffen.'
---

+ VPN Verbindung
+ scp buildserver@10.100.11.10:~/dump ./
+ [Thema auf stackoverflow](http://stackoverflow.com/questions/343711/transferring-files-over-ssh)