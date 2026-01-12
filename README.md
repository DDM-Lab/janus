# ![Janus image](https://github.com/DDM-Lab/janus/blob/main/janus.png) Janus

Janus (`janus.hss.cmu.edu`) is the DDMLab’s new (early 2022) server. It is a 64 core 256 GB Linux box with
an 8,604 CUDA core graphics card.
As of September 2022 Janus is running Pop!_OS version 22.04 LTS, which is essentially the same as
Ubuntu 22.04 LTS.
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

Everyone in the DDMLab, plus perhaps a few “friends of the lab,” will be provided with accounts
on Janus. But Coty would like to severely limit those with root (aka superuser or sudo) access,
which as of 17 October 2023 will just be Coty and Don.
Over time folks will
come and go from the lab and new users will be provided with accounts and old accounts will
disappear.

Typically folks will access Janus through SSH. For example, a user with username gandalf might login with
```
ssh gandalf@janus.hss.cmu.edu
```
and then be prompted for a password to finish logging in.

***Important***: Please ***do not*** login to Janus at the console connected to the physical machine. If you do so Janus will think you are using it as a desktop machine, and by default will assume you want to save power and put itself to sleep once you’ve been idle for a little while, and thus make it completely unavailable over the network!

If you want to use the GPU on Janus’s graphics card to speed up highly parallelizable, linear computations you may, or may not, need to access Janus through its console, depending
upon details of the software you are using. If you think you do need to do this please discuss it with Don first so we can make sure it is done in such a way that it does not
disrupt Janus for other users.

When a new account is created the user will be sent an initial password, typically in a separate, cryptic mail message
that does not mention the account or server name. This should be changed at the earliest
convenience. Please be sure to change it to something not easily guessed. The new password should be at least eight
characters long and contain at least one each of upper case letters, lower case letters, digits, and non-alphanumeric
characters, nor any long strings of the same character or characters in ascending or descending order,
though such a format is not currently enforced. To change your password, after logging in with your current one
use the `passwd` command on Janus.

There are undoubtedly GUI alternatives to the ssh command
that can be used from various platforms if preferred. And setting up `ssh_keys` to allow easily
logging in without having to type a password is recommended as it is generally more secure
than logging in with a password.

All users are members of the `ddmlab` and `www-data` groups which may be useful for easily sharing write access to files
that might otherwise be difficult to share.

We normally keep online experiments in the directory `~ddmlab/experiments/`. In addition to their home
directory each user is provided with a subdirectory of `~ddmlab/experiments/` with the same name as
their user account, into which they can write. For example, the directory `~ddmlab/experiments/gandalf`
would be owned by the user `gandalf`. To ensure easy access to old code and so on once you’ve left the group
please be sure to keep your experiments in this directory. For nodeGame experiments each user also has
a subdirectory under `~ddmlab/experiments/nodegame/games_available/`,
*e.g.* `~ddmlab/experiments/nodegame/games_available/gandalf/`. Note that `~ddmlab` is just a shorthand
way of writing `/home/ddmlab`.

Most things you will need to do can be accomplished without root privileges, but for those rare things
that do require such privileges, *e.g.* restarting nodeGame or opening a port in the firewall, please
ask one of those with root privileges, currently Coty and Don.

Please do not share login credentials with others. When trouble arises, it really is best if we can
track who did what to most expeditiously resolve problems. Multiple people masquerading as the same
user both step on each other’s feet and confuse things.

If you are new to Unix, there is a wealth of information about it available online. Note that you’ll typically
be interacting with Janus from the command line, so you may want to focus on tutorials and so on that concentrate
on that, rather than those aimed at GUI users of personal Linux machines. You may also find searching for
the more general Unix command line useful rather than the more specific Linux; while there are differences between
different Unix implementations, the similarities dominate the differences. One reasonable starting place
is [Canonical’s “The Linux command line for beginners”](https://ubuntu.com/tutorials/command-line-for-beginners#1-overview). However, you’ll need to ignore much of what is in section 3 of this tutorial,
as it is aimed at someone logging into a Linux machine from its console, and Janus should generally
only be logged into over SSH.

One important thing you’ll want to do frequently is move files onto and off of Janus. There are various ways
this can be accomplished, but probably the easiest is [scp](https://manpages.ubuntu.com/manpages/trusty/en/man1/scp.1.html),
a command that works much like the `cp` command for copying files, but works between machines by using SSH
as a transport mechanism. So, for example, if your user name is `gandalf` and you have a file called
`data.zip` in `~ddmlab/experiments/gandalf` you could move it to your current directory on you local
machine by running on your local machine the command

    scp gandalf@janus.hss.cmu.edu:/home/experiments/gandalf/data.zip ./

Note that in this command you typically can’t use the `~ddmlab` shortcut and need to spell out `/home/ddmlab`; also
this works on macOS or Linux, but I’ve no idea what the cognate incantation would be in Windows, though there must
be something similar. There are also
undoubtedly GUI implementations of `scp` if you prefer such, though again I know nothing about them.

If you are new to writing online experiments here is some
[possibly useful, though opinionated, advice](https://github.com/DDM-Lab/janus/blob/main/web-apps.md).

In the past we had a Janus users mailing list. Now that everyone will have an account on Janus
it would be better to use the `DDMLab-members` mailing list instead.

In any case, please do use mail to coördinate activities
that may impact others. For example, you might send a message saying something like,

> I’m going to be running a resource intensive simulation for about the next 48 hours, and so it will
> be difficult for others to use massive resources on Janus during that time. Please let me
> know if this will be a problem. I will send another message as soon as I’m done.

And be sure to send that promised followup! And note that, as discussed further below, if you
are doing something like what is described in this message you should take steps to ensure it
does not conflict with ongoing on-line experiments run on Janus simultaneously.

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

The Apache HTTP web server, version 2.4.52, is installed on Janus, and serves HTTP through port 80.
We do not yet support TLS (transport layer security, aka HTTPS), but may obtain the necessary certificates and serve
it through port 443 if a need arises. As of right now, there does not appear to be such a need.

For serving simple, static files, simply place them in `/var/www/html/` and ensure that they are publicly readable,
or at least readable by the user `www-data`, though once you’re serving them publicly it is hard to imagine
why you’d not let them be publicly readable. On the other hand, do **not** make them publicly writable.

CGI scripts can be placed in `/usr/lib/cgi-bin/`. Ensure they are executable.

## [Best practices](#best-practices)

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

For an example of a good README, see [the investment single player README](https://github.com/DDM-Lab/investment-single-player/blob/main/README.md);
though the description of the code in this file may be a bit more detailed that will always be practical, it seems something good to aim at.

***Note***: as of an audit in late July 2024 nearly *half*, 49%, of the repositories in the DDMLab
GitHub space lack a README entirely, contain an empty README, or contain a semantically vacuous README.
The goal of keeping things in GitHub is to facilitate their reuse, possibly long after the author
has left the lab; it is not simply to tick a box that says “it’s in GitHub.” For very old code
it may be too late to add much information as it is long forgotten, but for new things being
added please *always* include a *useful* README.

### A note on copy and paste coding

Many, probably most, of the things we create in the DDMLab, both online experiments and models, are
intended to work across two or more similar, but slightly different, conditions. This unfortunately
tempts folks to code in a fashion that discourages reuse and encourages errors: the temptation is to write it once for one condition,
than just make a complete verbatim copy of that and change a few constants or lines of code. This results in code
that is not only two or several fold longer than necessary, in addition it makes it difficult for a reader to
understand how seemingly identical large blocks of code differ, where changes need to be made, and, worst of all,
requires any changes to be made multiple times. This doesn’t just make life difficult for those who come
after you, it makes life difficult for you, yourself, if you need to update or change things in six months.

Instead, aim at writing such things once, perhaps as a function or similar abstraction, with the different conditions
as parameters or other small, easily changed and isolated pieces of data. For example, imagine writing a simple
PyIBL model to perform a safe/risky binary choice task with three different risky payoffs and corresponding
probabilities. Don’t write something like this

    import matplotlib.pyplot as plt
    import numpy as np
    from pyibl import Agent
    from random import random

    results = []
    high_probability = 3 / 4
    for participant in range(100):
        agent = Agent(default_utility=(1.2 * 4))
        round_results = [None] * 60
        for round in range(60):
            choice = agent.choose(["safe", "risky"])
            if choice == "safe":
                payoff = 3
            elif random() < high_probability:
                payoff = 4
            else:
                payoff = 0
            agent.respond(payoff)
            round_results[round] = int(choice == "risky")
        results.append(round_results)
    plt.plot(range(1, 61),
             np.mean(np.asarray(results), axis=0),
             label=f"risky high payoff = 4 points")

    results = []
    high_probability = 3 / 6
    for participant in range(100):
        agent = Agent(default_utility=(1.2 * 4))
        round_results = [None] * 60
        for round in range(60):
            choice = agent.choose(["safe", "risky"])
            if choice == "safe":
                payoff = 3
            elif random() < high_probability:
                payoff = 6
            else:
                payoff = 0
            agent.respond(payoff)
            round_results[round] = int(choice == "risky")
        results.append(round_results)
    plt.plot(range(1, 61),
             np.mean(np.asarray(results), axis=0),
             label=f"risky high payoff = 4 points")

    results = []
    high_probability = 3 / 12
    for participant in range(100):
        agent = Agent(default_utility=(1.2 * 4))
        round_results = [None] * 60
        for round in range(60):
            choice = agent.choose(["safe", "risky"])
            if choice == "safe":
                payoff = 3
            elif random() < high_probability:
                payoff = 12
            else:
                payoff = 0
            agent.respond(payoff)
            round_results[round] = int(choice == "risky")
        results.append(round_results)
    plt.plot(range(1, 61),
             np.mean(np.asarray(results), axis=0),
             label=f"risky high payoff = 4 points")

    plt.ylim([0, 1])
    plt.ylabel("fraction choosing risky")
    plt.xlabel("round")
    plt.legend()
    plt.title(f"Safe (3 points) versus risky, 100 participants")
    plt.savefig("binary-choice.png")

Instead, write something like this

    import matplotlib.pyplot as plt
    import numpy as np
    from pyibl import Agent
    from random import random

    HIGH_PAYOUTS = [4, 6, 12]
    SAFE_PAYOUT = 3
    PLOT_FILE = "binary-choice.png"
    ROUNDS = 60
    PARTICIPANTS = 100
    PREPOPULATED_MULTIPLIER = 1.2

    def run_condition(high_payout):
        results = []
        high_probability = SAFE_PAYOUT / high_payout
        for participant in range(PARTICIPANTS):
            agent = Agent(default_utility=(PREPOPULATED_MULTIPLIER * high_payout))
            round_results = [None] * ROUNDS
            for round in range(ROUNDS):
                choice = agent.choose(["safe", "risky"])
                if choice == "safe":
                    payoff = SAFE_PAYOUT
                elif random() < high_probability:
                    payoff = high_payout
                else:
                    payoff = 0
                agent.respond(payoff)
                round_results[round] = int(choice == "risky")
            results.append(round_results)
        return results

    for payout in HIGH_PAYOUTS:
        plt.plot(range(1, ROUNDS + 1),
                 np.mean(np.asarray(run_condition(payout)), axis=0),
                 label=f"risky high payoff = {payout} points")
    plt.ylim([0, 1])
    plt.ylabel("fraction choosing risky")
    plt.xlabel("round")
    plt.legend()
    plt.title(f"Safe ({SAFE_PAYOUT} points) versus risky, {PARTICIPANTS:,} participants")
    plt.savefig(PLOT_FILE)

(Yes, there are many other ways the “good” example could be further improved; the point here is just to avoid copy and paste programming,
no matter how tempting it is with similar conditions. Those who come after you will thank you, and you will thank yourself
if you come after yourself in six months.)

### A note for Python users

When adding a Python project to the DDMLab GitHub space it should almost always include a `requirements.txt` file. When doing this it is tempting to
just do `pip freeze > requirements.txt`. While that does capture what you have been using, in the long term it is likely to cause trouble. A bit more
thought and care will make life much easier for those who come after you. While doing `pip freeze` is a fine first step, there are at least two problems
with leaving it at that:

The first problem is that it captures *everything* in your current Python environment. Often folks developing code install a *lot* more packages than are actually needed by the
end product.
For example, it might include various plotting and data analysis packages you routinely use, but that are not needed by the code you are capturing in GitHub. Or, if you use
one environment for most of your work, it can include things only used in completely different projects. And if you’ve been working on this for a while, it may include
far older versions of packages where the most recent ones work fine, or even better

Far better is, when your work on a piece of code is stable, to create a *fresh, new* virtual environment, activate it, and install just what you think you need into it.
Then do the `pip freeze` with that to capture just what you need, and the latest versions that work with your code.

The second problem is that it captures everything, *including* the versions of indirect dependencies. This is unfortunate. If you are not yourself using
an indirect dependency, it is really the package that depends upon that indirect dependency that should be expressing an option on what version it needs.
Locking it in makes life much more difficult for those who come after and may want to upgrade one of the direct dependencies of your code.

For example, imagine you are making a simple PyIBL model, and the only package you install is PyIBL. When you do `pip freeze` you will get

    contourpy==1.2.1
    cycler==0.12.1
    fonttools==4.53.1
    kiwisolver==1.4.5
    matplotlib==3.9.1
    numpy==2.0.1
    ordered-set==4.1.0
    packaging==24.1
    pandas==2.2.2
    pillow==10.4.0
    prettytable==3.10.2
    pyactup==2.2.3
    pyibl==5.1.5
    pylru==1.2.1
    pyparsing==3.1.2
    python-dateutil==2.9.0.post0
    pytz==2024.1
    six==1.16.0
    tzdata==2024.1
    wcwidth==0.2.13

Now, imagine two years from now someone needs to reuse this code, and wants to upgrade it to PyIBL version 6.
When they look at the `requirement.txt` file they will have no idea which of these many packages your
model is using, and which versions it is depending upon. In simple cases they can upgrade everything to the
latest and greatest, but as things become more complex, particularly for code that may have an unusual, further dependency,
it can become difficult to see what is actually needed. Far better if the `requirements.txt` file simply contained

    pyibl==5.1.5

This is, in fact, *all* that is needed to run the model, since all those other dependencies are indirect ones simply
required by PyIBL and it is far better for PyIBL to say what it needs. Indeed, for all we know, whenever PyIBL version 6
is available is may in turn use a newer version of PyACTUp which in turn may no longer need `prettytable` or `pylru`. Far
better if the `requirements.txt` file cites only what is actually needed and lets Python’s installation software handle
the indirect dependencies.

So, best practice, is to reduce your `requiremnets.txt` file to just the packages *you* actually need. In this case,
just PyIBL.

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
with others using the `DDMLab-members` mailing list. And remember it is as important to say “I’m done using most
of Janus now” as it was to say “I’ve debugged by simulation running in one process and I’m about to start
it up using 56 of them, and so coming close to saturating the machine.”

If we all adhere to these guidelines we will probably cohabit on Janus with ease. If it turns out that
even with these there’s trouble, we’ll revisit what we all need to do to ensure success.

### Online experiments

In general online experiments are probably the biggest exposure of Janus to malefactors.
While we are not likely to be an appealing target, if we are not careful we will surely be
attacked, sooner or later.

To minimize our exposure, please
* run your experiment code only when needed for testing, data collection or demonstration purposes
  (demos may well need to run all the time, of course), and shut it down when not being actively
  used;
* run it as an ordinary user, typically yourself, but **never** as root;
  though for demos as opposed to experiments it’s best to use the user `ddmlab`;
* and when you are no longer running an experiment, or don’t intend to do so again for a very long
  long time, if it was being served out of a dedicated port please ensure that port is closed in the
  firewall, which will require asking someone with root privileges to do so on your behalf  — see the subsequent
  section on port usage for more details

#### Tagging experiments

When about to run an experiment, please be sure to *tag* that experiment, with a meaningful name, in Git and GitHub, so we can always find, and resurrect if necessary, the code that was used to run our experiments.

To learn about tagging in Git, see [https://git-scm.com/book/en/v2/Git-Basics-Tagging](https://git-scm.com/book/en/v2/Git-Basics-Tagging) and/or [https://www.atlassian.com/git/tutorials/inspecting-a-repository/git-tag](https://www.atlassian.com/git/tutorials/inspecting-a-repository/git-tag).

There are two principal flavors of tags; the kind we’re talking about here are “annotated” tags. It’s really important for these purposes that we do use the annotated variety as they store a little extra, useful metadata.

So the rough process when you’re about to run an experiment is to ensure you have the exact version of code you’re running committed in your local Git repository, and then do something like

    git tag -a momble-frotz-experiment-single-user-run-2023-01-05 -m 'commit tag for experiment'
    git push --follow-tags

Note that pushing a commit does not normally push tags, you have to use `--follow-tags` to do that. Also note that `--follow-tags` only pushes annotated tags , which is *a good thing*, as the lightweight kind are really just private bookmarks and normally should not be pushed to GitHub.†

An alternative to `--follow-tags` is to push a specific tag by name, like

    git push origin momble-frotz-experiment-single-user-run-2023-01-05

though this pushes only the tag, and not the underlying commit, so you’ll have to push the code proper separately.

You can configure Git to apply `--follow-tags` by default by setting the `push.followTags` option to true with [`git-config`](https://git-scm.com/docs/git-config). If you do this you can still skip following tags with `git push --no-follow-tags`.

Please be sure to pick meaningful names for your tags, so that someone else searching years in the future for the exact version of the code you used for the experiment today can be readily discovered. It’s probably best to include a date, or at least date range, in tag names for experiments that are run.

You can see what tags there are with `git tag`, with no arguments. You can see information about an annotateed tag with `git show <tag-name>`. You can also view a list of tags in GitHub, right near where you view information about branches.

If you use a GUI or similar interface to Git instead of the command line it almost certainly includes cognates to all of the above, though you’ll have to consult the documentation of that interface for details.

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
  `DDMLab-members` mailing list. Note also that applications using ports in this range must
  be run, or at least started, as root, which is yet a further security complication,
  and requires extra care and discussion.
* For now, please do not use ports greater than 1024 but less than 3000.
* When exposing experiments to the cruel, outside world please use ports numbered
  3000 or greater, but less than 9000. When opening such ports open them only for TCP or UDP,
  whichever you need; please do not open them for both unless you really need both.
* Even within the 3000–8999 range there are seven ports that need to be avoided because the
  CMU network blocks them: 3389, 5555, 5900–5903 and 6050. See
  [CMU Internet Blocks](https://www.cmu.edu/iso/service/net-blocks/index.html) for details.
* When using ports for internal interprocess communication please use ports numbered
  9000 or greater but less than 10000.
* Please do not use ports numbered 10000 or greater.

To coördinate our use of ports there is in the Janus repo in the DDMLab GitHub space a
[CSV file port usage table](https://github.com/DDM-Lab/janus/blob/main/ports.csv).
Please “claim” any port you need to use here, and fill out all four fields for each.

You will need to ask some one with root privileges, currently Coty or Don, to open a port for you
once you’ve settled on which you will be using.

Open ports are the primary source of vulnerabilities of web servers. When you no longer
need an open port (which should be in the range 3000 to 8999) please ask to have it closed again,
and update the usage table. If you wish to retain a claim to the port number for
possible future reuse, please simply change its state from “open” to “closed.”

### Demos that should always be running

Most of our online experiments have relatively short lifetimes, and don’t need to automatically restart
when Janus is rebooted. In fact, it’s best that they don’t in case we forget to stop them
when no longer needed (but please, do remember to stop them!).

Note that the rest of this section on demos describes activities that require root privileges, so can be skipped
by most readers.

But permanent demos do need to run 7/24 The
[pm2 process manager](https://pm2.keymetrics.io/) is running on Janus, and using this is
usually the simplest way to accomplish this. New demos can be added to those started by `pm2`
simply by calling `pm2 start` with suitable arguments. However, it is important to only call
`pm2` while running as the user `ddmlab`, lest you instead start a new instance of it running
as yourself. So, to start a demo initialized by running `run-demo.sh` in the current directory, do

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
for malefactors to attack Janus and minimizes any unnecessary drain of Janus’s resources.

And please run them as an ordinary user, usually as yourself.
But **never** as root.

Typically your files and directories will be owned by you when you are running your experiment as yourself.

Normally a transient experiment will have a shell script able to start it. When you are testing simply running
that script is probably sufficient. But when you deploy an experiment you may want to ensure that it is not
stopped when you log out. This is easily done with the [`nohup` command](https://manpages.ubuntu.com/manpages/trusty/man1/nohup.1.html). If the script that starts your experiment is called `run.sh` you can do something like

    nohup ./run.sh &

which will run your experiment in the background, and ignore you logging out. It will print a process id which you
can use to kill it with the [Unix `kill` command](https://manpages.ubuntu.com/manpages/focal/en/man1/kill.1.html) when
you are done with it, or if you need to restart it.

If you lose track of the process id you can easily rediscover it using `pgrep -a` with a suitable string to identify it.
For example, if you are using `flask` you might do something like

    pgrep -a flask

In this case be sure to look at the output carefully and pick the process id corresponding to *your* experiment
and not someone else’s.

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

Version 8.1 of PHP is installed on Janus.

To access PHP simply put your PHP files in `/var/www/html` or (usually better) a subdirectory thereof, and ensure
they have the extension `.php`, and access them in usual way from a web browser through port 80.

For more details of the PHP installation point a browser at [phpinfo.php](http://ddmlab.com/phpinfo.php).

### JavaScript

The latest (as of 22 December 2022) LTS version of node.js, version 19.3.0, along with npm version 9.2.0 and nodemon 2.0.20, are installed on Janus. These will be upgraded on an irregular basis, though upgrades will be announced on the `DDMLab-members` mailing list. They will almost certainly be upgraded along with the biennial OS upgrade.

If for some reason you need a different version of node.js please discuss it on the `DDMLab-members` mailing list. It appears that, if necessary, technologies exist so that multiple versions of node.js can peacefully cohabit one machine, though they seem awkward to install and configure.

#### nodeGame

Note that we have rearranged nodeGame a little since this was first written. Updating this section of this document
remains a TODO. It will be updated soon.

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
* ensure, probably by using the `DDMLab-members` mailing list, that nodeGame coming down will not impact anyone else,
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

R version 4.1.2 is installed. Unless there is a demand it will only be upgraded infrequently.

#### Shiny Server

The R Shiny Server version 1.7.1 is also installed. Again it is unlikely to be upgraded unless there is a demand.
It serves through port 3838.
To deploy applications simply place them in an appropriately named subdirectory of `/srv/shiny-server/`.
So, for example, if your application is in `/srv/shiny-server/mumble/` users get at it with a URL
like `http://ddmlab.com:3838/mumble/`.
See `/srv/shiny-server/shinyibl/` for an example.

### Databases

While many folks instinctively reach for a database when they wish to store data, think long
and hard about whether you really need one. For the things we do in the DDMLab it is usually
a heavier hammer than we really need. Simple text files, typically CSV, are often sufficient.

If you decide you really do need a database, it is ***strongly*** recommended that you use SQLite
in preference to MySQL. It is much easier to deal with, MySQL being quite complicated, and additionally
running as a separate process, with its own idiosyncratic  permissions system.

#### SQLite

The version of SQLite 3 current in the OS version is installed (as of 22 March 2022 this is version 3.37.2).
Note that it must be called as `sqlite3`, not just `sqlite`, which is obsolete and rarely used.
This typically lags behind the very latest and greatest available, but is stable and well tested.
It will typically be upgraded sometime after a new version appears in the OS repositories, and almost
certainly whenever the OS is upgraded.

#### MySQL

If you *really* need MySQL, it is installed. But you will have a *lot* to learn to use it.

The version of the MariaDB fork of MySQL current in the OS version is
installed (as of 22 December 2022 this is version 10.6.11), with InnoDB as the
default underlying engine. It will typically be upgraded sometime
after a new version appears in the OS repositories, and almost
certainly whenever the OS is upgraded.

If you really must use MySQL be sure to back up your database *frequently* by dumping its contents to a text file.
While MySQL is included in the nightly backup, it is far more difficult to recover things there than from simple files,
and depending upon the night backup is almost certain to lead to tears.

## Why the name Janus?

Janus is the ancient Roman god of beginnings, gates, transitions,
time, duality, doorways, passages, frames, and endings, and thus by implication
of decisions. He is also typically depicted with two faces, just as janus.hss.cmu.edu
has its two faces, online experiments and resource intensive simulations.
