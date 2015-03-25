---
layout: post
title: Securely Transferring a Directory
author: Miles Steele
description: How to encrypt and transfer a directory using gpg and tar.
---

Have a directory that you would like to you would like to securely share with someone else?
This post describes how to encrypt a directory and protect
the file with a password for transfer using `tar` and `gpg`.

<div></div><!--more-->

To find this useful you:

- Have a directory full of secrets
- Want to share it with someone else
- Only people with the password should be ablet to decrypt it

You will need:

- A internet-connected machine to host the encrypted file.
- Another secure way to distribute the password.
- `gpg` installed on both computers.

If you don't have `gpg` set up already, check out [How To Use GPG](https://www.digitalocean.com/community/tutorials/how-to-use-gpg-to-encrypt-and-sign-messages-on-an-ubuntu-12-04-vps).
You may not need to set up a private key for this task.

## Encrypting and uploading the directory

Let's say you have some secrets in `./secrets/` and want to share that directory.
We will use `tar` to bundle the directory into a single file and `gpg` to secure
it with a password.

Create the (unencrypted) archive:

    $ tar -cvzf secrets.tar.gz secrets

- `-c` means create a new archive
- `-v` means be verbose and list files as they are packed
- `-z` means compress the file using `gzip`
- `-f` means write output to the specified file

Encrypt the tarball using `gpg`:

    $ gpg -c secrets.tar.gz

- `-c` means use a symmetric cipher, i.e. a password instead of keys.

This will create the password-encrypted file `secrets.tar.gz.gpg`.

Now upload the file to your server:

    $ scp secrets.tar.gz sleuth@computerti.me:/var/www/transfer/

Don't forget to let the recipient of your files know the password
over some other secure channel.

## Downloading and decrypting the directory

Receiving the file is a bit easier.
The receiver still needs `gpg` to decrypt the file.

Download the encrypted file:

    $ wget https://computerti.me/transfer/secrets.tar.gz.gpg

Decrypt and pipe the output through the `tar` to unpack.

    $ gpg --output - secrets.tar.gz.gpg | tar -xvz

The secrets should now be in `./secrets/`

Note that `tar` will replace existing files with the version
from the tarball. So you may want to be cautious and do this
from inside a new directory.
