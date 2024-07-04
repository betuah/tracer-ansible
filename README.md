# Tracer Study Project - Ansible Setup and Usage Guide

This document provides instructions for setting up SSH keys, configuring users, and running Ansible playbooks for the **Tracer Study** project.

## Generate and Copy SSH Key

Ensure that you have generated an SSH key and copied it to all servers as follows:

```bash
ssh-keygen -t rsa -b 2048 -f ./ssh/id_rsa -N ""
ssh-copy-id sysadmin@haproxy_ip
ssh-copy-id sysadmin@apps1_ip
ssh-copy-id sysadmin@apps2_ip
ssh-copy-id sysadmin@dbcluster1_ip
ssh-copy-id sysadmin@dbcluster2_ip
ssh-copy-id sysadmin@dbcluster3_ip
ssh-copy-id sysadmin@memcache_ip
```

* **`ssh-keygen -t rsa -b 2048 -f ./ssh/id_rsa -N ""`**: Generates a new RSA SSH key pair with a 2048-bit key size and no passphrase.
* **``ssh-copy-id sysadmin@<IP>`**: Copies the public key to the **`<username>`** user on the specified server for password-less authentication.

## Set User Without Password
Edit the **`sudoers`** file to allow the **`<username>`** user to run commands without a password:
```bash
sudo visudo

# Add the following line to the visudo file
username ALL=(ALL) NOPASSWD:ALL
```
* **`sudo visudo`**: Opens the sudoers file for editing in a safe manner.
* **`sysadmin ALL=(ALL) NOPASSWD:ALL:`** Grants **`<username>`** the ability to execute all commands without requiring a password.

## Running Ansible for Project

### Pretask
For servers that require a password, run the following command to apply the **`sudoers`** configuration:
```sh
ansible-playbook /ansible/playbooks/sudoers.yml --ask-become-pass
```

### 1. Run Docker Compose
To start the Ansible services, execute the following command:
```sh
docker-compose up -d
```
### 2. Access the Container
To access the Ansible container, use the following command:
```sh
docker exec -it ansible /bin/sh
```
### 3. Run the Playbook:
Inside the container, execute the desired Ansible playbook:
```sh
ansible-playbook /ansible/playbooks/all.yml
```

## Ansible Playbook
Here are the available Ansible playbooks and their commands for the Tracer Study project:
| Task               | Command                                             |
|--------------------|-----------------------------------------------------|
| Run sudoers        | `ansible-playbook /ansible/playbooks/sudoers.yml`   |
| Run Galera         | `ansible-playbook /ansible/playbooks/galera.yml`    |
| Run HAProxy        | `ansible-playbook /ansible/playbooks/haproxy.yml`   |
| Run Apps           | `ansible-playbook /ansible/playbooks/nginx_php.yml` |
| Run Memcache       | `ansible-playbook /ansible/playbooks/memcache.yml`  |
| Pull Apps          | `ansible-playbook /ansible/playbooks/pull_apps.yml`  |

## Helpful Ansible Commands
Here are some useful Ansible commands for managing the Tracer Study project:
```sh
# Check the latest version of Ansible
ansible --version

# Check the inventory
ansible-inventory -i /ansible/inventory/hosts --list

# Ping all servers
ansible all -m ping -i /ansible/inventory/hosts
```

## Directory Structure
.
├── Dockerfile
├── README.md
├── ansible
│   ├── ansible.cfg
│   ├── inventory
│   │   └── hosts
│   ├── playbooks
│   │   ├── all.yml
│   │   ├── galera.yml
│   │   ├── haproxy.yml
│   │   ├── memcache.yml
│   │   ├── nginx_php.yml
│   │   ├── pull_apps.yml
│   │   └── sudoers.yml
│   └── roles
│       ├── composer
│       │   └── tasks
│       │       └── main.yml
│       ├── galera
│       │   ├── tasks
│       │   │   ├── bootstrap.yml
│       │   │   ├── check.yml
│       │   │   ├── main.yml
│       │   │   └── users.yml
│       │   └── templates
│       │       └── galera.cnf.j2
│       ├── haproxy
│       │   ├── tasks
│       │   │   └── main.yml
│       │   └── templates
│       │       └── haproxy.cfg.j2
│       ├── memcache
│       │   ├── tasks
│       │   │   └── main.yml
│       │   └── templates
│       │       └── memcache.conf.j2
│       ├── nginx
│       │   ├── tasks
│       │   │   └── main.yml
│       │   └── templates
│       │       └── default
│       └── php
│           ├── tasks
│           │   └── main.yml
│           └── templates
│               └── php.ini
└── docker-compose.yml