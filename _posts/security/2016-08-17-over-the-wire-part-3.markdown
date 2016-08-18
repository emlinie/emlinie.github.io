---
layout: post
title:  "Over the Wire - Bandit Levels 15-20"
date:   2016-08-17 22:53:00
categories: pentest
tags: [pentest]
image: /assets/article_images/2016-07-27-over-the-wire-part-1/2016-07-27.jpg
---

[Over the Wire](http://overthewire.org/wargames/)'s Bandit series again, and more to go. I've been busy at work with some performance testing and performance analysis, but here's a new episode of this! So what awesome track is playing for me today? What am I listening to? [Brain Food!](https://open.spotify.com/user/spotify/playlist/67nMZWgcUxNa5uaiyLDR2x)

### Notes: I shorted all certs and keys, they're just a bunch of characters anyways and they take up space!

## Level 15 - 16

[Hint](http://overthewire.org/wargames/bandit/bandit16.html):

> The password for the next level can be retrieved by submitting the password of the current level to port 30001 on localhost using SSL encryption.

> Helpful note: Getting “HEARTBEATING” and “Read R BLOCK”? Use -ign_eof and read the “CONNECTED COMMANDS” section in the manpage. Next to ‘R’ and ‘Q’, the ‘B’ command also works in this version of that command…

Okay so this level is using ssl, looking up `openssl` man pages located [here](https://www-origin.openssl.org/docs/manmaster/apps/s_client.html) gives me more information about `openssl` and `s_client`.

Lets try straight up connecting to localhost on port 30001

```sh
bandit15@melinda:~$ openssl s_client -connect localhost:30001
CONNECTED(00000003)
depth=0 CN = li190-250.members.linode.com
verify error:num=18:self signed certificate
verify return:1
depth=0 CN = li190-250.members.linode.com
verify return:1
---
Certificate chain
 0 s:/CN=li190-250.members.linode.com
   i:/CN=li190-250.members.linode.com
---
Server certificate
-----BEGIN CERTIFICATE-----
MIIC3jCCAcagAwIBAgIJAI5QiWZw4YHbMA0GCSqGSIb3DQEBCwUAMCcxJTAjBgNV
BAMTHGxpMTkwLTI1MC5tZW1iZXJzLmxpbm9kZS5jb20wHhcNMTQxMTE0MTAyODA0
...
fGPFpuPCVqVPsruei8Z/iqn3bFIhQQdmumeePZQdPMwZSWHNVYC5XODd7PvNDrDu
5MZJjkz4+6LbwwAvyew62meFN2QEsYbK2Brtbhze+IjE27FGWlSw4K3jlwa409MD
MTf4JU41ELaYY8G/LSNDJsBVhhkHzvXR9iCbXxNz3IL0dQDNj7h4LKhBy0q7hvqg
kDzwlmBO4WKSmCAuky44cXmd
-----END CERTIFICATE-----
subject=/CN=li190-250.members.linode.com
issuer=/CN=li190-250.members.linode.com
---
No client certificate CA names sent
---
SSL handshake has read 1714 bytes and written 637 bytes
---
New, TLSv1/SSLv3, Cipher is DHE-RSA-AES256-SHA
Server public key is 2048 bit
Secure Renegotiation IS supported
Compression: NONE
Expansion: NONE
SSL-Session:
    Protocol  : SSLv3
    Cipher    : DHE-RSA-AES256-SHA
    Session-ID: D3AABB4FE2E174F2652C17D3B277D9BDFD330B480714B34DBACA64DE6E64597F
    Session-ID-ctx:
    Master-Key: 72DF2AF677BBED1AF646B10C5DCEEACB206A5F0CD51E9708969AE175CC69440EE59EF3BBCAE2579E8EB677EB7BA78E0D
    Key-Arg   : None
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    Start Time: 1471494098
    Timeout   : 300 (sec)
    Verify return code: 18 (self signed certificate)
---
```

It paused here, so like before I'm going to input the password of the current level giving me:

```sh
BfM**************************
HEARTBEATING
read R BLOCK
read:errno=0
bandit15@melinda:~$
```
There's the hint, which means we're getting close: HEARTBLEEDING, with some R BLOCK...

Running the same command above but with a additional flag to ignore.

```sh
bandit15@melinda:~$ openssl s_client -ign_eof -connect localhost:30001
.
.
.
.
Verify return code: 18 (self signed certificate)
---
BfM**************************
Correct!
cluFn7wTiGryunymYOu4RcffSxQluehd

read:errno=0
bandit15@melinda:~$
```

There we have it!

## Level 16 - 17

[Hint](http://overthewire.org/wargames/bandit/bandit17.html):

> The credentials for the next level can be retrieved by submitting the password of the current level to a port on localhost in the range 31000 to 32000. First find out which of these ports have a server listening on them. Then find out which of those speak SSL and which don’t. There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.

We can just blindly send every potin that range the current password but lets not get too crazy! So lets use `nmap` and it's reference can be found [here](https://nmap.org/book/man.html). Alright lets scan networks.

```sh
bandit16@melinda:~$ nmap -sV -p 31000-32000 localhost

Starting Nmap 6.40 ( http://nmap.org ) at 2016-08-18 05:12 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00054s latency).
Not shown: 996 closed ports
PORT      STATE SERVICE     VERSION
31046/tcp open  echo
31518/tcp open  msdtc       Microsoft Distributed Transaction Coordinator (error)
31691/tcp open  echo
31790/tcp open  ssl/unknown
31960/tcp open  echo
1 service unrecognized despite returning data. If you know the service/version, please submit the 

Service detection performed. Please report any incorrect results at http://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 92.45 seconds
```

```sh
bandit16@melinda:~$ openssl s_client -ign_eof -connect localhost:31790
CONNECTED(00000003)
depth=0 CN = li190-250.members.linode.com
verify error:num=18:self signed certificate
verify return:1
depth=0 CN = li190-250.members.linode.com
verify return:1
---
Certificate chain
 0 s:/CN=li190-250.members.linode.com
   i:/CN=li190-250.members.linode.com
---
Server certificate
-----BEGIN CERTIFICATE-----
MIIC3jCCAcagAwIBAgIJAI5QiWZw4YHbMA0GCSqGSIb3DQEBCwUAMCcxJTAjBgNV
BAMTHGxpMTkwLTI1MC5tZW1iZXJzLmxpbm9kZS5jb20wHhcNMTQxMTE0MTAyODA0
...
AQCaZdUNAj8WDEKWdoU3LNXUBJlTJwiWBrh550PbHSQORcCz2K0kiMei1A4ojK2N
dMHFGAqAeUEaxtz92p2BoFpZasAtdSa3u63tBckFhfUolIS1TC7Cj51y19ysTeep
fGPFpuPCVqVPsruei8Z/iqn3bFIhQQdmumeePZQdPMwZSWHNVYC5XODd7PvNDrDu
5MZJjkz4+6LbwwAvyew62meFN2QEsYbK2Brtbhze+IjE27FGWlSw4K3jlwa409MD
MTf4JU41ELaYY8G/LSNDJsBVhhkHzvXR9iCbXxNz3IL0dQDNj7h4LKhBy0q7hvqg
kDzwlmBO4WKSmCAuky44cXmd
-----END CERTIFICATE-----
subject=/CN=li190-250.members.linode.com
issuer=/CN=li190-250.members.linode.com
---
No client certificate CA names sent
---
SSL handshake has read 1714 bytes and written 637 bytes
---
New, TLSv1/SSLv3, Cipher is DHE-RSA-AES256-SHA
Server public key is 2048 bit
Secure Renegotiation IS supported
Compression: NONE
Expansion: NONE
SSL-Session:
    Protocol  : SSLv3
    Cipher    : DHE-RSA-AES256-SHA
    Session-ID: 7330F183D852D3BB3515F6E28656B4EFDA1B4720FF9347D34987263FB39D1B2E
    Session-ID-ctx:
    Master-Key: 8C313C2BECBDC7C28A6C72027AE4F9EEA66C798DD1B528F8A4B47456859B3D655EE95CB49E93174697431FEE751B62E6
    Key-Arg   : None
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    Start Time: 1471497314
    Timeout   : 300 (sec)
    Verify return code: 18 (self signed certificate)
---
cluFn7wTiGryunymYOu4RcffSxQluehd
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
....
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----
```

Wow, we got a key, lets make it and connect it the next level via ssh

```sh
bandit16@melinda:/tmp/jxn16$ nano key.private
bandit16@melinda:/tmp/jxn16$ chmod 600 key.private
bandit16@melinda:/tmp/jxn16$ ssh -i key.private bandit17@localhost
Could not create directory '/home/bandit16/.ssh'.
The authenticity of host 'localhost (127.0.0.1)' can't be established.
ECDSA key fingerprint is 05:3a:1c:25:35:0a:ed:2f:cd:87:1c:f6:fe:69:e4:f6.
Are you sure you want to continue connecting (yes/no)? yes
...
bandit17@melinda:~$ cat .bandit16.password
cluFn7wTiGryunymYOu4RcffSxQluehd
```

and there we have it, our next level!



## Level 17 - 18

[Hint](http://overthewire.org/wargames/bandit/bandit18.html):
> There are 2 files in the homedirectory: passwords.old and passwords.new. The password for the next level is in passwords.new and is the only line that has been changed between passwords.old and passwords.new
> Commands you may need to solve this level
> `cat`, `grep`, `ls`, `diff`