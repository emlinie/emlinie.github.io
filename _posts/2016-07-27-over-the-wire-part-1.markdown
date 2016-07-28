---
layout: post
title:  "Over the Wire - Bandit Levels 5-10"
date:   2016-07-27 22:53:00
categories: pentest
tags: pentest
image: /assets/article_images/2016-07-27-over-the-wire-part-1/2016-07-27.jpg
---
###The Beginning

[Over the Wire](http://overthewire.org/wargames/) Has fun challenges for newbies like me.

They start at a beginner series called Bandit and you can work your way up or start at higher levels.

At the beginning they start you off on teaching how to navigate through Linux commands such as `ls cd cat find du file` and using the `man` pages to look into the intrcacies of those basic commands. I started myself at [Level 1](http://overthewire.org/wargames/bandit/bandit0.html) but now I'll post some of my beginner solutions. Oh and by the way, there are _spoliers_!


## Level 5 - 6
Skipping the first  few levels, we'll connect to [level 5](http://overthewire.org/wargames/bandit/bandit6.html) using the password we retrived from [Level 4](http://overthewire.org/wargames/bandit/bandit5.html)!
Previously, challenges taught how to us `ls and find` and here we're going to be using more of that.

The hint here is :
>The password for the next level is stored in a file somewhere under the inhere directory and has all of the following properties: - human-readable - 1033 bytes in size - not executable

After connecting to the server, there sits a lone folder named `inhere`. Going into it and using `find` we get a whole lot of fodler and files so using the hint we can find what we need.

```bash
bandit5@melinda:~/inhere$ ls
maybehere00  maybehere03  maybehere06  maybehere09  maybehere12  maybehere15  maybehere18
maybehere01  maybehere04  maybehere07  maybehere10  maybehere13  maybehere16  maybehere19
maybehere02  maybehere05  maybehere08  maybehere11  maybehere14  maybehere17
```
Using the hint

```bash
bandit5@melinda:~/inhere$ cat `find . -size 1033c`
DXjZPULLxYr17uwoI01bNLQbtFemEgo7
```

## Level 6 - 7
The hint is:

>The password for the next level is stored somewhere on the server and has all of the following properties: - owned by user bandit7 - owned by group bandit6 - 33 bytes in size

Similar to the first one. We log in to the home directory but notice that there were no folder or files so it must be somewhere on the _system_. Using the `man` pages for `find` for help:

```bash
bandit6@melinda:~$ find / -size 33c -user bandit7 -group bandit6
find: `/root': Permission denied
find: `/proc/tty/driver': Permission denied
find: `/proc/7210/task/7210/fd/5': No such file or directory
find: `/proc/7210/task/7210/fdinfo/5': No such file or directory
find: `/proc/7210/fd/5': No such file or directory
find: `/proc/7210/fdinfo/5': No such file or directory
find: `/etc/krypton_pass': Permission denied
find: `/etc/chatscripts': Permission denied
find: `/etc/natas_session_toucher': Permission denied
find: `/etc/ssl/private': Permission denied
find: `/etc/natas_pass': Permission denied
find: `/etc/ppp/peers': Permission denied
find: `/run/user/11007': Permission denied
find: `/run/user/12000': Permission denied
find: `/run/user/16003': Permission denied
....
```

Slightly modifying that since we're getting a lot of *stderr* messages.

```bash
find / -size 33c -user bandit7 -group bandit6 2>&1 | grep -v "Permission denied"
find: `/proc/7611/task/7611/fd/5': No such file or directory
find: `/proc/7611/task/7611/fdinfo/5': No such file or directory
find: `/proc/7611/fd/5': No such file or directory
find: `/proc/7611/fdinfo/5': No such file or directory
/var/lib/dpkg/info/bandit7.password
```

We could go furthur and filter for "No much file or directory" but we found what we need.

```bash
find / -size 33c -user bandit7 -group bandit6 2>&1 | grep -v "Permission denied"
find: `/proc/7611/task/7611/fd/5': No such file or directory
find: `/proc/7611/task/7611/fdinfo/5': No such file or directory
find: `/proc/7611/fd/5': No such file or directory
find: `/proc/7611/fdinfo/5': No such file or directory
/var/lib/dpkg/info/bandit7.password
bandit6@melinda:~$ cat /var/lib/dpkg/info/bandit7.password
HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs
```

and done!
