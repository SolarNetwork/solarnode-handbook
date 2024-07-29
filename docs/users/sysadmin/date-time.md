# Date and Time

SolarNodeOS includes date and time management functions through the [timedatectl][timedatectl-man]
command. Run `timedatectl status` to view information about the current date and time settings.

```sh title="Viewing the current date and time settings"
$ timedatectl status
               Local time: Fri 2023-05-26 03:41:42 BST
           Universal time: Fri 2023-05-26 02:41:42 UTC
                 RTC time: n/a
                Time zone: Europe/London (BST, +0100)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

## Changing the local time zone

SolarNodeOS uses the `UTC` time zone by default. If you would like to change this, use the
`dpkg-reconfigure tzdata`

```sh title="Changing the local time zone"
$ sudo dpkg-reconfigure tzdata
```

This will present a menu to choose the desired time zone. You can also list the available time zone names by running `timedatectl list-timezones`.

## Internet time synchronization

SolarNodeOS uses the [systemd-timesyncd][systemd-timesyncd-man] service to synchronize the node's clock
with internet time servers. Normally no configuration is necessary. You can check the status of the network
time synchronization with [timedatectl][timedatectl-man] like:

```sh
$ timedatectl status
               Local time: Fri 2023-05-26 03:41:42 BST
           Universal time: Fri 2023-05-26 02:41:42 UTC
                 RTC time: n/a
                Time zone: Europe/London (BST, +0100)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

!!! warning

	For internet time synchronization to work, SolarNode needs to access Network Time Protocol
	(NTP) servers, using UDP over port 123.

### Network time server configuration

The NTP servers that SolarNodeOS uses are configured in the [/etc/systemd/timesyncd.conf][timesyncd.conf-man]
file. The default configuration uses a pool of Debian servers, which should be suitable for most nodes.
If you would like to change the configuration, edit the `timesyncd.conf` file and change the `NTP=` line,
for example

``` title="Configuring the NTP servers to use"
[Time]
NTP=my.ntp.example.com
```

## Setting the date and time

In order to manually set the date and time, NTP time synchronization must be disabled with `timedatectl set-ntp false`.
Then you can run `timedatectl set-time` to set the date:

```sh title="Manually changing the date and time"
$ sudo timedatectl set-ntp false
$ sudo timedatectl set-time "2023-05-26 17:30:00"
```

If you then look at the `timedatectl status` you will see that NTP has been disabled:

```sh title="Status with NTP disabled"
$ timedatectl
               Local time: Fri 2023-05-26 17:30:30 NZST
           Universal time: Fri 2023-05-26 05:30:30 UTC
                 RTC time: n/a
                Time zone: Pacific/Auckland (NZST, +1200)
System clock synchronized: no # (1)!
              NTP service: inactive # (2)!
          RTC in local TZ: no
```

1. The clock is not synchronized with internet time servers, as it shows **no**
2. The NTP service has been disabled, as it is listed as **inactive**

You can re-enable NTP time synchronization like this:

```sh title="Enabling NTP time synchronization"
$ sudo timedatectl set-ntp true
```

[systemd-timesyncd-man]: https://manpages.debian.org/bullseye/systemd-timesyncd/systemd-timesyncd.8.en.html
[timedatectl-man]: https://manpages.debian.org/bullseye/systemd/timedatectl.1.en.html
[timesyncd.conf-man]: https://manpages.debian.org/bullseye/systemd-timesyncd/timesyncd.conf.5.en.html
