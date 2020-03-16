---
title: "RHCSA v8: Work with Package Module Streams"
date: 2020-03-15T20:52:36-04:00
draft: false
author: "Victor Mendonça"
description: "Working with YUM modules on RHEL 8"
tags: ["Linux", "RedHat", "RHCSA"]
---

#### Disclaimer

These are my study notes for the RHCSA exam on YUM modules. There's most likely more information than what's needed for the exam, and I cannot guarantee that all information is correct.

## Definition

RHEL 8 content is distributed through two main repositories: BaseOS and AppStream.  
<br>
#### **BaseOS**

Content in the BaseOS repository is intended to provide the core set of the underlying OS functionality that provides the foundation for all installations. This content is available in the RPM format and is subject to support terms similar to those in previous releases of Red Hat Enterprise Linux.  
<br>
#### **AppStream**

Content in the AppStream repository includes additional user-space applications, runtime languages, and databases in support of the varied workloads and use cases. Content in AppStream is available in one of two formats - the familiar RPM format and an extension to the RPM format called modules.

Components made available as Application Streams can be packaged as modules or RPM packages and are delivered through the AppStream repository in Red Hat Enterprise Linux 8. Each AppStream component has a given life cycle.

### Modules

Modules allow you to install a specific version and/or type of an application in your system. For example, for 'postgresql' you can choose to install from multiple versions (stream), and client or server type (profile).

![](/img/rhsa-v8-work-with-package-module-streams/postgresql.png)

```nothing
# yum module list postgresql

Last metadata expiration check: 0:20:44 ago on Sat 14 Mar 2020 08:59:58 PM UTC.
CentOS-8 - AppStream
Name                     Stream              Profiles                        Summary
postgresql               9.6           client, server [d]              PostgreSQL server and client module
postgresql               10 [d]        client, server [d]              PostgreSQL server and client module
postgresql               12            client, server                  PostgreSQL server and client module

Hint: [d]efault, [e]nabled, [x]disabled, [i]nstalled
```

<br>
For httpd on Centos8, currently only one stream (version) is available, and profiles are the package type (common, minimal, development)

![](/img/rhsa-v8-work-with-package-module-streams/httpd.png)

```nothing
# yum module list httpd

Last metadata expiration check: 0:21:46 ago on Sat 14 Mar 2020 08:59:58 PM UTC.
CentOS-8 - AppStream
Name                  Stream               Profiles                              Summary
httpd                 2.4 [d][e]           common [d], devel, minimal            Apache HTTP Server

Hint: [d]efault, [e]nabled, [x]disabled, [i]nstalled
```

<br>
## Working with Modules

### Getting Information on Modules
<br>
Listing all modules

```
# yum module list
```
<br>
Listing module summary for one module with `yum module list [module]`

```nothing
# yum module list httpd

Last metadata expiration check: 0:21:46 ago on Sat 14 Mar 2020 08:59:58 PM UTC.
CentOS-8 - AppStream
Name                  Stream               Profiles                              Summary
httpd                 2.4 [d][e]           common [d], devel, minimal            Apache HTTP Server
```
<br>
Listing info on a module with `yum module info [module]`

```nothing
# yum module info httpd

Last metadata expiration check: 0:35:45 ago on Sat 14 Mar 2020 08:59:58 PM UTC.
Name             : httpd
Stream           : 2.4 [d][e][a]
Version          : 8010020191223202455
Context          : cdc1202b
Architecture     : x86_64
Profiles         : common [d], devel, minimal
Default profiles : common
Repo             : AppStream
Summary          : Apache HTTP Server
Description      : Apache httpd is a powerful, efficient, and extensible HTTP server.
Artifacts        : httpd-0:2.4.37-16.module_el8.1.0+256+ae790463.src
                 : httpd-0:2.4.37-16.module_el8.1.0+256+ae790463.x86_64
                 : httpd-debuginfo-0:2.4.37-16.module_el8.1.0+256+ae790463.x86_64
                 : httpd-debugsource-0:2.4.37-16.module_el8.1.0+256+ae790463.x86_64
                 : httpd-devel-0:2.4.37-16.module_el8.1.0+256+ae790463.x86_64
                 : httpd-filesystem-0:2.4.37-16.module_el8.1.0+256+ae790463.noarch
                 : httpd-manual-0:2.4.37-16.module_el8.1.0+256+ae790463.noarch
                 : httpd-tools-0:2.4.37-16.module_el8.1.0+256+ae790463.x86_64
                 : httpd-tools-debuginfo-0:2.4.37-16.module_el8.1.0+256+ae790463.x86_64
                 : mod_http2-0:1.11.3-3.module_el8.1.0+213+acce2796.src
                 : mod_http2-0:1.11.3-3.module_el8.1.0+213+acce2796.x86_64
                 : mod_http2-debuginfo-0:1.11.3-3.module_el8.1.0+213+acce2796.x86_64
                 : mod_http2-debugsource-0:1.11.3-3.module_el8.1.0+213+acce2796.x86_64
                 : mod_ldap-0:2.4.37-16.module_el8.1.0+256+ae790463.x86_64
                 : mod_ldap-debuginfo-0:2.4.37-16.module_el8.1.0+256+ae790463.x86_64
                 : mod_md-0:2.4.37-16.module_el8.1.0+256+ae790463.x86_64
                 : mod_md-debuginfo-0:2.4.37-16.module_el8.1.0+256+ae790463.x86_64
                 : mod_proxy_html-1:2.4.37-16.module_el8.1.0+256+ae790463.x86_64
                 : mod_proxy_html-debuginfo-1:2.4.37-16.module_el8.1.0+256+ae790463.x86_64
                 : mod_session-0:2.4.37-16.module_el8.1.0+256+ae790463.x86_64
                 : mod_session-debuginfo-0:2.4.37-16.module_el8.1.0+256+ae790463.x86_64
                 : mod_ssl-1:2.4.37-16.module_el8.1.0+256+ae790463.x86_64
                 : mod_ssl-debuginfo-1:2.4.37-16.module_el8.1.0+256+ae790463.x86_64
```
<br>
Listing profiles with `yum module info --profile [module]`

```nothing
# yum module info --profile httpd

Last metadata expiration check: 0:36:28 ago on Sat 14 Mar 2020 08:59:58 PM UTC.
Name    : httpd:2.4:8010020191223202455:cdc1202b:x86_64
common  : httpd
        : httpd-filesystem
        : httpd-tools
        : mod_http2
        : mod_ssl
devel   : httpd
        : httpd-devel
        : httpd-filesystem
        : httpd-tools
minimal : httpd
```
<br>
You can also filter the information with `[module_name]:[stream]`

```
# yum module info --profile php:7.3
```
<br>
### Enabling Stream

Note that switching module streams will not alter installed packages. You will need to remove a package, enable the stream and then install the package.  
<br>
Enable the stream for 'postgresql' v9.6

```nothing
# yum module enable postgresql:9.6
```
<br>
Enable the httpd devel profile

```nothing
# yum module enable --profile httpd:2.4/devel

Last metadata expiration check: 0:47:51 ago on Sat 14 Mar 2020 08:59:58 PM UTC.
Ignoring unnecessary profile: 'httpd/devel'
Dependencies resolved.
Nothing to do.
Complete!
```
<br>
Then install the package

```
# yum install postgresql httpd  
```
<br>
To change a module stream again, you will need to run `yum module reset [module name]`, and then enable the new module.  

```nothing
# yum module enable postgresql:10

Last metadata expiration check: 0:06:07 ago on Sat 14 Mar 2020 09:57:50 PM UTC.
Dependencies resolved.
The operation would result in switching of module 'postgresql' stream '9.6' to stream '10'
Error: It is not possible to switch enabled streams of a module.
It is recommended to remove all installed content from the module, and reset the module using 'dnf module reset <module_name>' command. After you reset the module, you can install the other stream.
```

```nothing
# yum module reset postgresql

Last metadata expiration check: 0:06:15 ago on Sat 14 Mar 2020 09:57:50 PM UTC.
Dependencies resolved.
=================================================================================================
Package               Architecture         Version                  Repository             Size
=================================================================================================
Resetting modules:
postgresql                                                                                

Transaction Summary
=================================================================================================

Is this ok [y/N]: y
Complete!
```
