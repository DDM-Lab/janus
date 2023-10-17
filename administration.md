## Janus Administrative Tasks ##

This documents a few easy administrative tasks on Janus, that require root access.

These tasks are all done through the command line interface.
If you are not familiar with this, there are lots of resources on the web for learning about
it. One possibility is [Canonical’s “The Linux command line for beginners”](https://ubuntu.com/tutorials/command-line-for-beginners#1-overview). However, you’ll need to ignore much of what is in section 3 of this tutorial,
as it is aimed at someone logging into a Linux machine from its console, and Janus should generally
only be logged into over SSH.

To login to Janus you’ll need a terminal window on your own machine connected to the network. How you do
that depends upon the operating system of you own machine, but it should be straightforward.
In this window, if your user name is, say, “gandalf,” simply type

    ssh gandalf@janus.hss.cmu.edu

You will be asked for your password, and after supplying it you should be in, and will type the
commands described below into this window. It is worth nothing that any commands that need
root privileges must be preceded by the magic word `sudo ...` which simply says “run this command
as if I were the Super User.” The first time you do this after logging in, or after a long pause, you
will be asked for your password again. Note also that everything in the commands quoted below
is case sensitive; if you use the wrong case at best things won’t work correctly, and at worst they
may do something shockingly unlike what you intended, which can be particularly undesirable when
running with root privileges.

### Adding a user ###

Say we want to add a user, whose real name is “Frodo Baggins” and we want them to have the user name `frodo`.
Here are the steps we’ll need to go through to add them. It is a fairly complicated dance with many steps.
Note that in every case `frodo` needs to be replaced by the new user name you really want to create.

Note that user names should *always* be a combination of *lower* case letters and digits, and always start with a lower
case letter. In a slightly awkward wrinkle, they also should never be interpretable as a hexadecimal number; for
example, do *not* use or `deadbeef` `abba1972` as user names.

**Step one**: figure out an initial password. This should be something not guessable, ideally at least eight characters,
including at least one each of upper case letters, lower case letters, digits, and non-alphanumeric characteers, without
any long strings of the same character or characters in a clear ascending or descending order.

**Step two**: create their account by doing

    sudo adduser frodo

You will then be asked for the initial password, twice. If they are both typed the same, you will
then be asked for a little further information. It is recommended that at least the users full name,
in this case “Frodo Baggins,” be supplied things like room number and telephone number are optional,
and unlikely to be useful, but it’s harmless to either supply or omit them. If all goes well, the
user is now created, along with a default group of the same name as the user name (*ie.e* in this case
`frodo`, as well as their login directory and default shell.

**Step three**: add this user to the `ddmlab` and `www-data` groups

    sudo usermod -aG ddmlab,www-data frodo

**Step four**: create a subdirectory of `~ddmlab/experiments/` for this user, and adjust its permissions
so the new user owns it and it is in the `ddmlab` group

    sudo mkdir ~ddmlab/experiments/frodo
    sudo chown frodo ~ddmlab/experiments/frodo
    sudo chgrp ddmlab ~ddmlab/experiments/frodo

**Step five**: allow this user to access Janus over SSH; we do this by creating a file in
`/etc/ssh/sshd_config.d/`.

    sudo echo 'AllowUsers frodo' > /etc/ssh/sshd_config.d/frodo.conf

Note that you need to replace “frodo” in two places in that command, one the content going into
the file and the other the name of the file.

**Step six**: restart the SSH daemon to make the preceding configuration change take effect

    sudo systemctl restart ssh

**Step seven**: tell the new user their account is now available, by copying the following into a mail
message to the new user, obviously replacing “frodo” by their new user name.

> I’ve created an account on Janus for you, with the username frodo.
>
> BEFORE accessing this account please be sure to read the Janus documentation at
> https://github.com/DDM-Lab/janus.
>
> I will send you your initial password in a separate message. You should change it to something
> different, please, following the instructions in the Janus document. Please be sure to make your
> new password something sufficiently secure and difficult to guess.
>
> Your account is a member of the ddmlab and www-data groups, and, in addition to your normal
> home directory there is directory owned by you in ~ddmlab/experiments, also named “frodo”.

**Step eight**: send the new user a second message containing just their initial password. This message
should ***not*** include the word “password” anywhere in it, as the whole purpose of sending it
separately is to reduce the risk of someone else learning their initial password before they’ve had
a chance to change it.

### Changing a user’s password ###

For security reasons even with root privileges, it is impossible to look up a user’s current password.
If a user forgets their password, or it is compromised, we must change it to a new one. This is easy,
and is similar to how a user changes their own password, the main difference is by running as root
we no longer have to type their old password to set a new one. If the user’s username is `frdo`, simply
run

    sudo passwd frodo

You will be asked to type the new password twice, and then the job is done.

### Removing a user ###

When someone leaves the group and we wish remove their ability to login to Janus we normally
disable their account, and archive the home directory in case we need to consult it in the future.
Here are the steps for a user names `frodo`.

**Step one**: disable the account.

    sudo usermod -L -e 1 -s /usr/sbin/nologin frodo

**Step two**: remove the configuration file allowing them to SSH to Janus.

    sudo rm /etc/ssh/sshd_config.d/frodo.conf

**Step three**: restart the SSH daemon to make the preceding configuration change take effect

    sudo systemctl restart ssh

**Step four**: archive their home directory.

    sudo tar -cjf /home/former-users/frodo.tgz /home/frodo --remove-files

Note that the user name appears twice in the above. Also, depending upon how much is in the former
user’s home directory this command can take a *long* time to complete; do not interrupt its progress.
If you do not want to wait you can instead run

    sudo tar -cjf /home/former-users/frodo.tgz /home/frodo --remove-files &

Note that this differs only in being followed by an ampersand. In this case, though, be
sure *not* to log out until the command has finished. When it does finiah, it should print a
message to that effect. If you think you’ve missed that message you can query what’s going
on by doing

    pgrep -a tar | grep frodo

and noting whether or not that prints something that looks like the command you started.

### Restarting nodeGame ###

When someone makes a change to a nodeGame game the nodeGame server must be restarted, which
requires root privileges. So the usual routine would be for the user to make those changes,
and then ask someone with root privileges to restart it, which be done with

    sudo systemctl restart nodegame

Note that this typically takes about two minutes to complete, and should not be interrupted.
