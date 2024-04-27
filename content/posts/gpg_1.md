---
title: GPG + SSH ?
date: 2020-06-29  13:15:55
tags:
    - GnuPG
    - SSH
    - Password Manager
    - Unix
    - English
---

This will be a small walk through on how to generate an authentication subkey on GPG and integrated with ssh. First of all bare in mind that will need a GPG key first in order to generate this subkey, if have not generated on yet refer to part 0 of these series.

### Authentication Key

In order to generate an authentication key, as I mentioned before, you will need to have a key generated. Let's check that with
```shell
$ gpg -K                                                       
/home/<USER>/.gnupg/pubring.kbx
-------------------------------
sec   rsa4096 2019-12-19 [SC]
      F0ASSDF32440E42342FDF25GHB8F9CF4EC8EFE1B
uid           [ultimate] Your Name (Comment) <yourmail@yourdomain.com>
ssb   rsa4096 2019-12-19 [E]
```

Then we will edit our key using the key ID shown above with the command `gpg --expert --edit-key F0ASSDF32440E42342FDF25GHB8F9CF4EC8EFE1B`. We'll be prompt with a GPG tty where we will be able to add a new key with "Authentication" capabilities. By default, as we have seen in `part 0`, GnuPG creates new keys with Sign and Encryption capabilities so we need to make sure to remove those since we already have keys for that purpose.
Now answer what you are prompted and you'll be good to go. I'll leave a set of steps taken from [Yubico's website](https://support.yubico.com/support/solutions/articles/15000006420-using-your-yubikey-with-openpgp) I've found very useful.

1. Insert the YubiKey into the USB port if it is not already plugged in.
2. Enter the GPG command: `gpg --expert --edit-key 1234ABC` (where 1234ABC is the key ID of your key)
3. Enter the command: addkey
4. Enter the passphrase for the key. Note that this is the passphrase, and not the PIN or admin PIN.
5. You are prompted to specify the type of key. Enter 8 for RSA.
6. Initial default will be Sign and Encrypt. To select authentication key toggle S to disable sign, E to disable encrypt, A to enable authentication.
7. Once you can confirm that authentication is the current allowed actions select Q to Finish the selection.
8. Specify the key size.
9. Specify the expiration of the authentication key (this should be the same expiration as the key).
10. When prompted to save your changes, enter y (yes).

Once you're done, run `gpg -K`. The output should look something like this.

```bash
$> gpg -K                                                       
/home/<USER>/.gnupg/pubring.kbx
-------------------------------
sec   rsa4096 2019-12-19 [SC]
      F0ASSDF32440E42342FDF25GHB8F9CF4EC8EFE1B
uid           [ultimate] Your Name (Comment) <yourmail@yourdomain.com>
ssb   rsa4096 2019-12-19 [E]
ssb   rsa4096 2019-12-27 [A]
```
*Note that now we have a new subkey with " [A] " capabilities.* 

### Integrating SSH

First of all let me clarify that using GPG does not make your SSH connections more secure, it just change a bit the way you manage your ssh keys and sometimes is a bit more convenient.
To make use of the new authentication subkey we've just created, we need to tell the `gpg-agent` we want him to handles the ssh requests from SSH. For that we need to enable ssh support by running the following command.

```shell
$> echo "enable-ssh-support" >> ~/.gnupn/gpg-agent.conf
```

Additionally you might want to specify which subkey you want to use. We do this by adding the `keygrip` of our key to `~/.gnupg/sshcontrol`

```shelL
$> gpg -K --with-keygrip
/home/<USER>/.gnupg/pubring.kbx
-------------------------------
sec   rsa4096 2019-12-19 [SC]
      F032E440E7B8F960B9A4D68BA32DDCF4EC8EFE1B
      Keygrip = 872C0BA0BB0EAB6600EE9A14146819B41DFF8B73
uid           [ultimate] Lucas Contre (Contre) <lucascontre95@gmail.com>
ssb   rsa4096 2019-12-19 [E]
      Keygrip = 96FD0AD76EB01C5475122670A0F85961C7952A1E
ssb   rsa4096 2019-12-19 [A]
      Keygrip = 037A6603BBDA3461BF672F8071037AK66ACACE34

$ echo  037A6603BBDA3461BF672F8071037AK66ACACE34 >> ~/.gnupg/sshcontrol
```

Last but not least you need to tell the `ssh-agent` how to access these keys. This is done by changing the SSH_AUTH_SOCK env variable.
To be sure this variable is well set, add these two lines to your `.bashrc`, `.zshrc` or whatever shell run commands file you use.

```
export SSH_AUTH_SOCK=$(gpgconf --list-dirs agent-ssh-socket)
gpgconf --launch gpg-agent
```

Now you're done! In order to share your public ssh keys just run `ssh-add -L` to list all of your public ssh keys and place them on any server your like. 

### Bibliography and technology used in this project

* [This awesome post](https://opensource.com/article/19/4/gpg-subkeys-ssh#:~:text=When%20you%20use%20SSH%2C%20a,gnupg%2Fgpg%2Dagent.)
* [Yubico's site](https://support.yubico.com/support/solutions/articles/15000006420-using-your-yubikey-with-openpgp)
---------------------------

**Author:** Lucas Contreras
**Email:** lucascontre95@gmail.com
**GPG:** [gpg.contre.io](https://gpg.contre.io)

