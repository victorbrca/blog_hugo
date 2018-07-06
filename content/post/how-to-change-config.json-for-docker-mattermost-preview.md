---
title: "How to Change cconfig.json for Docker Mattermost Preview"
date: 2018-07-05T15:20:24-04:00
draft: false
author: "Victor Mendonça"
description: "Instructions on how to change config.json for Docker Mattermost preview"
tags: ["Linux", "Docker", "Mattermost"]
---

These instructions show you how to change the `config.json` file for a [Docker Mattermost Preview](https://hub.docker.com/r/mattermost/mattermost-preview/).


a- As root (or if you enabled non root users to manipulate docker), let's open a shell in the docker container

```
docker exec -ti mattermost-preview /bin/bash
```

b. Update the APT db on the docker instance and install `vim` (needed to edit the file)

```
apt update
apt install vim
```

c. Change `config.json` as needed

```
cd /mm/mattermost/config

vim config.json
```

_**Note:**_ You might need to update `config_docker.json` in the same folder

d. Restart the docker container

```
docker restart mattermost-preview
```
