---
title: "How to Migrate Local Users and Grops"
date: 2023-07-20T16:45:00-04:00
draft: false
author: "Victor Mendonça"
description: "How to migrate local users and groups from one Linux system to another"
tags: ["Bash", "Linux"]
---

### Introduction

Migrating users from one Linux system to another can be a complex task, but with the right steps and careful planning, it can be accomplished smoothly. In this guide, we will walk you through the process of migrating users, their passwords, groups, and home folders from one Linux system to another. We'll be using bash scripts to automate the backup and restore process, ensuring a seamless transition.

The following items will not be covered by our instructions:

+ User limits
+ Mail
+ Group password

### Step 1: Backup User Information on the Source Server

First, log in to the source Linux system and open a terminal. Use the following command to get a list of all users:

```bash
lslogins -o "USER,UID,GECOS,HOMEDIR,SHELL,GROUP,GID,SUPP-GROUPS,SUPP-GIDS"
```

Next, identify the users you want to migrate and add their names to the `users_to_backup` variable in the provided script:

```bash
backup_password_file="etc-passwd-bak"
backup_group_file="etc-group-bak"
backup_shadow_file="etc-shadow-bak"
# Add more users separated by spaces if needed
users_to_backup="linus lennart"

for user in $users_to_backup ; do
  curuser=$user
  echo -e "\n=> User is $curuser"
  curid=$(id -u "$curuser")
  curgid=$(id -g "$curuser")
  cursupgit=$(id -G "$curuser")
  echo "User id is $curid"
  echo "User group id is $curgid"
  echo "User supplementary groups are $cursupgit"
  echo "Backing up /etc/passwd for $curuser"
  grep -E "^${curuser}:.:${curid}" /etc/passwd >> "$backup_password_file"

  echo "Backing up primary group for $curuser"
  if [ -f "$backup_group_file" ] ; then
    if grep -q ":${curid}:" "$backup_group_file" ; then
      echo "Group already backed up"
    else
      grep ":${curid}:" /etc/group >> "$backup_group_file"
    fi
  else
    grep ":${curid}:" /etc/group >> "$backup_group_file"
  fi

  echo "Backing up secondary groups for $curuser"
  for groupid in $cursupgit ; do
    if grep -q ":${groupid}:" "$backup_group_file" ; then
      echo "Group already backed up"
    else
      grep ":${groupid}:" /etc/group >> "$backup_group_file"
    fi
  done

  echo "Backing up /etc/shadow for $curuser"
  sudo grep -E "^$curuser" /etc/shadow >> "$backup_shadow_file"
done
```

Save the script to a file, e.g., `user_backup_script.sh`, and make it executable:

```bash
chmod +x user_backup_script.sh
```

Execute the script as root:

```bash
sudo ./user_backup_script.sh
```

The script will back up user information, including passwords and group data, to the specified backup files.

### Step 2: Copy Backup Files to the Destination Server

Next, copy the three backup files (_etc-passwd-bak_, _etc-group-bak_, and _etc-shadow-bak_) from the source server to the destination server using the scp command or any preferred method.

```bash
# Example using scp
scp etc-*-bak user@destination_server:/path/to/backup_files/
```

### Step 3: Restore Users on the Destination Server

Log in to the destination Linux system and open a terminal. Navigate to the directory where the backup files are located and run the provided restore script as root:

```bash
backup_password_file="etc-passwd-bak"
backup_group_file="etc-group-bak"
backup_shadow_file="etc-shadow-bak"

echo -e "\n=> Backing up the system files"
for file in /etc/{passwd,group,shadow} ; do
  cp -v "$file" "${file}_$(date -I)"
done

echo -e "\n=> Restoring users"
cat "$backup_password_file" | while read -r line ; do
  userid="$(echo "$line" | awk -F":" '{print $3}')"
  username="$(echo "$line" | awk -F":" '{print $1}')"
  echo "-> Restoring user $username"
  if grep -Eq "^${username}:.:${userid}" /etc/passwd ; then
    echo "  ERROR: User ID already exists. Manual intervention is needed"
  else
    echo "$line" >> /etc/passwd
    if grep -qE "^${username}:" /etc/shadow ; then
      echo "  ERROR: User password already exists. Manual intervention is needed"
    else
      grep -E "^${username}:" "$backup_shadow_file" >> /etc/shadow
    fi
  fi
done

echo -e "\n=> Restoring groups"
cat "$backup_group_file" | while read -r line ; do
  groupid="$(echo "$line" | awk -F":" '{print $3}')"
  groupname="$(echo "$line" | awk -F":" '{print $1}')"
  echo "-> Restoring group $groupname"
  if grep -qE "^${groupname}:.:${groupid}" /etc/group ; then
    echo "  ERROR: Group already exists. Manual intervention may be needed"
  else
    echo "$line" >> /etc/group
  fi
done
```

Run the restore script:

```bash
sudo ./user_restore_script.sh
```

The script will restore the users and their passwords, taking care to avoid conflicts with existing user IDs. Pay close attention to error messages and take action if needed. If there are any error messages related to system group creation, it might be acceptable as the groups may already exist on the new system.

### Step 4: Copy Home Folders and Set Permissions

Finally, to complete the migration, copy the home folders for the desired users from the source server to the destination server using rsync:

```bash
# Replace 'user1,user2,user3' with appropriate values
# Replace 'destination_server' with appropriate values
rsync -avzh -e ssh /home/{user1,user2,user3} user@destination_server:/home/
```

Ensure that the permissions and ownership are set correctly for the home folder of each user on the destination server:

```bash
# Replace user and primary_group with appropriate values
sudo chown -R user:primary_group /home/user
```

**Note:** _For SFTP users that use CHROOT, the user's home folder needs to be owned by 'root'_

### Conclusion:

Migrating users from one Linux system to another involves a series of crucial steps, including backup, restoration, and copying of home folders. By using the provided scripts and following this step-by-step guide, you can ensure a smooth and successful user migration process. Always exercise caution during the migration and verify the results to ensure everything is functioning as expected on the destination system.

**Additional reading:**

+ https://access.redhat.com/solutions/179753
