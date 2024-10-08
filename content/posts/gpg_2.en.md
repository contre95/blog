---
title: Dealing with your private key
date: 2020-04-25  00:15:55
tags:
    - GnuPG
    - Security
---
![Intro](/images/posts/gpg_2_banner.jpg)

Now that you've managed to create your own GPG key it's time to understand a bit more about how GnuPG works.

The first question we need to ask is where are our keys stored. The answer depends on which version of GnuPG you are running. From version 2.1 onwards the method of storing your keys [has change](https://gnupg.org/faq/whats-new-in-2.1.html). On previous versions our keys were stored in two separate keyring under the `~/.gnupg` directory. Prior version to 2.1 used to keep the public key pairs in two files: `pubring.gpg` and `secring.gpg`, you can imagine which stores which.

Now, the file ***secring.gpg*** is not used to store the secret keys anymore. Instead they are stored on the key store of the *gpg-agent* (a folder named `private-keys-v1.d` below the GnuPG home directory `~/.gnupg`) with an unique identifier for the key called "*Keygrip*" as the name of the file (i.e `~/.gnupg/private-keys-v1.d/{KEYGRIP}.key`).Additionally, the `pubring.gpg` file has been replaced with `pubring.kbx` which remains under the GnuPG directory. This "*database*" stores all of our friend's keys, metadata and certificates. We might wanna backup this file.

Know that we know where our (I bet passphrase encrypted) private key is stored, we naturally ask the next question. *Is it safe down there?* Well.. it's passphrase protected, right? Depends on the degree of securit/privacy obsession you can handle. Let's take a look at this comment on https://security.stackexchange.com/.

*On the days when my paranoia is like a ripe tomato, begging me to pick it, I split the private key (naturally it is already passphrase-protected) in half, then make a 3rd string by XOR-ing them together. Then I use simple password encryption (gpg --symmetric) on each string, and put each on a remote server on a different continent. Ideally, each remote server is with a different ISP or cloud provider.*
*But as the medicine was working -- at least until I realized how ambitious the NSA has been -- what I've actually done in the past is merely encrypted the (whole) private key (again using gpg --symmetric) and put it on my smartphone.*
*Now, having read the other answers, I'm finding the idea of three QR codes, embedded into three family photos, blindingly attractive. Time for stronger medicine?*

*Source: https://security.stackexchange.com/questions/51771/where-do-you-store-your-personal-private-gpg-key*

Whether you want keep your private key on your local (probably proprietary) machine or do what our dear friend [Darren](https://security.stackexchange.com/users/31291/darren-cook) did, it's on you. I will show how to import, backup and retrieve/restore that private key as I think is the most secure and practical way.

First, we need to somehow backup our keys in case we lose them. I suggest exporting the private key and print it on a piece of paper. You would not need the backup unless you somehow lose your private key. To do so the following command might help you.

```
$ gpg --armor --export-secret-key fula@fuladetal.xyz
-----BEGIN PGP PRIVATE KEY BLOCK-----

lQdGBF6kqnEBEAD3TDT/BfbKQE4aqOSmSAsLCsQ8HCt3HATXPBgDWhSUzy3xyQsl
x4oHGCnOpG5bBaUF3LRZh5GFsQovjfMVy11JeFcNkJO3eJRvwGgS98CiKW72HI+/
							{ . . . }
3K/UBeKAtimIZagOWBpwX9OJehVcFwws4ToCshnyio2rhU79HWutJFtls/oE8HJc
Rc4Y9PMRfu7nuiCcNdc9Lp1BiaTymnQDLECi3bNtstZEnUGkzgvGwTX7DAZ5BDlY
KMBBP60EgSDpp1eG+4z8M/0O9NV2TQ==
=AXh6
-----END PGP PRIVATE KEY BLOCK-----
```

Print that on paper and you'll be good to go. Now that you've backed up your keys, let's see how can we export them and use them on different devices. There are several ways we can achieve this.

**Having multiple copies of our private key**

A simple and fast solution, if we don't mind having our keys in multiple devices, is simply export them with into a file:

```shell
gpg --armor --export-secret-key fula@fuladetal.xyz > private.key
```

and then just import the gpg on the other machine with:

```shell
gpg --import private.key
```

This is far from the safest option but it lets us decrypt no matter on what device we are working on.

**Store it on an USB stick**

Another option is to simply store it on an USB stick. This way you can have your private key with you always and there's no need to store it on any machine we don't trust. I haven't tried this solution myself any further than a PoC to be sure it works.

Basically, what you want to do first is copy the file on `~/.gnupg/private-keys-v1.d` on an USB stick.

```shell
mv ~/.gnupg/private-keys-v1.d/{KEYGRIP}.key /PATH/TO/USB
```

*Be aware that moving these files from your machine will require to have the USB stick plugged when you need to decrypt something.*

You will probably find more than one `{KEYGRIP}.key` file, each of them corresponding to a subkey. We can know which one belongs to which by simply listing your key this way: `gpg -k --with-keygrip`

Now that you've copied the files into the USB stick you will need to create a symbolic link.

```shell
ln -s ~/.gnupg/private-keys-v1.d/{KEYGRIP}.key /path/to/usb/{KEYGRIP}.key
```

We will need to do this for every single subkey we want to export. Once you are done check you can access these key (with the USB stick plugged) running `gpg -K` . Bare in mind that you should always `mount` your device on the same path, otherwise the sym-links won't work.

**Note:** *You will need to import your public key as well if you want gpg to recognize your keys. They need to be part of the pubring.kbx database. [Source](https://gnupg-users.gnupg.narkive.com/fZ0hDcpy/how-restore-backuped-gnupg-private-keys-v1-d)*.

**Yubikey or SmartCard**

Finally, in my opinion the most secure/practical trade off for storing your private key is a Yubikey. For those who doesn't know a YubiKey is a hardware authentication device manufactured by [Yubico](https://www.yubico.com/) that supports one-time passwords, **public-key cryptography** and authentication, and the Universal 2nd Factor and FIDO2 protocols. 

A detailed step by step on how to export your secret key to a *Yubi* will be the starting point of part 2 of this GnuPG series.


### Bibliography and technology used in this project

* [Narkive Malinglist Archive](https://gnupg-users.gnupg.narkive.com/fZ0hDcpy/how-restore-backuped-gnupg-private-keys-v1-d)
* [Security Stackexchange](https://security.stackexchange.com/questions/51771/where-do-you-store-your-personal-private-gpg-key)
* [RFC 4880](https://tools.ietf.org/html/rfc4880)

---------------------------

**Author:** Lucas Contreras

**Leave a comment:** [Join the Reddit discussion](https://www.reddit.com/r/notanothertechyblog/)

