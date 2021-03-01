---
title: "Getting Started with Ansible Molecule"
date: 2021-02-28T07:00:00-05:00
draft: false
author: "Victor Mendonça"
description: "Quick guide to get you started with Ansible Molecule"
tags: ["Linux", "Ansible", "Docker"]
---

Overview
---

If you haven't heard about Ansible Molecule you came to the right place. I will cover what it is, it's basic usage, and how to install it and get started.

#### What it is

[Ansible Molecule](https://molecule.readthedocs.io/en/latest/) is a project (tool) that can greatly improve your Ansible workflow. It allows you to automate your tasks (which is great for CI) by providing means of running different and idempotent tests against your roles.

And Molecule is based on Ansible, so you are essentially using Ansible to test Ansible. How cool is that!?

#### What it does

To put it in simple words, Molecule tests your code. With Molecule you can easily run multiple tests against your code and make sure it works before deploying to an environment.

Some of the tests you can run are:

+ Yaml lint
+ Ansible lint
+ Ansible syntax
+ Run the Ansible role against a virtual machine
+ Test idempotence (run the same Ansible role against the same virtual machine for the second time)

#### Folder Structure

When invoked Molecule creates a single role folder with the usual Ansible structure. Inside this role folder an additional `molecule` folder is created. This is where the main Molecule configuration lives.

```none
$ tree -d
.
├── defaults
├── files
├── handlers
├── molecule
│   └── default
│       ├── converge.yml
│       ├── molecule.yml
│       └── verify.yml
├── tasks
├── templates
├── tests
└── vars
```

#### Test Types and Scenarios

Test scenarios can be configured inside the molecule folder and each scenario should have it's own folder.

A 'default' scenario is created automatically with the following tests enabled (you can change them according to your needs):

+ lint
+ destroy
+ dependency
+ syntax
+ create
+ prepare
+ converge
+ idempotence
+ side_effect
+ verify
+ destroy

#### Drivers

Three different drivers (Vagrant, Docker, and OpenStack) can be used to create virtual machines. These virtual machines are used to test our roles.

_On this tutorial we will be using Docker as our driver._


Installing Molecule
---

Molecule can be installed via pip or with distro packages (depending on your distro). You can mix and match and install Molecule via pip and specific components (like `ansible` or `ansible-lint`) via your distro's package manager.

**Notes:**

+ _On Windows Molecule can only be installed via WSL_
+ _I'm assuming you already have Ansible installed and will not cover it here_

### Windows Install (Ubuntu WSL)

On Ubuntu Molecule needs to be installed via pip. If perhaps you are running another distro in WSL, you can check if the packages are available with your package manager (if you choose to install that way).

Install `python-pip`.

```none
$ sudo apt install -y python-pip
```

Create a python virtual environment for Molecule. The software we are going to install will reside in the virtual environment (we can use the environment many times).

```none
$ python3 -m venv molecule
```

Activate the environment (see that the prompt changes).

```none
$ source molecule/bin/activate
(molecule-venv) $
```

Install the wheel package.

```none
(molecule-venv) $ python3 -m pip install wheel
```

Install ansible and ansible-lint. You can do this via Python/Molecule, or via the OS.

*OS*

```none
$ apt install ansible ansible-lint
```

*Via molecule*

```none
(molecule-venv) $ python3 -m pip install "molecule[ansible]"  # or molecule[ansible-base]
```

Install molecule and docker.

```none
(molecule-venv) $ python3 -m pip install "molecule[docker,lint]"
```

### Linux Install (Arch)

If you are running Arch (I use Arch BTW) you can install everything with `pacman` (or with pip like we did for Windows WSL).

```none
$ sudo /usr/bin/pacman -S yamllint ansible-lint molecule molecule-docker
```

Getting Started
---

### Creating a Molecule Role

We will now create and configure a role with Molecule using Docker as the driver.

Run `molecule init role [role name] -d docker`:

![](/img/getting-started-with-ansible-molecule/getting-started-with-ansible-molecule-28a74.png)

This should have created a new role folder with the required molecule files inside.

#### molecule.yml

Holds the default configuration for molecule.

_`./myrole/molecule/default/molecule.yml`_

```yml
---
dependency:
  name: galaxy
driver:
  name: docker
platforms:
  - name: instance
    image: docker.io/pycontribs/centos:8
    pre_build_image: true
provisioner:
  name: ansible
verifier:
  name: ansible
```

#### converge.yml

This is the playbook file that molecule will run.

_`./myrole/molecule/default/converge.yml`_

```yml
---
- name: Converge
  hosts: all
  tasks:
    - name: "Include myrole"
      include_role:
        name: "myrole"
```

Running a Simple Test
---

Let's use the existing configuration in `molecule.yml` and `converge.yml` to run a simple test.

Edit the tasks file (`myrole/tasks/main.yml`) and add a simple task (you can use the example below with the debug module):


```yml
---
# tasks file for myrole

- debug:
    msg: "System {{ inventory_hostname }} has uuid {{ ansible_product_uuid }}"
```

Run `molecule converge` (from the role folder) to build the image and run the playbook.

<script id="asciicast-G8PAulZtu6nNtMobEbLypgLwK" src="https://asciinema.org/a/G8PAulZtu6nNtMobEbLypgLwK.js" async></script>

Now that we got the very basic stuff done, let's move into a bit more advanced steps so we can better understand Molecule and use it with our code.

Additional Steps and Configuration
---

### Adding Lint

Lint is not enabled by default, but that can be easily changed by editing `molecule.yml` and adding the `lint` key to it:

```yaml
dependency:
  name: galaxy
driver:
  name: docker
platforms:
  - name: instance
    image: docker.io/pycontribs/centos:8
    pre_build_image: true
provisioner:
  name: ansible
verifier:
  name: ansible
lint: |
  set -e
  yamllint .
  ansible-lint .
```

Now run `molecule lint`. You should get a lot of warnings due to to information missing in the meta folder:

![](/img/getting-started-with-ansible-molecule/getting-started-with-ansible-molecule-d02b3.png)

As instructed by the output of the command, you can quiet or disable the messages by adding a `warn_list` or `skip_list` to `.ansible_lint`

_**Tip:** You can also fine tune yaml linting by editing `.yamllint`_

### Running a Full Test

So far we have only run two tests:

+ Converge (dependency, create, prepare converge)
+ Lint (yaml lint and Ansible lint)

Let's run a full test (default scenario) on our role. Remember, the full test will run dependency, lint, syntax, create, prepare, converge, idempotence, side_effect, verify, cleanup and destroy.

Run `molecule test`.

<script id="asciicast-NYNnPRJaYkEHqUwG0mqxiaUli" src="https://asciinema.org/a/NYNnPRJaYkEHqUwG0mqxiaUli.js" async></script>

Running Roles from Another Folder
---

You can also use Molecule to test roles from another folder (which makes molecule very flexible for testing).

Let's say I have the following folder structure:

```none
.
└── Ansible
    ├── Molecule
    └── web-project
```

Inside my `web-project` folder I have a role called 'apache' that installs (guess what?) httpd.

_`./Ansible/web-project/roles/apache/tasks/main.yml`_

```yml
---
# tasks file for apache

- name: Installs Apache
  yum:
     name: httpd
     state: present
```

I can easily modify my existing `converge.yml` to include that role:

_`./Ansible/Molecule/myrole/molecule/default/converge.yml`_

```yml
---
- name: Converge
  hosts: all
  tasks:
    - name: "Include myrole"
      include_role:
        name: "../../web-project/roles/apache"
```

Also edit `molecule.yml` so we are linting that external folder:

_`./Ansible/Molecule/myrole/molecule/default/molecule.yml`_

```yml
lint: |
  set -e
  yamllint ../../web-project
  ansible-lint ../../web-project
```

And then run `molecule converge` (from the Molecule role folder) to test. Because `molecule converge` does not include the `destroy` command, I can login to the container ( with `molecule login`) and check if httpd was installed:

![](/img/getting-started-with-ansible-molecule/getting-started-with-ansible-molecule-d229e.png)

_**Tip:**_

+ _When testing against multiple containers you can use `molecule login --host [container name]`_
+ _You can also use docker cli to connect to the container - `docker exec -ti [container name] /bin/bash`_

_**Note:** On this example nothing other than the role is imported (e.g. variables and config from `ansible.cfg` are not imported)_

### Additional Container Configuration

We can configure different options for our container in `molecule.yml` under the platforms key section. Configuration here is similar to a docker compose file.

+ **name** - Name of the container
+ **image** - Image for the container. Can be local or from a remote registry
+ **pre_build_image** - Instructs to use pre-build image (pull or local) instead of building from `molecule/default/Dockerfile.j2`
+ **privileged** - Give extended privileges (a "privileged" container is given access to all devices)
+ **capabilities** - Same as `--cap-add=SYS_ADMIN`. Grants a smaller subset of capabilities to the container compared to the privileged option
+ **command** - Same as Dockerfile `RUN`
+ **groups** - Assigns the container to one or more Ansible groups

Example:

```yml
---
dependency:
  name: galaxy
driver:
  name: podman
platforms:
  - name: rhel8
    image: registry.access.redhat.com/ubi8/ubi-init
    tmpfs:
      - /run
      - /tmp
    volumes:
      - /sys/fs/cgroup:/sys/fs/cgroup:ro
    capabilities:
      - SYS_ADMIN
    command: "/usr/sbin/init"
    pre_build_image: true
  - name: ubuntu
    image: geerlingguy/docker-ubuntu2004-ansible
    tmpfs:
      - /run
      - /tmp
    volumes:
      - /sys/fs/cgroup:/sys/fs/cgroup:ro
    capabilities:
      - SYS_ADMIN
    command: "/lib/systemd/systemd"
    pre_build_image: true
provisioner:
  name: ansible
verifier:
  name: ansible
lint: |
  set -e
  yamllint .
  ansible-lint .
```

---

Conclusion
---

I'm hoping I was able to provide you with enough information on how to get started with Molecule. If you have any comments, suggestions or corrections, please leave them in the comment box below.
