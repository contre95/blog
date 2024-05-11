---
title: Getting started with GnuPG
date: 2020-04-15  00:15:55
tags:
    - GnuPG
    - Password Manager
    - English
---
![Intro](/images/posts/gpg_0_banner.jpg)

GnuPG is a complete and free implementation of the OpenPGP standard and this is just **part 0** of who knows how many series where I will explain my basic understanding of GPG and how useful is on my daily basis. If you wish to understand more in detail how GPG work please refer to the RFC or [here](https://davesteele.github.io/gpg/2014/09/20/anatomy-of-a-gpg-key/) is a great post that captures the essence of it quite deeply.

### My use case

No. I don't have [my public key](https://gpg.contre.io) on any keyserver and no I don't use it to send encrypted messages, not mostly at least. I use GnuPG to encrypt any kind of data/files I want to keep safe, for encrypting/decrypting day-to-day info I need to grab quickly within systems I use but don't fully trust, to login into some of my servers with ssh and gpg agents and last but not least to [retrieve all of my passwords securely from any device](https://www.passwordstore.org/).

Me and my stack of devices getting used to using gpg was a long journey I'm not sure it's over yet. But it started here, by creating my first GPG key pair and that's what this post is about.

### Generating a new GPG key pair

By default **GnuPG** generates one primary (also called master) key with the ability (also called flag) of signing and certifying **[SC]**.

**Certification vs. signing** - *Signing* is an action against arbitrary data (As I understand it testifying that the encrypted data is sent by who it's supposed to be sent). *Certification* is the signing of another key. Ironically, the act of certifying a key is universally called “key signing”.(As I understand it saying "*Hey this is my public key* or *Hey,  I trust this key is from who It says it is*). Just embrace the contradiction.

```bash
gpg --full-generate-key
Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
```

Let's go with the default option with indicates we are going to use the RSA algorithm and we are going to generate one primary key for *Signing* and *Certifying*  **[SC]** and other sub-key for *Encrypting* **[E]**. We will also specify a key size of ***4096*** bits and set the expiration date to 1 year from now. We will then be prompt with  interface and be asked for a passphrase.

```bash
Your selection? 1
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (3072) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 1y
Key expires at Mon Apr 12 17:43:02 2021 UTC
Is this correct? (y/N) y
```

Once we specified this we will be asked some info to identify our User ID (UID). Which is basically the name and email of the user and it is stored in one or more UID entries under the Primary key.

```
GnuPG needs to construct a user ID to identify your key.

Real name: Fulanito Detal
Email address: fula@fuladetal.xyz
Comment: Fulanito's keypair
You selected this USER-ID:
    "Fulanito Detal (Fulanito's keypair) <fula@fuladetal.xyz>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
```

After we've entered the info needed GnuPG will create a new key pair for us.

```
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: key 363690E797C0E0A7 marked as ultimately trusted
gpg: revocation certificate stored as '/root/.gnupg/openpgp-revocs.d/1D537C302A599F7BFC55C260363690E797C0E0A7.rev'
public and secret key created and signed.
```

After it's created we would an output of this kind. We can also access this info with `gpg -k` which lists our public keys or `gpg -K` to list our private keys.

```
pub   rsa4096 2020-04-12 [SC] [expires: 2021-04-12]
      1D537C302A599F7BFC55C260363690E797C0E0A7
uid                      Fulanito Detal (Fulanito's keypair) <fula@fuladetal.xyz>
sub   rsa4096 2020-04-12 [E] [expires: 2021-04-12]
```

Notice that a **revocation certificate**  has been created under `/root/.gnupg/openpgp-revocs.d/02A59{BlaBlaBla}60BH36.rev'` . If your GPG private key becomes compromised, you need to revoke it to warn others not to trust future signatures or encrypt data to your public key. However, by the time a key compromise happens, you might not have your GPG key available, such as if it resided on hardware stolen from you, or the attacker removed it after accessing it. A revocation certificate consists of a signed message, stating in machine-readable form that a key no longer has validity for future cryptographic operations. Anyone with this certificate can revoke your private key. I recommend printing it out and storing it in a secure location.

Finally, now that you managed to create your own key pair you can start sharing you public key, decrypt and sign messages. In order to get use the `gpg` cli tool I suggest checking the following cheat sheet until you get used to it. 

```bash
$ curl cheat.sh/gpg
```

### Bibliography and technology used in this project

* [RFC 4880](https://tools.ietf.org/html/rfc4880)
* [This awesome post](https://davesteele.github.io/gpg/2014/09/20/anatomy-of-a-gpg-key/)
* [This other awesome post](https://davesteele.github.io/gpg/2015/08/01/intermediate-gpg/)

---------------------------

**Author:** Lucas Contreras

**Leave a comment:** [Join the Reddit discussion](https://www.reddit.com/r/notanothertechyblog/)

