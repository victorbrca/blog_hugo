---
title: "Logrotate: Simplifying Log Management"
date: 2024-09-09T18:00:00-04:00
draft: false
author: "Victor Mendonça"
description: ""
tags: ["Linux", "Administration", "Ansible"]
---

`logrotate` is a system utility designed to manage log files, ensuring that logs don't consume excessive disk space by rotating, compressing, and removing old logs according to user-defined rules. This post will help you get familiar with `logrotate` configuration and usage.

By configuring and running `logrotate` effectively, you can automate log management, saving space, and ensuring your logs stay manageable over time.

## Main Config

The primary configuration file for `logrotate` is located at `/etc/logrotate.conf`. It sets default values and contains the directory for additional configuration files (typically `/etc/logrotate.d`):

```bash
# see "man logrotate" for details
# rotate log files weekly
weekly

# keep 4 weeks worth of backlogs
rotate 4

# create new (empty) log files after rotating old ones
create

# use date as a suffix of the rotated file
dateext

# uncomment this if you want your log files compressed
#compress

# RPM packages drop log rotation information into this directory
include /etc/logrotate.d

# no packages own wtmp and btmp -- we'll rotate them here
/var/log/wtmp {
    monthly
    create 0664 root utmp
    minsize 1M
    rotate 1
}

/var/log/btmp {
    missingok
    monthly
    create 0600 root utmp
    rotate 1
}
```

## Configuration Files

The configuration file for each application or log location usually resides under `/etc/logrotate.d/` and can contain various options to control how logs are handled.

### Common Options

- `hourly` – Rotate logs every hour (_requires cron to run logrotate hourly_).
- `daily` – Rotate logs daily.
- `weekly [weekday]` – Rotate logs once per week.
- `monthly` – Rotate logs the first time `logrotate` is run in a month.
- `yearly` – Rotate logs once a year.
- `rotate [count]` – Defines how many rotated logs to keep. If set to 0, logs are deleted instead of being rotated.
- `minsize [size]` – Rotate logs when they exceed a specific size, while also respecting the time interval (daily, weekly, etc.).
- `size [size]` – Rotate only when log size exceeds the defined limit.
- `maxage [days]` – Remove rotated logs after a specified number of days.
- `missingok` – Continue without error if the log file is missing.
- `notifempty` – Skip rotation if the log file is empty.
- `create [mode] [owner] [group]` – Create a new log file with specified permissions, owner, and group.
- `compress` – Compress rotated logs.
- `delaycompress` – Delay compression until the next rotation cycle.
- `copytruncate` – Truncate the log after copying it to the rotated file.
- `sharedscripts` – Ensures that post-rotation scripts are executed only once.
- `postrotate/endscript` – Define commands to be executed after log rotation:

```bash
postrotate
  /opt/life/tools/stop-approve.sh && /opt/life/tools/start-approve.sh
endscript
```

### Examples

#### Example 1: Deleting Old NMON Files

This example deletes NMON output files that are older than 3 months or larger than 5 MB:

```bash
# Logrotate config for nmon
/var/log/nmon/*.nmon {
  rotate 0
  maxage 90
  size 5M
}
```

#### Example 2: Compressing LMS Logs

In this case, `logrotate` keeps 10 compressed versions of logs, rotating them when logs are older than 12 months or larger than 5 MB:

```bash
# Logrotate config for LMS
/home/my_admin/cmd/log/*.log {
  rotate 10
  maxage 360
  size 5M
  compress
}
```

### Creating a New Configuration File

To create a new log rotation rule, drop a configuration file into the `/etc/logrotate.d/` directory. Ensure the scheduler for `logrotate` (either cron or systemd) is set up correctly.

> **Tip:** Double-check your scheduler settings to ensure smooth log rotation.

## Testing and Running Logrotate

### Testing

#### Dry-Run

Before applying your configuration, you can test it with a dry-run using the `-d` option:

```bash
logrotate -d [my_config_file].conf
```

#### Verbosity

To view detailed steps, run `logrotate` with the `-v` option (useful with `-d` for dry-run testing):

```bash
logrotate -vd [my_config_file].conf
```

### Scheduling Logrotate

`logrotate` can be scheduled using either cron or systemd. Here's a quick overview of both methods:

#### Cron

When using cron, the `logrotate` job is typically defined in `/etc/cron.daily/logrotate`:

```bash
#!/bin/sh

/usr/sbin/logrotate -s /var/lib/logrotate/logrotate.status /etc/logrotate.conf
EXITVALUE=$?
if [ $EXITVALUE != 0 ]; then
    /usr/bin/logger -t logrotate "ALERT exited abnormally with [$EXITVALUE]"
fi
exit 0
```

#### Systemd

Systemd can also manage logrotate via `logrotate.timer` and `logrotate.service`.

_logrotate.timer_:

```systemd
[Unit]
Description=Daily rotation of log files

[Timer]
OnCalendar=daily
RandomizedDelaySec=1h
Persistent=true

[Install]
WantedBy=timers.target
```

_logrotate.service_:

```systemd
[Unit]
Description=Rotate log files

[Service]
Type=oneshot
ExecStart=/usr/sbin/logrotate /etc/logrotate.conf
```

#### Personal Cron Job

You can also schedule `logrotate` manually through a personal cron job:

```bash
# Runs logrotate daily
@daily /sbin/logrotate -s /home/my_admin/cmd/log/logrotate.status /home/my_admin/cmd/logrotate.conf >> /home/my_admin/cmd/log/cron.log 2>&1
```

## Creating Logrotate With Ansible

Managing log rotation through Ansible is a powerful way to automate log maintenance across multiple servers. Below is an example of how you can create a `logrotate` configuration using Ansible.

### Ansible Playbook Example

This playbook installs `logrotate` (if it's not already installed) and creates a new configuration file under `/etc/logrotate.d/` for an application:

```yaml
---

- hosts: all
  gather_facts: true
  become: true

  vars:
    logrotate_conf: |
      # Logrotate for application
      /var/log/application/* {
        # Keep 4 versions of file
        rotate 4

        # compress rotated files
        compress

        # Rotates the log files every week
        weekly

        # Ignores the error if the log file is missing
        missingok

        # Does not rotate the log if it is empty
        notifempty

        # Creates a new log file with specified permissions
        create 0755 apache splunk       
      }

  tasks:

    - name: Installs logrotate
      package:
        name: logrotate

    - name: Creates logrotate configuration
      copy:
        content: "{{ logrotate_conf }}"
        dest: /etc/logrotate.d/application
        owner: root
        group: root
        mode: '0644'

```

### Explanation

- **`logrotate_conf` variable**: Defines the configuration file for `logrotate`. It includes options such as file rotation frequency, compression, and file permissions.
  - `rotate 4` – Keeps 4 old versions of the log.
  - `compress` – Compresses the rotated logs.
  - `weekly` – Rotates the logs every week.
  - `missingok` – Ignores errors if the log file is missing.
  - `notifempty` – Skips log rotation if the log is empty.
  - `create 0755 apache splunk` – Creates a new log file with specific permissions, owned by `apache` and `splunk` groups.

- **Installs logrotate**: The task ensures that `logrotate` is installed on the target servers.

- **Creates logrotate configuration**: The `copy` task creates the custom `logrotate` configuration file under `/etc/logrotate.d/`, with appropriate permissions.

---

By using Ansible, you can streamline the management of log rotation across your environment, ensuring consistency in how logs are maintained across all your systems.
