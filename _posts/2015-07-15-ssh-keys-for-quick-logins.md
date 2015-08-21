---
layout: post
title: Generating SSH keys for quick logins
---

How to generate a Key Pair for authentication without password when login to your server's terminal. No need to remember passwords when you connect to a server or multiple servers. If you disable password authentication on your server and only to use keys, you eliminate brute-force password attacks and thus saving resources for serving web pages.

## Generate Keys

At your local computer, generate keys

```bash
$ ssh-keygen -t rsa -b 2048 -v
```

Enter filename for your key. It will generate two files ie keyfile.pub and keyfile (without extension). 
You will be asked a passphrase, you can ignore it or enter your desire passphrase.

Rename keyfile to keyfile.pem

```bash
mv keyfile keyfile.pem
```

## Upload keys to remote server

In the same directory where your keys are

```bash
# check server port setting, this example ssh port is 45667
# if using cloudflare services, use ip address
$ ssh-copy-id username@xxx.xxx.xxx.xxx -p 45667
```

Key in password, message will appear that key is added

Test login

```bash
ssh username@xxx.xxx.xxx.xxx -p 45667
```

## Add entry to ssh config

```
Host domain.com
Hostname 1.1.1.1
Port 45667
User username
IdentityFile ~/path/to/file
```

## ssh login test

In your local computer, key in

```
ssh domain.com
```