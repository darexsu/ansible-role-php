# Ansible role PHP 

[![CI Molecule](https://github.com/darexsu/ansible-role-php/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-php/actions/workflows/ci.yml)

Molecule testing:

| Platforms |    Debian     |    Ubuntu     |    CentOS     |  Rocky Linux |
| --------- | ------------- | ------------- | ------------- | ------------ |
|  Version  |   10, 11      | 18.04, 20.04  |     7, 8      |      8       |
| --------- | ------------- | ------------- | ------------- | ------------ |
| PHP repository |  Sury    | ppa:ondrej    | Epel, Remi    | Epel, Remi   |

Optional:

  - install PHP {version} from Sury/Remi repositories
  - configuration ( ./pool.d/*.conf ) 
  - configuration ( ./php.ini )

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
        php_install: true                                 # enable ./task/install/*
        php_install__version: "8.0"                       # you can change to 7.1, 7.2, 7.3 etc
        php_settings: true                                # enable ./task/settings/*
        php_settings__php_ini: true                       # enable ./templates/php_ini.j2
        php_settings__pool: true                          # enable ./templates/php_pool.j2
        php_settings__pool_webserver_user: "www-data"     # you can change to apache or nginx
        php_settings__pool_webserver_group: "www-data"    # you can change to apache or nginx
        php_settings__pool_tcp_ip_socket: true            # enable tcp/ip socket
        php_settings__pool_tcp_ip_socket_listen: "127.0.0.1:9000" # listen port: 9000 on localhost
```
2.2) Example playbook for php 8.0, unix socket

```yaml
---
- hosts: all

  roles:
    - role: darexsu.php
      vars:
        php_install: true                                # enable ./task/install/*
        php_install__version: "8.0"                      # you can change to 7.1, 7.2, 7.3 etc
        php_settings: true                               # enable ./task/settings/*
        php_settings__php_ini: true                      # enable ./templates/php_ini.j2
        php_settings__pool: true                         # enable ./templates/php_pool.j2
        php_settings__pool_webserver_user: "www-data"    # you can change to apache or nginx
        php_settings__pool_webserver_group: "www-data"   # you can change to apache or nginx 
        php_settings__pool_tcp_ip_socket: false          # disable tcp/ip socket
        php_settings__pool_unix_socket: true             # enable unix socket
        php_settings__pool_unix_user: "username"         # owner socket
        php_settings__pool_unix_group: "username"
        php_settings__pool_socket_listen: "/run/php/php{{ php_install__version }}-{{ php_settings__pool_unix_user }}.sock"
```
Customize:

You can custom your tasks. For example:

Update php.ini only. Note: PHP already installed

```yaml
---
- hosts: all

  roles:
    - role: darexsu.php
      vars:
        php_settings: true                              # enable ./task/settings/*
        php_settings__version: "8.0"                    # you can change to 7.1, 7.2, 7.3 etc
        php_settings__php_ini: true                     # enable ./templates/php_ini.j2
        php_settings__php_ini_date_timezone: "America/Chicago" # edit ./templates/php_ini.j2

```

Create ./pool.d/nginx.conf for Nginx web-server. Note: PHP already installed

```yaml
---
- hosts: all

  roles:
    - role: darexsu.php
      vars:
        php_settings: true                              # enable ./task/settings/*
        php_settings__version: "8.0"                    # you can change to 7.1, 7.2, 7.3 etc       
        php_settings__pool: true                        # enable ./templates/php_ini.j2
        php_settings__pool_file: "nginx.conf"           # name of pool
        php_settings__pool_webserver_user: "nginx"      # user who start web-server
        php_settings__pool_webserver_group: "nginx"       
        php_settings__pool_tcp_ip_socket: true
        php_settings__pool_tcp_ip_socket_listen: "127.0.0.1:9001"
```