# Autopilot Ansible

## Overview

Ansible playbooks for setting up a optimized web server for WordPress and Laravel. Perfect for:

* Local development environments.
* High-performance production servers (with caching).

## What's included

Autopilot will configure a server with the following packages/services:

* [Caddy Web Server (for proxy and auto ssl)](https://caddyserver.com/)
* [OpenLitespeed (web server)](https://www.litespeedtech.com/open-source/openlitespeed)
* [LSPHP 7.4](https://www.litespeedtech.com/open-source/litespeed-sapi/php)
* [LSPHP 7.3](https://www.litespeedtech.com/open-source/litespeed-sapi/php)
* [Composer](https://getcomposer.org/)
* [WPCLI](https://wp-cli.org/)
* [WordMove](https://github.com/welaika/wordmove)
* [UFW (firewall)](https://help.ubuntu.com/community/UFW)
* [OpenSSH Server & SFTP](https://www.openssh.com/)
* [MSMTP (email relay)](https://wiki.archlinux.org/index.php/msmtp)
* [Restic (for backups)](https://restic.net/)
* [Redis](https://redis.io/)
* [MySQL 8](https://hub.docker.com/_/mariadb)
* [phpMyAdmin 5](https://www.phpmyadmin.net/)
* [Node Exporter (for monitoring)](https://prometheus.io/docs/guides/node-exporter/)

Users are isolated and allowed to use SFTP with password authentication (chroot directory `{{ root_path }}/users/%u/sites`).

### Tools

* phpMyAdmin: `http://<hostname>/phpmyadmin/`.
* Health check: `http://<hostname>/health/`.
* Node Exporter: `http://<hostname>/metrics/`.

### Filesystem

* Caddy vhosts folder: `/etc/caddy/vhosts`.
* OpenLitespeed vhosts folder: `/usr/local/lsws/conf/vhosts`.
* Site public folder: `/home/{{ user.name }}/sites/{{ site.name }}/public`.
* Site logs folder: `/home/{{ user.name }}/sites/{{ site.name }}/logs`.
* Default services folder (health & phpMyAdmin): `/home/{{ admin }}/sites/default/public`

# Requirements

* Ansible
* Ubuntu 20.04 LTS (Desktop/Server)

## License

MIT / BSD

## Author

Autopilot was created in 2020 by [Nick Jansen](https://nbejansen.com/).
