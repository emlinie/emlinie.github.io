---
layout: post
title:  "Over the Wire - Bandit Levels 5-10"
date:   2016-07-29 22:53:00
categories: pentest
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

## Level 11 - 12
[Hint](http://overthewire.org/wargames/bandit/bandit12.html):

> The password for the next level is stored in the file data.txt, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions

We can use `tr` here to transform the string by *"mapping"* the characters 13 places ahead.

```sh
bandit11@melinda:~$ cat data.txt
Gur cnffjbeq vf 5Gr8L4qetPEsPk8htqjhRK8XSP6x2RHh
bandit11@melinda:~$ cat data.txt | tr '[a-zA-Z]' '[n-za-mN-ZA-M]'
The password is 5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu
```

