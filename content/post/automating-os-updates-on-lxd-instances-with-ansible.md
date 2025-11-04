---
title: "Automating OS Updates on LXD Instances with Ansible"
date: 2025-11-03T17:40:13-05:00
draft: false
author: "Victor Mendonça"
description: "A simple Ansible project to automate OS updates on your LXD containers and VMs"
tags: ["Linux", "Containers", "Virtualization"]
---

![image 0](/img/automating-os-updates-on-lxd-instances-with-ansible/header.png)

Keeping your LXD containers and VMs up to date can be a bit of a hassle — especially when you’re managing different distributions or even Windows instances. That’s why I built **[lxd-os-update](https://github.com/victorbrca/lxd-os-update/)**, an Ansible project designed to automate the process of running OS updates across all your LXD environments.

It’s a simple yet powerful role that takes care of the entire cycle for you:

* Starts stopped and frozen instances (configurable)
* Runs OS updates for both **APT** and **YUM**-based Linux distros
* Updates **Windows** servers via SSH and PowerShell
* Restores each instance to its previous stopped or frozen state afterward

### Why I Built It

When managing LXD VMs and containers, I often needed a way to keep everything patched without manually starting, updating, and stopping each instance.
Ansible seemed perfect for the job — but I quickly ran into a limitation with the Ansible LXD inventory plugin.

The plugin hardcodes `ansible_connection` to ssh whenever it detects an IP address. That means even if you explicitly set `ansible_connection: lxd`, Ansible will still try to connect via SSH, using the IP address as `ansible_host`.

Here’s an example of what happens:

```bash
$ ansible-inventory --host ubuntu-2404
{
    "ansible_connection": "ssh",
    "ansible_host": "10.48.212.128",
    "ansible_lxd_os": "ubuntu",
    "ansible_lxd_type": "virtual-machine"
}
```

And when you try to run a task with the LXD connection plugin:

```bash
$ ansible -m ping ubuntu-2404 -e 'ansible_connection=lxd' -vvvv
```

You’ll get the error:

```
Error: Failed to fetch instance "10" in project "default": Instance not found
```

That’s because Ansible is trying to connect to an instance literally named **"10"**, taken from the IP address.

### The Workaround: A Custom Inventory Script

To get around this, I created a custom inventory script, included in the project. It defines instances manually and correctly sets `ansible_connection: lxd` (except for Windows, which still uses SSH).

This approach bypasses the plugin’s issue and ensures Ansible connects directly through LXD when possible.

## Requirements

Before running the playbook, make sure your environment meets these conditions:

+ Ansible on the host
  + Ansible collections (see `requirements.yml`):
      + community.general
      + ansible.windows
* Each instance must have the `image.os` config attribute set (see below)
* `python3` should be installed inside the instances
* The LXD agent must be installed and running inside the instances
* For Windows instances, OpenSSH server must be enabled and configured for an administrator user

#### Setting the `image.os` Attribute

The automation relies on the `image.os` attribute to detect the OS family. You can view and set it like this:

```bash
$ lxc list -cns,image.os:OS
+------------------+---------+--------+
|       NAME       |  STATE  |   OS   |
+------------------+---------+--------+
| jellyfin         | STOPPED | ubuntu |
| rhel9            | STOPPED | rhel   |
| windows11        | RUNNING |        |   <--- MISSING
+------------------+---------+--------+
```

You can manually set the value with:

```bash
# Example for a Windows 11 instance
$ lxc config set windows11 image.os=windows

# Example for a RHEL 9 instance
$ lxc config set rhel9 image.os=rhel
```

#### Installing the LXD Agent

The LXD agent enables direct Ansible communication through the LXD plugin. Refer to your distribution’s documentation on how to install and start it.

#### Configuring Windows for SSH

For Windows instances, Ansible connects over SSH. So you’ll need to configured it, preferrably with an SSH key.

a. Enable OpenSSH server and configure the firewall:

```PowerShell
# Start the sshd service
Start-Service sshd

# OPTIONAL but recommended:
Set-Service -Name sshd -StartupType 'Automatic'

# Install the OpenSSH Server
Add-WindowsCapability -Online -Name (Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH.Server*').name

# Confirm the Firewall rule is configured. It should be created automatically by setup. Run the following to verify
if (!(Get-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -ErrorAction SilentlyContinue)) {
    Write-Output "Firewall Rule 'OpenSSH-Server-In-TCP' does not exist, creating it..."
    New-NetFirewallRule -Name 'OpenSSH-Server-In-TCP' -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22
} else {
    Write-Output "Firewall rule 'OpenSSH-Server-In-TCP' has been created and exists."
}
```

b. Test the connection

c. Create a new ssh key pair if desired

d. Copy the contents of your public key (e.g.: `id_rsa.pub`) to `C:\ProgramData\ssh\administrators_authorized_keys`

e. Change de default shell to PowerShell in the registry key `HKEY_LOCAL_MACHINE\SOFTWARE\OpenSSH\DefaultShell`:

```PowerShell
New-ItemProperty -Path "HKLM:\SOFTWARE\OpenSSH" -Name DefaultShell -Value "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" -PropertyType String -Force
```

f. Install the PSWindowsUpdate PowerShell Module for updates

```PowerShell
# Install module
Install-Module -Name PSWindowsUpdate

# Enable execution
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned
```

g. Update your inventory variables (`group_vars/windows.yml`) with your SSH details:

```yaml
ansible_user:
ansible_ssh_private_key_file:
```

h. Then test your connection:

```bash
$ ansible windows11 -m raw -a 'get-date'
windows11 | CHANGED | rc=0 >>
Tuesday, October 28, 2025 7:48:35 PM
```

### Role Variables

You can control different behaviors using the defaults and vars role variables.

**Defaults - `roles/os-update/defaults/main.yml`**

Enable the reboot of instances, if needed, after the update:

```yaml
reboot_if_needed: true
```

Enable/disable updates on non running instances:

```yaml
update_non_running: true
```

**Vars - `roles/os-update/vars/main.yml`**

Specifies what distros are interpreted as apt/yum. You can add distros as needed.

```yaml
apt_distros:
  - debian
  - ubuntu
  - kali
  - raspbian

yum_distros:
  - rhel
  - oracle
```

### Running the Playbook

Once configured, just run:

```bash
ansible-playbook playbooks/os-update.yml
```

Ansible will:

1. Start any stopped or frozen instances (if configured)
2. Run system updates for each one
3. Reboot if required
4. Return the instances to their previous state

### Final Thoughts

This setup has saved me a lot of time keeping my LXD environment consistent and secure. Whether you’re managing a small lab or a larger test cluster, automating OS updates with Ansible can make your maintenance much smoother — and less error-prone.

You can check out the full project on GitHub:
👉 [https://github.com/victorbrca/lxd-os-update/](https://github.com/victorbrca/lxd-os-update/)
