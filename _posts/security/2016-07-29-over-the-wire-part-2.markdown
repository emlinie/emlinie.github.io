---
layout: post
title:  "Over the Wire - Bandit Levels 10-15"
date:   2016-07-29 22:53:00
categories: pentest
tags: [pentest, featured]
image: /assets/article_images/2016-07-27-over-the-wire-part-1/2016-07-27.jpg
---

[Over the Wire](http://overthewire.org/wargames/)'s Bandit series is continuing! Lets have more fun! Starting at level 13 and over are methods/commands I don't use very often so it's interesting to see how far they can push me in this series. 

What am I listening to? [K-Hip Hop Beats](https://open.spotify.com/user/spotify/playlist/4FtvCOlFKwqo2uPolnuypJ)

## Level 10 - 11

Hint:

> The password for the next level is stored in the file **data.txt**, which contains base64 encoded data

base64 decode go!

```sh
bandit10@melinda:~$ base64 -d data.txt
The password is IFu**************************
```

## Level 11 - 12
[Hint](http://overthewire.org/wargames/bandit/bandit12.html):

> The password for the next level is stored in the file data.txt, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions

We can use `tr` here to transform the string by *"mapping"* the characters 13 places ahead.

```sh
bandit11@melinda:~$ cat data.txt
Gur cnffjbeq vf 5Gr8L4qetPEsPk8htqjhRK8XSP6x2RHh
bandit11@melinda:~$ cat data.txt | tr '[a-zA-Z]' '[n-za-mN-ZA-M]'
The password is 5Te**************************
```

## Level 12 - 13
[Hint](http://overthewire.org/wargames/bandit/bandit13.html):

> The password for the next level is stored in the file data.txt, which is a hexdump of a file that has been repeatedly compressed. 

Hell yeah, first point of me going *"What is this?"* First thing, lets read the man pages to see what we can do.

Before we begin, we should see what the file currently contains.

```sh
bandit12@melinda:/tmp/jxn$ cat data2.txt
0000000: 1f8b 0808 34da 6554 0203 6461 7461 322e  ....4.eT..data2.
0000010: 6269 6e00 013f 02c0 fd42 5a68 3931 4159  bin..?...BZh91AY
0000020: 2653 5982 c194 8a00 0019 ffff dbfb adfb  &SY.............
0000030: bbab b7d7 ffea ffcd fff7 bfbf 1feb eff9  ................
0000040: faab 9fbf fef2 fefb bebf ffff b001 3b18  ..............;.
0000050: 6400 001e a000 1a00 6468 0d01 a064 d000  d.......dh...d..
0000060: 0d00 0034 00c9 a320 001a 0000 0d06 80d1  ...4... ........
.
.
.
bandit12@melinda:/tmp/jxn$

```

Just as the hint says, it's a hexdump file. Looking up all the commands that are *"useful"* from the page, the command `xxd` stands out, link [here](http://linux.die.net/man/1/xxd).

```sh
bandit12@melinda:/tmp/jxn$ xxd -r data.txt data
bandit12@melinda:/tmp/jxn$ file data
data: gzip compressed data, was "data2.bin", from Unix, last modified: Fri Nov 14 10:32:20 2014, max compression
bandit12@melinda:/tmp/jxn$
```

If we change the file extension to a gzip format, we can extract it.

```sh
gzip: data already exists; do you wish to overwrite (y or n)? y
bandit12@melinda:/tmp/jxn$ ls
data  data.txt
```

so we do the same thing again, `file data`. This challenge actually repeats itself from decompressing the file and using `file` to get the correct file extension it was using. In addition `bzip2` and  `tar` had to be used to get the password. Here's a little snippet below of this:

```sh
bandit12@melinda:/tmp/jxn$ file data
data: POSIX tar archive (GNU)
bandit12@melinda:/tmp/jxn$ file data8.bin
data8.bin: gzip compressed data, was "data9.bin", from Unix, last modified: Fri Nov 14 10:32:20 2014, max compression
bandit12@melinda:/tmp/jxn$ mv data8.bin data9.gz
bandit12@melinda:/tmp/jxn$ gzip -d data9.gz
bandit12@melinda:/tmp/jxn$ ls
data  data.tar  data.txt  data5.bin  data5.tar  data6  data9
bandit12@melinda:/tmp/jxn$ file data9
data9: ASCII text
bandit12@melinda:/tmp/jxn$ cat data9
The password is 8Zj**************************
```



## Level 13 - 14 
[Hint](http://overthewire.org/wargames/bandit/bandit13.html):

> The password for the next level is stored in /etc/bandit_pass/bandit14 and can only be read by user bandit14. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. Note: localhost is a hostname that refers to the machine you are working on
> Commands you may need to solve this level
> `ssh`, `telnet`, `nc`, `openssl`, `s_client`, `nmap`

Hooray we get an ssh key, using this key we might be able to get to the next level. Also take a note that localhost being this machine would make sense as we're logging on to different users ever level.

```sh
bandit13@melinda:~$ ssh -i sshkey.private bandit14@localhost
Could not create directory '/home/bandit13/.ssh'.
The authenticity of host 'localhost (127.0.0.1)' can't be established.
ECDSA key fingerprint is 05:3a:1c:25:35:0a:ed:2f:cd:87:1c:f6:fe:69:e4:f6.
Are you sure you want to continue connecting (yes/no)? yes
Failed to add the host to the list of known hosts (/home/bandit13/.ssh/known_hosts).

bandit14@melinda:~$ cat /etc/bandit_pass/bandit14
4wc**************************
```


## Level 14 - 15 
[Hint](http://overthewire.org/wargames/bandit/bandit15.html):

> The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.
> Commands you may need to solve this level
> `ssh`, `telnet`, `nc`, `openssl`, `s_client`, `nmap`

I have to look up these commands and how they are used and thier usage on the command line. One of the command stand out from the skim reading and from my knowledge. `nc` is what I would be using.

Looking at the man pages for  [`nc`](http://linux.die.net/man/1/nc) tells that it's used for network communication from listening to sending UDP or TCP.

```sh
bandit14@melinda:~$ nc -v localhost 30000
Connection to localhost 30000 port [tcp/*] succeeded!
4wc**************************
Correct!
BfM**************************
```

Wow, we're just getting started eh? See you next time...








