# ![Janus image](https://github.com/DDM-Lab/janus/blob/main/janus.png) Janus

> **DANGER, WILL ROBINSON, DANGER!**
>
> **Janus is NOT yet backed up regularly. This is definitely on the to do list, but not yet implemented**
> **Do NOT put your only copy of anything on Janus yet; be sure you have a copy somewhere safer, too**
> **It is highly recommended, both now and even after backups are implemented, that any code you run**
> **on Janus be stored in the DDMLab’s GitHub world. And that you rapidly extract to your own,**
> **backed up machine any data collected or generated on Janus.**
>

Janus (`janus.hss.cmu.edu`) is the DDMLab’s new (early 2022) server. It is a 64 core 256 GB Linux box with
an 8,604 CUDA core graphics card,
and as of early 2022 is running Pop!_OS version 20.04 LTS, which is essentially the same as
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
> In what follows mention will be made of various bits of software installed on Janus.
> As of 22 March 2022 some of these are merely aspirational.
> Janus is still being configured, and not all these things are installed yet.
> Once they have been installed, this warning note will be removed from this document,
> but while it remains do not depend upon all these being available. If you need them,
> sorry for the delay.

## User accounts on Janus

Coty would like to limit the number of Lab members who have accounts on Janus, and
those she would like initially have already been created. But over time folks will
come and go from the lab and new users will need accounts and old accounts will
disappear.

Typically folks will access Janus through SSH. For example, a user with username gandalf might login with
    ssh gandalf@janus.hss.cmu.edu
and then be prompted for a password to finish logging in.
When new account is created the user will be sent an initial password, typically in a separate, cryptic mail message
that does not mention the account or server name. This should be changed at the earliest
convenience. Please be sure to change it to something not easily guessed. Ideally it should be at least eight
characters long and contain all of upper case letters, lower case letters, digits, and non-alphanumeric characters,
though such a format is not currently enforced. To change your password, after logging in with your current one
use the `passwd` command on Janus.

There are undoubtedly GUI alternatives to the ssh command
that can be used from various platforms if preferred. And setting up `ssh_keys` to allow easily
logging in without having to type a password is recommended as it is generally more secure
than logging in with a password.

There is a mailing list, janus-users@lists.andrew.cmu.edu, to which users and other interested lab members are
subscribed. Besides announcements of various Janus issues, please also use it to coördinate use of Janus
that may impact others. For example, you might send a message saying something like

> I’m going to be running a resource intensive simulation for about the next 48 hours, and so it will
> be difficult for others to use massive resources on Janus during that time. Please let me
> know if this will be a problem. I will send another message as soon as I’m done.

And be sure to send that promised followup! And note that, as discussed further below, if you
are doing something like what is described in this message you should take steps to ensure it
does not conflict with ongoing on-line experiments run on Jason simultaneously.
