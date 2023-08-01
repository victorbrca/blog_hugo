---
title: "Understanding \"yum clean all\" and Stale Repos"
date: 2023-06-30T15:55:26-04:00
draft: false
author: "Victor Mendonça"
description: "yum clean all functionalities"
tags: ["Linux", "RedHat"]
---

When it comes to managing packages on a Linux system, the YUM ([Yellowdog Updater Modified](https://linux.die.net/man/8/yum)) package manager is widely used for its ease of use and robust features. One of the handy commands in YUM is `yum clean all`, which helps you keep your system clean and optimized. In this blog post, we will delve into the functionalities of `yum clean all` and explore how it can help you clear accumulated cache and improve system performance.

### Cleaning Options

The `yum clean` command can be used with specific options to clean individual components. Here are some of the available options:

+ **packages:** Cleans package-related cache files
+ **metadata:** Removes metadata and other files related to enabled repositories
+ **expire-cache:** Cleans the cache for metadata that has expired
+ **rpmdb:** Cleans the YUM database cache
+ **plugins:** Clears any cache maintained by YUM plugins
+ **all:** Performs a comprehensive cleaning, covering all the above options

### Clean Everything

The `yum clean all` command serves as a one-stop solution to clean various elements that accumulate in the YUM cache directory over time. It offers several cleaning options, allowing you to target specific items or perform a comprehensive clean.

It's essential to note that `yum clean all` does not clean untracked "stale" repositories. This means that if a repository is no longer in use or has been disabled, its cache will not be cleared by this command. We'll explore an alternative method to handle untracked repositories shortly.

#### Analyzing Cache Usage

Before diving into the cleaning process, it's helpful to analyze the cache usage on your system. You can use the following command to check the cache usage:

```bash
$ df -hT /var/cache/yum/

Filesystem               Type  Size  Used Avail Use% Mounted on
/dev/mapper/rootvg-varlv xfs   8.0G  4.6G  3.5G  57% /var
```

#### Cleaning with 'yum clean all'

When you execute `yum clean all`, the command will remove various cached files and improve system performance. However, you may sometimes notice a warning regarding other repository data:

    Other repos take up 1.1 G of disk space (use --verbose for details)

If you run it with the `--verbose` flag, you should see a list of stale/untracked repos

```bash
$ sudo yum clean all --verbose

Not loading "rhnplugin" plugin, as it is disabled
Loading "langpacks" plugin
Loading "product-id" plugin
Loading "search-disabled-repos" plugin
Not loading "subscription-manager" plugin, as it is disabled
Adding en_US.UTF-8 to language list
Config time: 0.110
Yum version: 3.4.3
Cleaning repos: epel rhel-7-server-ansible-2-rhui-rpms rhel-7-server-rhui-extras-rpms rhel-7-server-rhui-optional-rpms rhel-7-server-rhui-rh-common-rpms rhel-7-server-rhui-rpms
              : rhel-7-server-rhui-supplementary-rpms rhel-server-rhui-rhscl-7-rpms rhui-microsoft-azure-rhel7
Operating on /var/cache/yum/x86_64/7Server (see CLEAN OPTIONS in yum(8) for details)
Disk usage under /var/cache/yum/*/* after cleanup:
0      enabled repos
0      disabled repos
1.1 G  untracked repos:
  1.0 G  /var/cache/yum/x86_64/7Server/rhui-rhel-7-server-rhui-rpms
  90 M   /var/cache/yum/x86_64/7Server/rhui-rhel-server-rhui-rhscl-7-rpms
  9.8 M  /var/cache/yum/x86_64/7Server/rhui-rhel-7-server-rhui-extras-rpms
  6.4 M  /var/cache/yum/x86_64/7Server/rhui-rhel-7-server-rhui-supplementary-rpms
  5.3 M  /var/cache/yum/x86_64/7Server/rhui-rhel-7-server-dotnet-rhui-rpms
  1.6 M  /var/cache/yum/x86_64/7Server/rhui-rhel-7-server-rhui-rh-common-rpms
4.0 k  other data:
  4.0 k  /var/cache/yum/x86_64/7Serve
```

#### Manually Removing Untracked Repository Files

To handle untracked repository files, you can manually remove them from the cache directory with `rm`:

```bash
$ sudo rm -rf /var/cache/yum/*
```

#### Refreshing the Cache

Next time you run commands like `yum check-update` or any other operation that refreshes the cache, YUM will rebuild the package list and recreate the cache directory for the enabled repositories.

#### Check the Cache Usage After Cleanup

After performing the cleanup, you can verify the reduced cache usage. Use the `df` command again to check the cache size:

```bash
$ df -hT /var/cache/yum/
```

### Doing it all with Ansible

And if you want to, you can use the Ansible playbook below to automate the YUM cache purge and re-creation:

```yaml
---
- name: Delete yum cache and run yum check-update
  # Add your hosts accordingly below
  hosts: webserver
  become: yes
  tasks:

    - name: Check /var usage with df command
      shell: |
        df -hT /var | awk '{print $6}' | tail -1 | tr -d '%'
      register: var_usage_output

    - name: Display /var usage information
      debug:
        var: var_usage_output.stdout

    ##-- Block starts --------------------------------------------------------------
    - when: var_usage_output.stdout|int > 55
      block:

      - name: Delete yum cache directory
        file:
          path: /var/cache/yum
          state: absent

      - name: Update YUM cache
        yum:
          name: ''
          update_cache: yes

      - name: Check /var usage with df command
        shell: |
          df -hT /var | awk '{print $6}' | tail -1
        register: var_usage_after_cleanup_output

      - name: Display /var usage information
        debug:
          var: var_usage_after_cleanup_output.stdout
    ##-- block ends ----------------------------------------------------------------
```

### Conclusion

Regularly cleaning the YUM cache with the `yum clean all` command can help optimize your system's performance by clearing accumulated files. By understanding the available cleaning options and handling untracked repositories, you can ensure that your YUM cache remains streamlined and efficient. Keep your system running smoothly and enjoy the benefits of a clean YUM cache!

Remember, maintaining a clean and optimized system contributes to a seamless Linux experience.
