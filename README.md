omxplayer-frontend
==================

This is a web frontend for the Raspberry Pi omxplayer media player.
It is licensed under GNU GPL v3 or later and available at 
https://github.com/mmitch/omxplayer-frontend

omxplayer-frontend was originally written by TallOak and Krageon.
This is a fork based on their omxplayer-frontend hosted over at
bitbucket: https://bitbucket.org/krageon/omxplayer-frontend

See the upstream branch for the original import.  krageon told me
it's ok to clone it to github and put the GPL on it, so I did :-)
Thanks!


PREREQUISITES
-------------

Obviously, you'll need omxplayer.  If it is not already installed on
your system, it should be available via
```
  % apt-get install omxplayer
```

The preinstalled version on the Raspian images from late 2012 was too
old (some commandline parameters have changed), but
```
  % apt-get update; apt-get upgrade
```

did the trick.  If all else fails, get omxplayer from here:
* omxplayer source:   https://github.com/popcornmix/omxplayer
* omxplayer builds:   http://omxplayer.sconde.net/


You'll also need python and the Python package installer.  Both should
be installed by 
```
   % apt-get install python-pip
```

After that, install the Python module web.py:
```
   % pip install web.py
```


INSTALLATION
------------

Put omxplayer-frontend wherever you like.  In this example, we'll put
it under `$HOME/git/omxplayer-frontend`.  The easiest way to download
is to clone the repository:
```
   $ cd ~/git
   $ git clone git://github.com/mmitch/omxplayer-frontend.git
```
This will create the `omxplayer-frontend` subdirectory.  Next, go there
and create a FIFO named `omxin`.  This will be used by `omxplayerd.py`
to communicate with the omxplayer process.  Unfortunately, a FIFO
can't be checked in to git so you have to create it manually:
```
   $ cd ~/git/omxplayer-frontend
   $ mkfifo omxin
```
Next, either create a subdirectory named `media` in the
`omxplayer-frontend` directory and copy your media files there or
(this is more flexible and recommended) create a link named media to
your existing files:
```
   $ cd ~/git/omxplayer-frontend
   $ ln -s /path/where/your/files/are media
```
Now you are ready to go and can start the omxplayer daemon:
```
   $ cd ~/git/omxplayer-frontend
   $ ./omxplayerd.py
```
`omxplayerd.py` will then be running on port 8080 and can be reached via
http://your.ip:8080/

`omxplayerd.py` itself should run perfectly fine with normal user
permissions.  If you can't play videos, check if you can run `omxplayer`
as a normal user - if this works only as root, either change the
permissions on the video and audio devices or run `omxplayerd.py` as
root.  Running as root is generally not a very good idea, but on a
small system for video-only use it might be acceptable.

Root permissions will also be necessary for shutting down your system
via `omxplayerd.py`, see SHUTDOWN below.


PROPER INSTALLATION
-------------------

### AUTOSTART ###

To automatically start omxplayerd.py on boot, you could write
initscripts or add `omxplayerd.py` to `/etc/inittab`, but the easiest way
is a crontab entry.  Open your crontab via `crontab -e` and add a line
like this:
```
   @reboot cd git/omxplayer-frontend; ./omxplayerd.py > /tmp/omxplayerd.log
```
If you want to run `omxplayerd.py` as root, add this to root's crontab.
In that case, the logfile should be at `/var/log/omxplayerd.log`


### SHUTDOWN ###

The web frontend has a small link at the bottom (sometimes just below
the visible page) that allows system shutdown.  This is intended for
systems that don't have any keyboard attached and where you don't want
to open an SSH session to power down (eg. when controlling
`omxplayerd.py` via browser from your smartphone).

For this to work properly, `omxplayerd.py` has to be run as root
(beware of the security implications).

If you want use sudo(1) instead or just create a file that is checked
regularly by another job running as root (root's crontab perhaps),
have a look at class `Shutdown` in `omxplayerd.py` and adjust the
`subprocess.call()` accordingly.


### PORT CHANGE ###

By default, `omxplayerdpy` will run on port 8080. If you want another
port (say, the default HTTP port 80 when you have no other webserver
running, so you don't have to enter the `:8080` in the URL), add the
port number as a parameter to `omxplayerd.py`
```
   $ cd ~/git/omxplayer-frontend
   $ ./omxplayerd.py 80
```
Of course, this can also be done in a crontab.


UPDATES
-------

Let git update the repository:
```
   $ cd ~/git/omxplayer-frontend
   $ git update
```
If it finds updates and downloads them, you shold restart the running
`omxplayerd.py`.  The most simple way to do this is a reboot if
`omxpladerd.py` is started automatically.  Ugly but working :)

