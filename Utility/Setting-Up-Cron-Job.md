Mon Jan 23 13:38:16 PST 2023
# Setting up cron jobs under crouton

As per [ticket 957](https://github.com/dnschneid/crouton/issues/957):

> crouton doesn't use upstart, so you need to run crond explicitly, probably from the chroot's /etc/rc.local. Look inside /etc/init/cron for the commands needed to launch crond.

So edit `/etc/rc.local` and add the following line **before** `exit 0`:

    exec cron

This will start the `cron` process as **root** for the system. Being the case, any cron jobs added at the user level *will not work* (ie anything added using the command `crontab -e`). For this reason you will have to add crons to the `/etc/crontab` file.