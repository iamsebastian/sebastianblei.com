---
layout: post
title: "shell: list directory sizes"
date: 2014-02-04 17:02
comments: false
categories: zsh, shell
published: false
---

Friday in the morning. Robot tests are passed. The project is going to get deployed. You start *grunt* and ... there's no space left on your shiny new SSD. Damn it! It seems the space is gone. But we can localize it. Fast. With scripting.

<!-- more -->

```sh
$ df -h
Filesystem      Size   Used   Avail Capacity  iused  ifree %iused  Mounted on
/dev/disk0s2    120Gi  120Gi  0.0Gi   100% 14734031    975   100%   /
```

Ok. No space left. So we will jump to the home directory and have a look in some folders. Look there, and there, and there. And I downloaded this and that ... Maybe something there. *Do I need this stuff? Not anymore*. We can have a look at every single directory, or we have a script. And an alias.

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

Yeah. Nice comparison! Saves much time. But how to do this? We start with *du*:

```sh
du -sk ./*
```
**du** displays disk usage statistics, **-s** an entry for each specified file and **-k** sets block counts in 2^10 bytes (=== 1 KByte).  
On next, we pipe the stdout of *du* to *sort*:

```sh
sort -n
```

**sort** sorts lines of text and **-n** is the shorthand for **--numeric-sort**, which let *sort* compare according to string numerical values.  

At last, we take the output of *$ du -sk ./ | sort -n* and pipe the stdout to *awk*. *awk* will prettify the output for us, with some specific algorithms (like *modulo*) for human readable size output. The last line will be a comparison as size of the whole directory.

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

The final script:

```sh
#!/bin/sh
du -sk ./* | sort -n | awk 'BEGIN{ pref[1]="K"; pref[2]="M"; pref[3]="G";} { total = total + $1; x = $1; y = 1; while( x > 1024 ) { x = (x + 1023)/1024; y++; } printf("%g%s\t%s\n",int(x*10)/10,pref[y],$2); } END { y = 1; while( total > 1024 ) { total = (total + 1023)/1024; y++; } printf("Total: %g%s\n",int(total*10)/10,pref[y]); }'
```