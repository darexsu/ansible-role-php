# Ansible role PHP 

[![CI Molecule](https://github.com/darexsu/ansible-role-php/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-php/actions/workflows/ci.yml)

Optional:

  - install PHP {version} from Sury/Remi repositories
  - configuration ( ./pool.d/*.conf ) 
  - configuration ( ./php.ini )

Molecule testing:

|    Debian     |    Ubuntu     |    CentOS     |  Rocky Linux |
| ------------- | ------------- | ------------- | ------------ |
|   10, 11      | 18.04, 20.04  |     7, 8      |      8       |


Installation:

1) Install from Galaxy
```
ansible-galaxy install darexsu.php
```
2.1) Example playbook for php 8.0, TCP/IP socket

```yaml
---
- hosts: all

  roles:
    - role: darexsu.php
      vars:
        php_install: true
        php_install__version: "8.0"                       # you can change to 7.1, 7.2, 7.3 etc
        php_settings: true
        php_settings__pool_webserver_user: "www-data"     # you can change to apache or nginx
        php_settings__pool_webserver_group: "www-data"    # you can change to apache or nginx
        php_settings__pool_tcp_ip_socket: true            # enable tcp/ip socket
        php_settings__pool_tcp_ip_socket_listen: "127.0.0.1:9000"
```
2.2) Example playbook for php 8.0, unix socket

```yaml
---
- hosts: all

  roles:
    - role: darexsu.php
      vars:
        php_install: true
        php_install__version: "8.0"                      # you can change to 7.1, 7.2, 7.3 etc
        php_settings: true
        php_settings__pool_webserver_user: "www-data"    # you can change to apache or nginx
        php_settings__pool_webserver_group: "www-data"   # you can change to apache or nginx 
        php_settings__pool_tcp_ip_socket: false          # disable tcp/ip socket
        php_settings__pool_unix_socket: true             # enable unix socket
        php_settings__pool_unix_user: "username"         # owner socket
        php_settings__pool_unix_group: "username"
        php_settings__pool_socket_listen: "/run/php/php{{ php_install__version }}-{{ php_settings__pool_unix_user }}.sock"
```