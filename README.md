# ![Janus image](https://github.com/DDM-Lab/janus/blob/main/janus.png) Janus

Janus (`janus.hss.cmu.edu`) is the DDMLab’s new (early 2022) server. It is a 64 core 256 GB Linux box with
an 8,604 CUDA core graphics card.
As of September 2022 Janus is running Pop!_OS version 20.04 LTS, which is essentially the same as
Ubuntu 20.04 LTS; we expect to update it to Pop!_OS 22.04 LTS (the moral equivalent of Ubuntu 22.04 LTS) by the end
of 2022. It is expected that in general the OS version will be upgraded biennially.
Janus, and the disk to which its nightly backups are written, are on a UPS (Uninterruptible Power Supply);
while this means Janus can be expected not to go down if there’s a power outage no longer than the duration the UPS’s
battery can supply its power needs, which should be a couple of hours, it doesn’t mean Janus will be usable during
a power outage: odds are CMU’s network will be down when the power is out, so there will be no way to
get at Janus.
Janus is located in the DDMLab in Porter Hall, though physical access to the machine should rarely be required.

Janus is intended to be used in two, different ways, that to some extent compete with one another:
* a web server for running online experiments
* and a compute server for running resource intensive simulations.

It is important that we don’t let these two uses of one machine interfere with one another.
The primary responsibility for this is on those running resource intensive applications,
typically simulations of one sort or another, who need to ensure they leave sufficient resources
available for online experiments, whose needs are typically more modest. More on this below.

Note that the name `ddmlab.com` also points at Janus, and for most purposes is interchangeable
with `janus.hss.cmu.edu`.

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
machine, Halle, but have left the group, in `/home/former-halle-users/`.

If you are new to Unix, there is a wealth of information about it available online. Note that you’ll typically
be interacting with Janus from the command line, so you may want to focus on tutorials and so on that concentrate
on that, rather than those aimed at GUI users of personal Linux machines. You may also find searching for
the more general Unix command line useful rather than the more specific Linux; while there are differences between
different Unix implementations, the similarities dominate the differences.

If you are new to writing online experiments here is some
[possibly useful, though opinionated, advice](https://github.com/DDM-Lab/janus/blob/main/web-apps.md).

There is a [`janus-users` mailing list](https://lists.andrew.cmu.edu/mailman/listinfo/janus-users),
to which users and other interested lab members are
subscribed. Besides announcements of various Janus issues, please also use it to coördinate use of Janus
that may impact others. For example, you might send a message saying something like,

> I’m going to be running a resource intensive simulation for about the next 48 hours, and so it will
> be difficult for others to use massive resources on Janus during that time. Please let me
> know if this will be a problem. I will send another message as soon as I’m done.

And be sure to send that promised followup! And note that, as discussed further below, if you
are doing something like what is described in this message you should take steps to ensure it
does not conflict with ongoing on-line experiments run on Janus simultaneously.

Two notes on the mailing list:
* messages to it are archived; this may help in tracking down any problems that arise in the future
* folks who do not have accounts on Janus, but are otherwise interested in its use are encouraged
to subscribe to the list.

## Backups

Janus is backed up to an attached, external disk in the wee hours every day.
If you want to copy anything out of the backup, have a look at `/backups/`.
Please do **not** delete or modify anything on that disk, however!

Three important warnings:
  * The backup only happens once a day. Anything changed or added since that last backup has **not** yet been backed up.
  * The backup disk is on the desk right above Janus. If Janus is taken out by, say, a flood, fire or theft,
    odds are the backup disk will be, too.
  * It’s all still just computer hardware and software, so things could go wrong with the automated backup
    and we might not find out about it for a while.

In other words, be careful. If you have really precious data, periodically copy it to multiple other places,
geographically remote from the DDMLab.

## Web server

The Apache HTTP web server, version 2.4.41, is installed on Janus, and serves HTTP through port 80.
We do not yet support TLS (transport layer security, aka HTTPS), but may obtain the necessary certificates and serve
it through port 443 if a need arises. As of right now, there does not appear to be such a need.

For serving simple, static files, simply place them in `/var/www/html/` and ensure they are publicly readable,
or at least readable by the user `www-data`, though once you’re serving them publicly it is hard to imagine
why you’d not let them be publicly readable. On the other hand, do **not** make them publicly writable.

CGI scripts can be placed in `/usr/lib/cgi-bin/`. Ensure they are executable.

## Best practices

Always bear in mind that Janus is a shared resource, both shared between multiple lab members and
shared between the use cases of online experiments and big simulations.

By and large, use of Janus should be limited to what it’s both intended for and good at: being a web
server and an effective compute server. As far as practical, the development of experiments or models
should be done on your own machine, or some other suitable one, and only moved to Janus when ready
to be deployed, or to be run in a fashion requiring massive resources.

In particular, it is best not to use Janus as the primary repository of your code.
The principle, defining version of your software should be in the DDMLab GitHub space
(https://github.com/DDM-Lab), and be cloned to Janus when you are ready to use it there.
This
  * ensures a consistent backup of all versions of your work,
  * simplifies moving code between your local machine and Janus to simplify development and
    minimize the workload on Janus,
  * facilitates moving your work to other machines if such a need arises,
  * and simplifies things for others who may need to work with or run your code in the future,
    possibly even after you have left the lab.

Please always include an accurate, complete and up to date README with your code on GitHub.
Just because you are the only one that needs to install and run it now doesn’t mean someone
else won’t have to at some indeterminate time in the future, so please ensure they have some
help in figuring that out. And as your code evolves, be sure to read over your README occasionally,
and update it when things have changed.

### Resource intensive simulations

Since Janus is a rather powerful machine we believe that with a little care we can use it for
multiple purposes simultaneously, but this requires some care to ensure that neither use
excludes the other.
The primary responsibility for ensure we can use Janus both for online experiments and big simulations
undoubtedly resides with those running big simulations, though if you are running an unusually
resource hungry online experiment please adhere to these guidelines, too.

Please limit your use to no more than 200 GB of memory. Memory is probably the single most important
resource to be sure not to exhaust to ensure others see a responsive machine.
Fortunately, 200 GB is a **lot** of memory.

It can be a little awkward knowing in advance how much memory you will use, though since
200 GB is so much, only the most memory intensive applications will need careful attention.
One easy way to monitor memory usage is to run `top` while your application is running.
It presents a dynamically updated display of resource usage by all the processes on the machine,
typically ordered with the current greediest first. Do `man top` on Janus to learn more.

If you plan on running something in a great many of parallel processes or threads, a good
approach is to run just one to start, run `top` to see how much memory it is using, and do
the obvious multiplication. If that number exceeds 200 GB, please reduce the number of
processes or threads you plan to use accordingly.

If memory use becomes a problem for us I believe there are technical solutions to limit consumption
by one user or login, but that seems complicated, prone to unintended consequences, and more than
what we should need. I think if we’re all reasonably careful we can cohabit peacefully without
such Draconian measures.

Another resource we want to avoid overtaxing is the number of cores. If folks limit their use
to no more than 56 cores that should leave more than enough available for concurrent users.
That is, limit yourself to no more than 56 concurrent processes or threads.

This can get a little complicated, though, when things that use, say, numpy in Python. Under the
hood numpy (and other, similar packages) might use multiple threads without making the user explicitly
aware of it. If you are running big, long running simulations that use such technology you should
probably learn how to figure out a bit more of what it’s doing behind the scenes. Google can be
your friend here; or I (dfm) can try to help, though I’m no expert in such matters. If necessary
there are ways to tell numpy and its ilk to limit how many threads they use, though it seems
complicated and will require some time both with Google and experimenting to get it all right.

Also, when running large simulations or other things that do not interact with users please
start them with `nice`. For example, instead of `python my_big_nasty_simulation.py` simply do
`nice python my_big_nasty_simulation.py`. This says “if someone else really needs cycles let
them have first priority.” In practice it will only rarely impact the performance you see, and
those rare occasions will be when you would otherwise make a mess of someone else’s efforts, likely
an online experiment. Again, `man nice` will provide some further details.

There are other resources, such as file handles, disk space, and I/O bandwidth that different uses
of Janus might contend for, but it seems unlikely they will be a problem for us. If we discover
they are, we’ll revisit how we work.

One other important consideration when running resource intensive applications on Janus is the problem
that arises when two people do so simultaneously. For example, if two folks are each using 180 GB of memory
with 48 processes, we’ve got a problem; instead one of you should run first, and then the other when
the first has finished. When you expect to be using lots of resources please coördinate
with others using the `janus-users` mailing list. And remember it is as important to say “I’m done using most
of Janus now” as it was to say “I’ve debugged by simulation running in one process and I’m about to start
it up using 56 of them, and so coming close to saturating the machine.”

If we all adhere to these guidelines we will probably cohabit on Janus with ease. If it turns out that
even with these there’s trouble, we’ll revisit what we all need to do to ensure success.

### Online experiments

In general online experiments are probably the biggest exposure of Janus to malefactors.
While we are not likely to be an appealing target, if we are not careful we will surely be
attacked, sooner or later.

To minimize our exposure, please
* run you experiment code only when needed for testing, data collection or demonstration purposes
  (demos may well need to run all the time, of course), and shut it down when not being actively
  used;
* run it as an ordinary user, **never** as root; for experiments your own userid is probably fine,
  but for demos it’s best to use the user `ddmlab`;
* and when you are no longer running an experiment, or don’t intend to do so again for a very long
  long time, if it was being served out of a dedicated port please close that port in the
  firewall — see the next section for more details on port usage

### Ports

We typically run our online experiments out of lots of different
ports. This means we need to open these ports in the firewall to
enable access. In addition, some ports need to be used for interprocess
communication within Janus; it is important that these ports **not** be
open in the firewall, as they are almost always running insecure protocols.

Since port numbers are a global name space it is
important we not step on each others’ toes in our uses of them.
Please choose your ports as follows:
* Ports numbered 1024 and lower are special and should not be used, other than
  for their designated, well known applications such as SSH, HTTP and HTTPS.
  Please do not open any ports in this range without discussing it on the
  mailing list. Note also that applications using ports in this range must
  be run, or at least started, as root, which is yet a further security complication,
  and requires extra care and discusson.
* For now, please do not use ports greater than 1024 but less than 3000.
* When exposing experiments to the cruel, outside world please use ports numbered
  3000 or greater, but less than 9000.
* When using ports for internal interprocess communication please use ports numbered
  9000 or greater but less than 10000.
* Please do not use ports numbered 10000 or greater.

To coördinate our use of ports there is in the Janus repo in the DDMLab GitHub space a
[CSV file port usage table](https://github.com/DDM-Lab/janus/blob/main/ports.csv).
Please “claim” any port you need to use here, and fill out all four fields for each.

When opening ports in the firewall please include a comment stating the purpose of the open port.
This should be the same as the Purpose column in the above spreadsheet.

Open ports are the primary source of vulnerabilities of web servers. When you no longer
need an open port (which should be in the range 3000 to 8999) please close it
(that is, delete the `ufw` rule that had allowed access to that port through the firewall),
and update the usage table. If you wish to retain a claim to the port number for
possible future reuse, please simply change its state from “open” to “closed.”

Note that as of 26 April 2022 most of the port assignments from Halle have been copied to Janus.
For some it is unclear who is responsible, and in that case the who column has been left blank for now.
Some that were open on Halle are not yet open on Janus as it isn’t clear they are being used currently.
If any that are open are no longer needed please either close them and update the sheet, or
let me (dfm) know and I’ll take care of it.

### Demos that should always be running

Most of our online experiments have relatively short lifetimes, and don’t need to automatically restart
when Janus is rebooted. In fact, it’s probably best that they don’t in case we forget to stop them
when no longer needed (but please, do remember to stop them!).

But permanent demos do need to run 7/24 The
[pm2 process manager](https://pm2.keymetrics.io/) is running on Janus, and using this is
usually the simplest way to accomplish this. New demos can be added to those started by `pm2`
simply by calling `pm2 start` with suitable arguments. However, it is important to only call
`pm2` while running as the user `ddmlab`, lest you instead start a new instance of it running
as yourself. So, to start a demo initialized by running `run-demo.sh` in the current directly, do

    sudo su -c ’pm2 start run-demo.sh’ ddmlab

For demos using node.js create a configuration file, for example `ecosystem.config.js` describing
your demo and do, while cd’ed to the appropriate directory,

    sudo su -c ’pm2 start ecosystem.config.js' ddmlab

For an example of this, see `~ddmlab/demonstrations/gridworld-game-DEMO/`

For Python, you’ll also need activate the appropriate virtual environment. For such permanent applications
and demos, Miniconda/Anaconda are not really suitable as conda environments are tied to users and require
use from a login shell. For demos and the like it is better to use `venv`. This consideration is less of
an issue for ordinary experiments where you will be starting and stopping them yourself by hand anyway.
To start such a Python based demo you will need to encapsulate the activation of the
environment and subsequent running of the application into a simple shell script.
See `/home/ddmlab/demonstrations/minimap-DEMO/start-minimap-demo.sh` for an example of how to do this.
For minimap, we added it to the list of demos to start by cd’ing to
`/home/ddmlab/demonstrations/minimap-DEMO/` and running

    sudo su -c ’pm2 start start-minimap-demo.sh’ ddmlab

To recapitulate and make more concrete the above, when setting up a Python based demo
to start automatically you’ll usually want to create a venv virtual environment, activate
it, and ensure all the packages listed in requirements.txt are include with something like

    sudo su ddmlab
    python3 -m venv .venv
    source .venv/bin/activate
    pip install -r requirements.txt

followed by `^D` to stop being the user ddmlab.
Note that `.venv` is just a name of your choice.
Then create a script, say `start-my-demo.sh` containing something like

    source .venv/bin/activate
    python run_my_demo.py

that, when run, will activate you virtual environment and then run your demo. You should make this script
executable

    chmod a+x start-my-demo.sh

and then add it to pm2’s queue of things to start

    sudo su -c ’pm2 start start-my-demo.sh’ ddmlab

There is one more important step: after adding a demo to the list of those to be started
at boot time, also call

    sudo su -c ’pm2 save’ ddmlab

This causes the newly updated list of demos to be saved for restart at boot time.

Please do **not** defeat pm2’s efforts to run your demo as `ddmlab` and try to run it
as root instead. Running a demo as root is simply a disaster waiting to happen.

Normally, unless extra steps are taken, files on Janus will be writable by their author, only, though
readable by anyone able to login to Janus. While this is sufficient for most demos to be run as ddmlab, since
most demos should have no need to write any files, it is generally more convenient to make sure anyone
in the group can write to them, too. The easiest way to do this is to set the files’ and directories’ groups
to `ddmlab` and ensure they are group writeable. If you are unfamiliar with Linux file permissions there
are innumerable references on the web, for example
[this one](https://www.tutorialspoint.com/unix/unix-file-permission.htm).
You might also want to look at the man pages for `chmod`, `chwon` and `chgrp`
(for example, do on Janus `man chmod` to learn more about that command).
Have your files and directories in the `ddmlab` group and group writable also simplifies
other members of the group updating them, often a useful thing for demos.

A commonly useful pattern for ensuring a directory and all its contents are writable
by the ‘ddmlab` group is to do the following while cd’ed to that directory:

   chgrp -R ddmlab .
   chmod -R g+w .

If you are the owner of this directory and all its contents that should work fine. If not,
you may need to precede these commands with `sudo`, though, as always, it’s best not to
use root privileges unless you really need to.

While everything we write and put on Janus should be cloned from GitHub, this is particularly
important for demos as they are a shared resource and it is important that others be able
to run them, not just you. Similarly, it is important that how to install and run them is
accurately documented, probably in a README.

### Transient experiments

For more transient experiments please have them running only when actively testing them or collecting
data with them. Always shut them down again when you are done using them. This both reduces the opportunities
for malefactors to attach Janus and minimizes any unnecessary drain of Janus’s resources.

And please run them as an ordinary user, likely either as `ddmlab` or as yourself.
But **never** as root.

If your files and directories are owned by you and you are running your experiment as yourself
all should be well. But if you are running it as `ddmlab` you may need to ensure they they have
the group `ddmlab` and are group writable, as experiments frequently need to write files to
record participant behavior. See the preceding section for how to do this. And, again, if this
may be useful if you want to simplify matters for sharing the authorship of code with other
members of the group.

### Shared waiting room

There is a shared waiting room, implemented in nodeGame, available on Janus. Documenting this
further remains a TODO here.

### Python

Please do not install any Python versions or libraries globally, use virtual environments.
This ensures that we do not break any of the system software that depends upon Python, and
lets us each install locally for our own use exactly the versions of things we want without
stepping on one another. This is particularly important in Python which has a long tradition
of lots of possibly incompatible versions of things, and is used in the system software.

Two popular ways of manipulating virtual environments are
[venv](https://docs.python.org/3/library/venv.html)
and [Miniconda](https://docs.conda.io/en/latest/miniconda.html).
As noted above, conda is not suitable for use in a systemd service file; there, please
use venv instead.

Be sure to include a `requirements.txt` file with your code in GitHub,
and keep it current with whatever versions of various packages you are
using. And after creating your `requirements.txt` try installing from
it in fresh virtual environment as a test that all works as expected;
it should only take a minute, and can save copious time in the future
as problems caught early are far easier than those months not
discovered until months in the future. Not only will anyone picking up
your code in five years thank you, you will eventually thank yourself,
too.

If you want to use something like TensorFlow, Janus has a relatively high end NVIDIA graphics card
(10GB GeForce RTX 3080; note that this is *not* the GeForce RTX 3080 Ti). No supporting software
for such use is yet installed on Janus. It is reputed to be a bit finicky to get right, but
should certainly be possible if needed.

### PHP

Version 7.4.3 of PHP is installed on Janus, using fast-cgi through the lighttpd web server.
Updating from this version may prove problematic as it appears PHP
version 8 and later include several incompatible changes which may break old PHP applications. However,
at some point we may need to upgrade it. We’ll deal with those problems when we must, but not yet.

To access PHP simply put your PHP files in `/var/www/html` or (usually better) a subdirectory thereof, and ensure
they have the extension `.php`, and access them in usual way from a web browser through port 80.

For more details of the PHP installation point a browser at [phpinfo.php](http://ddmlab.com/phpinfo.php).

### JavaScript

The latest (as of 24 March 2022) LTS version of node.js, version 16.14.2, along with npm version 8.5.8 and nodemon 2.0.15, is installed on Janus. This will be upgraded on an irregular basis, though upgrades will be announced on the `janus-users` mailing list. It will almost certainly be upgraded along with the biennial OS upgrade.

If for some reason you need a different version of node.js please discuss it on the `janus-users` mailing list. It appears that, if necessary, technologies exist so that multiple versions of node.js can peacefully cohabit one machine, though they seem awkward to install and configure.

#### nodeGame

Version 7.1.0 (the latest stable version as of 24 March 2022) of nodeGame is installed in /home/ddmlab/experiments/, and configured to start when Janus boots, serving nodeGame out of port 8080.

In an effort to keep the world on Janus cleaner and tidier than the mess we had on Halle, please use this installation of nodeGame to serve your nodeGame games, rather than installing many copies of nodeGame, each using a different port. To do so

* ensure a copy of your game is in the DDMLab GitHub space
* clone a copy into `/home/ddmlab/experiments/nodegame/games_available/<your-game's-name>`
* ensure it is readable by the `ddmlab` group; assuming the usual permissions, this can be done with
`chgrp -R ddmlab /home/ddmlab/experiments/nodegame/games_available/<your-game's-name>`
* ensure the `data` sub-directory is writable by group, too; this can be done with
`chmod -R g+w /home/ddmlab/experiments/nodegame/games_available/<your-game's-name>/data`
* create in `/home/ddmlab/experiments/nodegame/games/` a symbolic link to your game; this can be done with
`cd /home/ddmlab/experiments/nodegame/games/ ; ln -s ../games_available/<your-game's-name> ./`
* ensure, probably by using the `janus-users` mailing list, that nodeGame coming down will not impact anyone else,
and then restart nodeGame with `sudo systemctl restart nodegame`

See the existing links in `…/nodegame/games/` for examples.

The URL to direct folks to your game will be `http://ddmlab.com:8080/<your-game's-name>` (assuming
you have named your game internally to match the name in the file system; if not it is the
internally assigned name that should go into the URL). Please do **not** direct users to the top
level `http://ddmlab.com:8080/`.

Note that if you are installing a nodeGame game written against an earlier version of nodeGame there
may be some minor changes that are needed. One likely one is that the argument signature to the
`stager` function has changed. Stefano has arranged that the old functions will continue to work,
at least for a while, but it would be best to update them. This can be easily done just by changing the
line

`module.exports = function(stager, settings) {`

to

`module.exports = function(treatmentName, settings, stager, setup, gameRoom) {`

in `game.stages.js`.

### R

R version 3.6.3 is installed. Unless there is a demand it will only be upgraded infrequently.

#### Shiny Server

The R Shiny Server version 1.7.1 is also installed. Again it is unlikely to be upgraded unless there is a demand.
It serves through port 3838.
To deploy applications simply place them in an appropriately named subdirectory of `/srv/shiny-server/`.
So, for example, if your application is in `/srv/shiny-server/mumble/` users get at it with a URL
like `http://ddmlab.com:3838/mumble/`.
See `/srv/shiny-server/shinyibl/` for an example.

### SQLite

The version of SQLite 3 current in the OS version is installed (as of 22 March 2022 this is version 3.33.0).
Note that it must be called as `sqlite3`, not just `sqlite`, which is obsolete and rarely used.
This typically lags behind the very latest and greatest available, but is stable and well tested.
It will typically be upgraded sometime after a new version appears in the OS repositories, and almost
certainly whenever the OS is upgraded.

If for some reason you need a newer version of sqlite3, please discuss it on the `janus-users` mailing list
before installing it and overwriting the existing version.

### MySQL

The version of the MariaDB fork of MySQL current in the OS version is
installed (as of 26 April this is version 10.3.34), with InnoDB as the
default underlying engine. It will typically be upgraded sometime
after a new version appears in the OS repositories, and almost
certainly whenever the OS is upgraded.

Only a few current members of the lab had user accounts in MySQL on Halle, and these have
been duplicated in MySQL on Janus, with the same permissions. I have also tried to duplicate
the passwords (which are not in clear text only hashed, so I don’t actually know them), but
the way user info is configured in recent versions has changed sufficiently from the older
version on Halle I’m not sure I’ve gotten it right. Please let me (dfm) know if you have any trouble.

All the MySQL databases from Halle have also been copied to Janus. The contents of these databases
on Janus are what they were on Halle late in the afternoon of 25 April 2022. Any changes to them on
Halle since then will require updating the corresponding database on Janus. This should not be difficult.

## Why the name Janus?

Janus is the ancient Roman god of beginnings, gates, transitions,
time, duality, doorways, passages, frames, and endings, and thus by implication
of decisions. He is also typically depicted with two faces, just as janus.hss.cmu.edu
has its two faces, online experiments and resource intensive simulations.
