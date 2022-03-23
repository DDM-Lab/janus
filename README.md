# ![Janus image](https://github.com/DDM-Lab/janus/blob/main/janus.png) Janus

> **DANGER, WILL ROBINSON, DANGER!**
>
> **Janus is NOT yet backed up regularly. This is definitely on the to do list, but not yet implemented**
> **Do NOT put your only copy of anything on Janus yet; be sure you have a copy somewhere safer, too**
> **It is highly recommended, both now and even after backups are implemented, that any code you run**
> **on Janus be stored in the DDMLab’s GitHub world. And that you rapidly extract to your own,**
> **backed up machine any data collected or generated on Janus.**

Janus (`janus.hss.cmu.edu`) is the DDMLab’s new (early 2022) server. It is a 64 core 256 GB Linux box with
an 8,604 CUDA core graphics card.
As of early 2022 Janus is running Pop!_OS version 20.04 LTS, which is essentially the same as
Ubuntu 20.04 LTS. It is expected that the OS version will be upgraded biennially.
Janus is located in the DDMLab in Porter Hall, though physical access to the machine should rarely be required.

Janus is intended to be used in two, different ways, that to some extent compete with one another:
* a web server for running online experiments
* and a compute server for running resource intensive simulations.

It is important that we don’t let these two uses of one machine interfere with one another.
The primary responsibility for this is on those running resource intensive applications,
typically simulations of one sort or another, who need to ensure they leave sufficient resources
available for online experiments, whose needs are typically more modest. More on this below.

> **NOTE**
>
> In what follows mention may be made of various bits of software installed on Janus.
> As of 22 March 2022 these may be merely aspirational, as both this document and
> the configuration of Janus remain works in progress.
> That is, Janus is still being configured, and not everything intended is installed yet.
> Once they have been installed, this warning note will be removed from this document,
> but while it remains do not depend upon all these being available. If you need them,
> sorry for the delay.

## User accounts on Janus

Coty would like to limit the number of Lab members who have accounts on Janus, and
those she would like initially have already been created. But over time folks will
come and go from the lab and new users will need accounts and old accounts will
disappear.

Typically folks will access Janus through SSH. For example, a user with username gandalf might login with
```
ssh gandalf@janus.hss.cmu.edu
```
and then be prompted for a password to finish logging in.
When a new account is created the user will be sent an initial password, typically in a separate, cryptic mail message
that does not mention the account or server name. This should be changed at the earliest
convenience. Please be sure to change it to something not easily guessed. Ideally it should be at least eight
characters long and contain all of upper case letters, lower case letters, digits, and non-alphanumeric characters,
though such a format is not currently enforced. To change your password, after logging in with your current one
use the `passwd` command on Janus.

There are undoubtedly GUI alternatives to the ssh command
that can be used from various platforms if preferred. And setting up `ssh_keys` to allow easily
logging in without having to type a password is recommended as it is generally more secure
than logging in with a password.

All users are members of the `ddmlab` group which may be useful for easily sharing write access to files
that might otherwise be difficult to share. In addition, those users who it is anticipated will be working
on online experiments, as opposed to simulations, are members of the `www-data` group.

Please do not share login credentials with others. When trouble arises, it really is best if we can
track who did what to most expeditiously resolve problems. Multiple people masquerading as the same
user both step on each other’s feet and confuse things.

In addition to current users, there are also archives of former lab members who had accounts on the old
machine, Halle, but have left the group, in `/home/former-halle-users`.

If you are new to Unix, there is a wealth of information about it available online. Note that you’ll typically
be interacting with Janus from the command line, so you may want to focus on tutorials and so on that concentrate
on that, rather than those aimed at GUI users of personal Linux machines. You may also find searching for
the more general Unix command line rather than the more specific Linux; while there are differences between
different Unix implementations, the similarities dominate the differences.

If you are new to writing online experiments here is
[some possibly useful advice](https://github.com/DDM-Lab/janus/blob/main/web-apps.md).

There is a [janus-users mailing list](https://lists.andrew.cmu.edu/mailman/listinfo/janus-users),
to which users and other interested lab members are
subscribed. Besides announcements of various Janus issues, please also use it to coördinate use of Janus
that may impact others. For example, you might send a message saying something like,

> I’m going to be running a resource intensive simulation for about the next 48 hours, and so it will
> be difficult for others to use massive resources on Janus during that time. Please let me
> know if this will be a problem. I will send another message as soon as I’m done.

And be sure to send that promised followup! And note that, as discussed further below, if you
are doing something like what is described in this message you should take steps to ensure it
does not conflict with ongoing on-line experiments run on Jason simultaneously.

Two notes on the mailing list:
* messages to it are archived; this may help in tracking down any problems that arise in the future
* folks who do not have accounts on Janus, but are otherwise interested in its use are encouraged
to subscribe to the list.

## Best practices

Always bear in mind that Janus is a shared resource, both shared between multiple lab members and
shared between the use cases of online experiments and big simulations.

By and large, use of Janus should be limited to what it’s both intended for and good at: being a web
server and an effective compute server. As far as practical, the development of experiments or models
should be done on your own machine, or some other suitable one, and only moved to Janus when ready
to be deployed, or to be run in a fashion requiring massive resources.

In particular, it is best not to use Janus as the primary repository of your code.
The principal, defining version of your software should generally be in the DDMLab GitHub space
(https://github.com/DDM-Lab), and be cloned to Janus when you are ready to use it there.
This both ensures a consistent backup of all versions of your work, and simplifies moving
code between your local machine and Janus to simplify development and minimize the workload
on Janus.

### Resource intensive simulations

TODO limit to no more than 56 cores, no more than 20GB memory. Note that top is your friend.

### Ports

### Python

Please do not install any Python versions or libraries globally, use virtual environments.
This ensures that we do not break any of the system software that depends upon Python, and
lets us each install locally for our own use exactly the versions of things we want without
stepping on one another. This is particularly important in Python which has a long tradition
of lots of possibly incompatible versions of things.

One popular way of manipulating virtual environments is [Miniconda](https://docs.conda.io/en/latest/miniconda.html).

Be sure to include a `requirements.txt` file with your code in GitHub, and keep it current
with whatever versions of various packages you are using. Not only will anyone picking up
your code in five years thank you, you will thank yourself, too.

If you want to use something like TensorFlow, Jason has a relatively high end NVIDIA graphics card
(10GB GeForce RTX 3080; note that this is *not* the GeForce RTX 3080 Ti). No supporting software
for such use is yet installed on Janus. It is reputed to be a bit finicky to get right, but
should certainly be possible.

#### Reverse proxy for Python web applications

### PHP

### JavaScript

The version of node.js current in the OS version is installed (as of 22 March 2022 this is version 12.22.5).
This typically lags behind the very latest and greatest available, but is stable and well tested.
It will typically be upgraded sometime after a new version appears in the OS repositories, and almost
certainly whenever the OS is upgraded.

If for some reason you need a newer version of node.js, please discuss it on the `janus-users` mailing list
before installing it and overwriting the existing version.

TODO discuss npm

#### nodeGame

### SQLite

The version of SQLite 3 current in the OS version is installed (as of 22 March 2022 this is version 3.33.0).
Note that it must be called as `sqlite3‘, not just `sqlite`, which is obsolete and rarely used.
This typically lags behind the very latest and greatest available, but is stable and well tested.
It will typically be upgraded sometime after a new version appears in the OS repositories, and almost
certainly whenever the OS is upgraded.

If for some reason you need a newer version of sqlite3, please discuss it on the `janus-users` mailing list
before installing it and overwriting the existing version.

### MySQL

### R

#### ShinyIBL

## Why the name Janus?

Janus is the ancient Roman god of beginnings, gates, transitions,
time, duality, doorways,[2] passages, frames, and endings, and thus by implication
of decisions. He is also typically depicted with two faces, just as janus.hss.cmu.edu
has its two faces, online experiments and resource intensive simulations.
