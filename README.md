# Autopilot Ansible Playbooks

## Overview

Ansible playbooks for setting up an optimized web server for WordPress and Laravel. These playbooks are used by Autopilot (our cloud server control panel) and are perfect for:

* Local development environments.
* High-performance production servers (with caching).

## Requirements

* Ansible
* Ubuntu 20.04 LTS (Desktop/Server)

## What's included

The following packages and services will be provisioned on each server:

* [Caddy Web Server (for proxy and auto ssl)](https://caddyserver.com/)
* [OpenLitespeed (web server)](https://www.litespeedtech.com/open-source/openlitespeed)
* [LSPHP 7.4](https://www.litespeedtech.com/open-source/litespeed-sapi/php)
* [LSPHP 7.3](https://www.litespeedtech.com/open-source/litespeed-sapi/php)
* [Composer](https://getcomposer.org/)
* [UFW (firewall)](https://help.ubuntu.com/community/UFW)
* [OpenSSH Server & SFTP](https://www.openssh.com/)
* [MSMTP (email relay)](https://wiki.archlinux.org/index.php/msmtp)
* [Restic (for backups)](https://restic.net/)
* [Redis](https://redis.io/)
* [MySQL 8](https://hub.docker.com/_/mariadb)
* [Bubblewrap](https://github.com/containers/bubblewrap)

### Optional

Optional packages are defined in `inventories/group_vars/all/install.yml`.

* [Docker](https://www.docker.com/)
* [Fail2Ban](https://en.wikipedia.org/wiki/Fail2ban)
* [Unattended Upgrades](https://help.ubuntu.com/community/AutomaticSecurityUpdates)
* [phpMyAdmin](https://www.phpmyadmin.net/)
* [Node Exporter (for monitoring)](https://prometheus.io/docs/guides/node-exporter/)
* [WPCLI](https://wp-cli.org/)
* [WordMove](https://github.com/welaika/wordmove)
  
## Configuration

You may override the default server configuration by creating a YAML-file in the following location: `inventory/host_vars/<inventory-server-name>/main.yml`. Copy the variables (defined in `inventories/group_vars/all/`) you would like to change to this file and change the value. Each resource (for example a site or user) is defined using a separate YAML-file in the `resources` folder.

## Playbooks

### Server

Provision the server.

```bash
# Test server connection playbook: 
ansible-playbook -i <inventory-file> ./playbooks/server/connect.yml

# Provision server playbook: 
ansible-playbook -i <inventory-file> ./playbooks/server/provision.yml

# Test server playbook: 
ansible-playbook -i <inventory-file> ./playbooks/server/test.yml 
```

### User

Provision a system user using the variables defined in `resources/users/example.yml`. System users are isolated and allowed to use SFTP with password authentication (chroot directory `{{ root_path }}/users/%u/sites`).

```bash
# Provision user playbook: 
ansible-playbook -i <inventory-file> ./playbooks/user/provision.yml -e @resources/users/example.yml

# Test user playbook: 
ansible-playbook -i <inventory-file> ./playbooks/user/test.yml -e @resources/users/test.yml

# Destroy user playbook: 
ansible-playbook -i <inventory-file> ./playbooks/user/destroy.yml -e @resources/users/example.yml
```

### Site

Provision a site using the variables defined in `resources/users/example.yml`.

```bash
# Provision site playbook: 
ansible-playbook -i <inventory-file> ./playbooks/site/provision.yml -e @resources/sites/example.yml

# Destroy site playbook: 
ansible-playbook -i <inventory-file> ./playbooks/site/destroy.yml -e @resources/sites/example.yml
```

### Site Mount

Provision a site mount using the variables defined in `resources/mounts/example.yml`. This will create an entry in `/etc/fstab` for mounting a site on a remote server using SSHFS.

```bash
# Provision sshfs site mount playbook: 
ansible-playbook -i <inventory-file> ./playbooks/mount/provision.yml -e @resources/mounts/example.yml

# Destroy sshfs site mount playbook: 
ansible-playbook -i <inventory-file> ./playbooks/mount/destroy.yml -e @resources/mounts/example.yml
```

### SSH Key

Provision a ssh key using the variables defined in `resources/keys/example.yml`. Adds a public ssh key to the `authorized_keys` file of a system user.

```bash
# Provision ssh key playbook: 
ansible-playbook -i <inventory-file> ./playbooks/key/provision.yml -e @resources/keys/example.yml

# Destroy ssh key playbook: 
ansible-playbook -i <inventory-file> ./playbooks/key/destroy.yml -e @resources/keys/example.yml
```

### Database

Provision a database and database user using the variables defined in `resources/databases/example.yml`.

```bash
# Provision database playbook: 
ansible-playbook -i <inventory-file> ./playbooks/database/provision.yml -e @resources/databases/example.yml

#Destroy database playbook: 
ansible-playbook -i <inventory-file> ./playbooks/database/destroy.yml -e @resources/databases/example.yml
```

### Backup

Create a file or database backup using the variables defined in `resources/backups/example.yml`.

```bash
# Run a database backup: 
ansible-playbook -i <inventory-file> ./playbooks/backup/run.yml -e @resources/backups/example-database.yml

# Run a file backup: 
ansible-playbook -i <inventory-file> ./playbooks/backup/run.yml -e @resources/backups/example-file.yml

# Restore a database backup: 
ansible-playbook -i <inventory-file> ./playbooks/backup/restore.yml -e @resources/backups/example-database.yml

# Restore a file backup: 
ansible-playbook -i <inventory-file> ./playbooks/backup/restore.yml -e @resources/backups/example-file.yml

# Destroy a database backup: 
ansible-playbook -i <inventory-file> ./playbooks/backup/destroy.yml -e @resources/backups/example-database.yml

# Destroy a file backup: 
ansible-playbook -i <inventory-file> ./playbooks/backup/destroy.yml -e @resources/backups/example-file.yml
```

## Web Apps

* phpMyAdmin: `https://<hostname>/phpmyadmin/`.
* Health check: `https://<hostname>/health/`.
* Node Exporter: `https://<hostname>/metrics/`.

## Filesystem

* Caddy vhosts folder: `/etc/caddy/vhosts`.
* OpenLitespeed vhosts folder: `/usr/local/lsws/conf/vhosts`.
* Site public folder: `/home/{{ user_name }}/sites/{{ site_name }}/public`.
* Site logs folder: `/home/{{ user_name }}/sites/{{ site_name }}/logs`.
* Default vhost folder for server index page, health & phpMyAdmin: `/home/{{ admin }}/sites/default/public`

## License

MIT / BSD

## Author

Autopilot was created in 2020 by [Nick Jansen](https://nbejansen.com/).
