---
layout: post
title:  "Over the Wire - Bandit Levels 5-10"
date:   2016-07-29 22:53:00
categories: security
tags: [pentest, featured]
image: /assets/article_images/2016-07-27-over-the-wire-part-1/2016-07-27.jpg
---

[Over the Wire](http://overthewire.org/wargames/)'s Bandit series is continuing! Lets have more fun!

## Level 10 - 11

Hint:

> The password for the next level is stored in the file **data.txt**, which contains base64 encoded data

base64 decode go!

```sh
bandit10@melinda:~$ base64 -d data.txt
The password is IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR
```

