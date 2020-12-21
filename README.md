# Autopilot Ansible Playbooks

Ansible playbooks for setting up an optimized web server for WordPress and Laravel. These playbooks are used by Autopilot  (our cloud server control panel) and are perfect for:

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
* [Redis](https://redis.io/)
* [MySQL 8](https://hub.docker.com/_/mariadb)
* [Fail2Ban](https://en.wikipedia.org/wiki/Fail2ban)
* [Supervisor](http://supervisord.org/)
* [Docker](https://www.docker.com/)
* [UFW (firewall)](https://help.ubuntu.com/community/UFW)
* [OpenSSH Server & SFTP](https://www.openssh.com/)
* [Restic (for backups)](https://restic.net/)
* [Bubblewrap](https://github.com/containers/bubblewrap)
* [Composer](https://getcomposer.org/)
* [phpMyAdmin](https://www.phpmyadmin.net/)
* [WPCLI](https://wp-cli.org/)
* [MSMTP (email relay)](https://wiki.archlinux.org/index.php/msmtp)
* [Node Exporter (for monitoring)](https://prometheus.io/docs/guides/node-exporter/)
* [Unattended Upgrades](https://help.ubuntu.com/community/AutomaticSecurityUpdates)

Take a look at `inventory/group_vars/install.yml` to see a list of packages and services you can disable during provisioning.

## Configuration

You may override the default server configuration by creating a YAML-file in the following location: `inventory/host_vars/<inventory-server-name>/main.yml`. Copy the variables you would like to change (defined in `inventories/group_vars/all/`) to this file and change the value. Each resource (for example a site or user) is defined using a separate YAML-file in the `resources` folder.

## Playbooks

### Server

Provision and test the server.

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

Provision a site using the variables defined in `resources/sites/example.yml`.

```bash
# Provision site playbook: 
ansible-playbook -i <inventory-file> ./playbooks/site/provision.yml -e @resources/sites/example.yml

# Destroy site playbook: 
ansible-playbook -i <inventory-file> ./playbooks/site/destroy.yml -e @resources/sites/example.yml
```

### SSH Key

Adds a public ssh key to the `authorized_keys` file of a system user using the variables defined in `resources/keys/example.yml`.

```bash
# Provision ssh key playbook: 
ansible-playbook -i <inventory-file> ./playbooks/key/provision.yml -e @resources/keys/example.yml

# Destroy ssh key playbook: 
ansible-playbook -i <inventory-file> ./playbooks/key/destroy.yml -e @resources/keys/example.yml
```

### Site Mount

Provision a site mount using the variables defined in `resources/mounts/example.yml`. This will create an entry in `/etc/fstab` for mounting a site on a remote server using SSHFS. Run `mount <site_name>-mnt` with the user specified in `mount_user` to mount the directory. Please note that the user's public key needs to be provisioned to the user specified in `mount_source_user`.

```bash
# Provision sshfs site mount playbook: 
ansible-playbook -i <inventory-file> ./playbooks/mount/provision.yml -e @resources/mounts/example.yml

# Destroy sshfs site mount playbook: 
ansible-playbook -i <inventory-file> ./playbooks/mount/destroy.yml -e @resources/mounts/example.yml
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
