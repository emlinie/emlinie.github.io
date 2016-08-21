---
layout: post
title:  "Kali Linux 2016.1 With Guest Additions"
date:   2016-08-21 00:15:00
categories: tech
tags: [tech]
image: /assets/article_images/2016-08-21-kali-linux-with-vbox/2016-08-21.png
---

## Kali Linux 2016.1 with VBox Guest Additions

Here's a simplied guide on how to get Kali Linux with VBox Guest features!
This includes the ability to have a share clipboard and drag and drop (features that I coudn't get to work)

If you already have  `virtualbox-guest-x11` installed, we're going to have to purge them.

```sh
apt-get purge virtualbox-guest-x11
apt-get autoremove --purge
```


First perform a dist-upgrade with reboot
```sh
apt-get update
apt-get -y dist-upgrade
reboot
```

And install the guest additions
```sh
apt-get update
apt-get install -y virtualbox-guest-x11
apt autoremove
reboot
```

Dont forget to enable shared cliboard and file drop options in Virtual Box!

