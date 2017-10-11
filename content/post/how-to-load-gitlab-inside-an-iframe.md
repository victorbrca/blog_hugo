---
title: "How to Load Gitlab Inside an Iframe"
date: 2017-10-11T17:08:34-04:00
draft: false
author: "Victor Mendonça"
description: "Instructions on how to configure GitLab to allow it to be loaded inside an iFrame."
tags: ["Linux", "Git", "GitLab"]
---

So you are trying to load GitLab inside another page via iframe, and you are not able to. Due to security reasons, this is default behavior for GitLab, and as per the project (see issue [2347](https://gitlab.com/gitlab-org/gitlab-ce/issues/2347), this will not change, and I agree).

However for some internal users this might not be the best approach, so here's how to enable it.

Browse to your install directory and go to your ‘nginx’ folder. If you are not sure where it is, with your GitLab instance running, use the following command (assuming you are using port 8888).

```
# netstat -anp | grep 8888
tcp        0      0 0.0.0.0:8888                0.0.0.0:*                   LISTEN      19761/nginx
```

Now use the PID to find where ‘nginx’ is running:

```
# ps -ef | grep 19761
root     19761  1029  0 13:12 ?        00:00:00 nginx: master process /opt/gitlab/embedded/sbin/nginx -p /var/opt/gitlab/nginx
```

Browse to the config folder and edit the ‘gitlab-http.conf’

```
# cd /var/opt/gitlab/nginx/conf

# vim gitlab-http.conf
```

Add the line `proxy_hide_header X-Frame-Options;`` in the `location /` code block

```http.conf
 location / {
     ## Serve static files from defined root folder.
     ## @gitlab is a named location for the upstream fallback, see below.
     try_files $uri $uri/index.html $uri.html @gitlab;
   }


   proxy_hide_header X-Frame-Options;
```

Comment the line `proxy_hide_header X-Frame-Options;` if present

```http.conf
location @gitlab {
    ## If you use HTTPS make sure you disable gzip compression
    ## to be safe against BREACH attack.


    ## https://github.com/gitlabhq/gitlabhq/issues/694
    ## Some requests take more than 30 seconds.
    proxy_read_timeout      300;
    proxy_connect_timeout   300;
    proxy_redirect          off;

    proxy_set_header   X-Forwarded-Proto $scheme;
    proxy_set_header   Host              $http_host;
    proxy_set_header   X-Real-IP         $remote_addr;
    proxy_set_header   X-Forwarded-For   $proxy_add_x_forwarded_for;
    proxy_set_header   X-Frame-Options   SAMEORIGIN;
    #proxy_hide_header X-Frame-Options;

    proxy_pass http://gitlab;
  }
```

Restart GitLab

```
# gitlab-ctl restart
ok: run: logrotate: (pid 31748) 1s
ok: run: nginx: (pid 31752) 0s
ok: run: postgresql: (pid 31757) 1s
ok: run: redis: (pid 31766) 0s
ok: run: sidekiq: (pid 31774) 0s
ok: run: unicorn: (pid 31786) 0s
```

That should be it!!!
