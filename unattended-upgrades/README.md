# unattended-upgrades

## change schedule for apt-daily.timer and apt-daily-upgrade.timer

### apt-daily.timer

This timer triggeres the apt-daily.service which only downloads the packages, but don't installs them.

`systemctl edit apt-daily.timer`

Add this content in the appropriate gap.

```
[Timer]
OnCalendar=*-*-* 02:00:00
RandomizedDelaySec=1h
```

`systemctl restart apt-daily.timer`

### apt-daily-upgrade.timer

This timer will do the actual update of those packages.

`systemctl edit apt-daily-upgrade.timer`

Add this content in the appropriate gap.

```
[Timer]
OnCalendar=*-*-* 03:00:00
RandomizedDelaySec=1h
```

`systemctl restart apt-daily-upgrade.timer`

## pinning version for critical packages

Some packages are quite critical and therefore we don't want them to be upgraded by unattended-upgrades (by default they won't be upgraded if you don't add them in the `Unattended-Upgrade::Origins-Pattern` setting) or accidentally by ourselves.

### version pinning for nodejs

Create the file `/etc/apt/preferences.d/nodejs` with this content:

```
Package: nodejs
Pin: origin deb.nodesource.com
Pin: version 20.12*
Pin-Priority: 600
```

Run `apt update` to enable the pinning.

### version pinning for mariadb-server

Create the file `/etc/apt/preferences.d/mariadb-server` with this content:

```
Package: mariadb-* libmariadb3 mysql-common
Pin: version 1:11.2.3*
Pin-Priority: 1000
```

Run `apt update` to enable the pinning.

### Test pinning

`apt-cache policy` will show your the pinned packages.
