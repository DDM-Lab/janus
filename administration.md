## Janus Administrative Tasks ##

The really biggest and most important administrative task is figuring out what's gone wrong when something does go wrong.
There's no recipe for this, though!

Good things to learn about in general are, besides general Linux administration,

* ufw, the firewall

* Apache

* sadly, MySQL / MareaDB; I say "sadly" because these really are overkill for the things we typically do in the DDMLab, but folks like to use them
because they're trendy

* maybe nodeGame, which we used to use a fair bit; bit seems to have mostly fallen off the radar these days, though; note
that we have one nodeGame instance running with lots of games under it, all served over port 8080

Here are some recipes for a few simple things we've often got to do, that require root access.

These tasks are all done through the command line interface.
If you are not familiar with this, there are lots of resources on the web for learning about
it. One possibility is [Canonical’s “The Linux command line for beginners”](https://ubuntu.com/tutorials/command-line-for-beginners#1-overview). However, you’ll need to ignore much of what is in section 3 of this tutorial,
as it is aimed at someone logging into a Linux machine from its console, and Janus should generally
only be logged into over SSH.

On the other hand, if you want to use other tools for doing this there's no reason not to, so long as the end result is the same.

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
including at least one each of upper case letters, lower case letters, digits, and non-alphanumeric characters, without
any long strings of the same character or characters in a clear ascending or descending order.

**Step two**: create their account by doing

    sudo adduser frodo

You will then be asked for the initial password, twice. If they are both typed the same, you will
then be asked for a little further information. It is recommended that at least the users full name,
in this case “Frodo Baggins,” be supplied; things like room number and telephone number are optional,
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

**Step five**: create a subdirectory of `~ddmlab/experiments/nodegame/games_available` for this user, and adjust
its permissions so the new user owns it and it is in the `ddmlab` group

    sudo mkdir ~ddmlab/experiments/nodegame/games_available/frodo
    sudo chown frodo ~ddmlab/experiments/nodegame/games_available/frodo
    sudo chgrp ddmlab ~ddmlab/experiments/nodegame/games_available/frodo

**Step six**: allow this user to access Janus over SSH; we do this by creating a file in
`/etc/ssh/sshd_config.d/`.

    echo 'AllowUsers frodo' | sudo tee /etc/ssh/sshd_config.d/frodo.conf

Note that you need to replace “frodo” in two places in that command, one the content going into
the file and the other the name of the file.

**Step seven**: restart the SSH daemon to make the preceding configuration change take effect

    sudo systemctl restart ssh

**Step eight**: tell the new user their account is now available, by copying the following into a mail
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

**Step nine**: send the new user a second message containing just their initial password. This message
should ***not*** include the word “password” anywhere in it, nor the name Janus, nor the user’s user name,
as the whole purpose of sending it
separately is to reduce the risk of someone else learning their initial password before they’ve had
a chance to change it.

### Changing a user’s password ###

For security reasons even with root privileges, it is impossible to look up a user’s current password.
If a user forgets their password, or it is compromised, we must change it to a new one. This is easy,
and is similar to how a user changes their own password, the main difference is by running as root
we no longer have to type their old password to set a new one. If the user’s username is `frodo`, simply
run

    sudo passwd frodo

You will be asked to type the new password twice, and then the job is done.

### Removing a user ###

When someone leaves the group and we wish remove their ability to login to Janus we normally
disable their account, and archive the home directory in case we need to consult it in the future.
Here are the steps for a user named `frodo`.

**Step one**: disable the account.

    sudo usermod -L -e 1 -s /usr/sbin/nologin frodo

**Step two**: remove the configuration file allowing them to SSH to Janus.

    sudo rm /etc/ssh/sshd_config.d/frodo.conf

**Step three**: restart the SSH daemon to make the preceding configuration change take effect

    sudo systemctl restart ssh

**Step four**: optionally archive their home directory.

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

This step is optional, though, as for some, maybe even most, former users it's prudent to leave their stuff readily available.

### Restarting nodeGame ###

When someone makes a change to a nodeGame game the nodeGame server must be restarted, which
requires root privileges. So the usual routine would be for the user to make those changes,
and then ask someone with root privileges to restart it, which be done with

    sudo systemctl restart nodegame

Note that this typically takes about two minutes to complete, and should not be interrupted.

### Opening a port ###

Commonly folks doing online experiments need one or more ports opened.
Before asking you to do this it's best if they update the `ports.csv` document, but if they
don't it's often easier to just do it yourself rather than nagging them.
Before fiddling anything double check that

1. the port they want opened isn't in use by someone else

2. the port is in the appropriate range as specified in the Janus document

3. the port isn't one of the handful the CMU network folks block; these are noted in `ports.csv`.

Double check the state of things by doing

    sudo ufw status

If all looks well, and you want to open port 7890 for Frodo's ring project do

    sudo ufw allow 7890/tcp comment 'Frodo's ring project'

Then do a `ufw status` again to check it's all as it should be.

### Monitoring uptime ###

While YMMV, I find having a free account on [Uptime Robot](http://uptimerobot.com/) convenient for getting a notification if Janus goes down.
I just have it ping Janus every few minutes, and send me mail or a text if it isn't up.

If it appears to be down, and I confirm that by and, the first thing to do is see if anything else in CMU, ideally in Porter Hall, is also down.
More often than not if it looks like Janus is down it's actually the network, and it comes back again after a while.
Or it could be a power outage.

### Backups ###

This isn't so much a recipe as a description of what's going on.

There is a script, `/root/run-backup`, that is run as a cron job at 3:12 every morning. It makes sure a suitable disk
is mounted at `/backups/` dumps out the contents of the MySQL / MariaDB databases as text files, and then uses `rsync` to copy much of the contents
of Janus's disk to the backup. It makes copies of files that go away, too, just in case. It writes lines to `/var/log/backups.log` when it starts and
finishes.

Since it's all just straightforward copies recovering a file is simply a matter of digging into the directory structure
on `/backups/` and copying the old version.

I suggest about once a month auditing things a bit by figuring out some arbitrary file that has been changes and should have been
copied, and using `diff` to make sure its bits are the same.

There is also a little CGI script in `/usr/lib/cgi-bin/recent-backups` that can be used to ascertain when the last successful
backup completed. and can be useful for another Uptime Robot thingie to alert one to the backup disk not being properly mounted or whatever.

### Other stuff ###

PHP is installed on Janus, and some folks use it. I think there are one or two demos that do, too.

There's a CGI script for allowing folks to download stuff made available o Janus. This used to be
widely used, but I think now that we're stuffing things into GitHub its value has decreased
markedly. But it's still there.

There also a little CGI thing for a "Participant Pool." I check with Jeffrey about how much we use that these days.

The CMU network folks serve DNS for Janus. The DDMLab owns the domain `ddmlab.com` which needs to be renewed periodically.
A `whois` lookup turns up the registrar (Verisign) as all three contacts for the domain, but inside their records
they point to real people, Coty being the owner.
CMU still serves DNS for it, as an A record, not a CNAME.
It points to the same IP address as `janus.hss.cmu.edu` does.
