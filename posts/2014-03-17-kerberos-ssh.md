---
title: Using Kerberos Tickets with SSH
author: Miles Steele
description: Tutorial for enabling kerberos tickets on Ubuntu.
---

## Athena over SSH

MIT supports ssh access to athena machines from on
and off campus through `athena.dialup.mit.edu`.

To log in run:

    $ ssh username@athena.dialup.mit.edu

replacing `username` with your athena username.

<div></div><!--more-->

But typing in a password for every ssh login is a hassle.
Normally we would use [ssh keypairs](https://help.github.com/articles/generating-ssh-keys) but athena does not allow those.
Instead, we must use kerberos tickets.
Kerberos tickets act like temporary ssh keypairs.

## Install the Kerberos Client

These are instructions on how to set up kerberos tickets for athena on Ubuntu.
For other flavors of linux, the method should be similar.

First install the necessary kerberos-related packages:

    $ sudo apt-get install krb5-user krb5-config

when prompted for a default realm enter (in ALL-CAPS) `ATHENA.MIT.EDU`

If you miss the prompt, that's ok, just run:

    $ sudo dpkg-reconfigure krb5-config

## Getting a Ticket

Once the installation is complete it is time to get a ticket.
To get a ticket run:

    $ kinit -p username

replacing `username` with your athena username.
The default realm should turn this into `username@ATHENA.MIT.EDU`
If it doesn't try again with:

    $ kinit -p username@ATHENA.MIT.EDU

You will be prompted for your athena password to get the ticket.

To make sure that you received the ticket run:

    $ klist


You should get something like this if it worked:

    Ticket cache: FILE:/tmp/krb5cc_1000
    Default principal: username@ATHENA.MIT.EDU

    Valid starting    Expires           Service principal
    17/03/2014 14:09  18/03/2014 11:24  krbtgt/ATHENA.MIT.EDU@ATHENA.MIT.EDU
      renew until 18/03/2014 14:09

## Using Kerberos with SSH

Now you can use the key to ssh without typing a password:

    ssh -K username@athena.dialup.mit.edu

The `-K` tells ssh to delegate authentication to kerberos.

To make your life easier, you can add the following entry to your `~/.ssh/config` file.
If `~/.ssh/config` doesn't exist yet, go ahead and create it.

    Host athena
      Hostname athena.dialup.mit.edu
      User username
      GSSAPIDelegateCredentials yes

Remember to replace `username` with your athena username.

That will enable you to ssh by running:

    $ ssh athena

and it will automatically connect to `athena.dialup.mit.edu`
as `username` and use tickets for authentication.

## Renewing Tickets

Note that kerberos tickets will expire. For MIT they expire in about a day.
To renew certificates without entering a password again run:

    kinit -R

But that doesn't actually work for me, so you might just have to run:

    $ kinit -p username

and enter your athena password to get a new one every once in a while.


## Sources
- [Ubuntu Wiki - Kerberos Setup](https://help.ubuntu.com/community/Kerberos#Client_Configuration)
- [IST - SSH Delegation Tutorial](http://kb.mit.edu/confluence/pages/viewpage.action?pageId=4981397)
