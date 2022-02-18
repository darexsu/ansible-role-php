# Ansible role PHP 

[![CI Molecule](https://github.com/darexsu/ansible-role-php/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-php/actions/workflows/ci.yml)&emsp;![](https://img.shields.io/static/v1?label=idempotence&message=ok&color=success)&emsp;![Ansible Role](https://img.shields.io/ansible/role/d/57603?color=blue&label=downloads)

### 1) Install role from Galaxy
```
ansible-galaxy install darexsu.php --force
```

### 2) Example playbooks: 
  
  - [full playbook](#full-playbook)  
    - install
      - [official repo](#example-playbook-from-official-repo)
      - [third-Party repo](#example-playbook-from-third-party-repo)
      - [php modules](#example-playbook-install-php-modules) 
    - config
      - [php.ini](#example-playbook-phpini)
      - [php-fpm tcp/ip socket](#example-playbook-php-fpm-tcpip-socket)
      - [php-fpm unix socket](#example-playbook-php-fpm-unix-socket)

|  Official repo   |  Third-Party repo |
| ---------------- | ----------------- |
| Debian 11        |  Sury             |
| Debian 10        |  Sury             |
| Ubuntu 20.04     |  ppa:andrej       |
| Ubuntu 18.04     |  ppa:andrej       |
| RockyLinux 8     |  remi             |
| OracleLinux 8    |  remi             |

### Role hash_behaviour: Replace with defaults dictionaries
```yaml
---
    vars:
      dict:           # <-- Replace default dictionary
        a: my value
        b: my value
        c: my value
```
### Role hash_behaviour: Merge with default dictionaries
```yaml
---
    vars:
      php_merge:    # <-- Merge with default dictionary
        dict:
          a: my value
          b: my value
          c: my value

```
##### Full playbook
```yaml
---
- hosts: all
  become: true

  vars:
    php_merge:
      php:
        version: "8.0"
      php_repo:
        enabled: true
      php_install:
        enabled: true
      php_ini:
        enabled: true
      php_fpm:
        enabled: true
        vars:    
          unix_socket:
            enabled: true
            file: "php{{ php.version }}-fpm.sock"
            user: "www-data"
            group: "www-data"

  tasks:
  - name: include role darexsu.php
    include_role: 
      name: darexsu.php  

```
##### Example playbook: from official repo
```yaml
---
- hosts: all
  become: true

  vars:
    php_merge:
      php:
        version: "7.4"
      php_install:
        enabled: true
  
  tasks:
  - name: include role darexsu.php
    include_role: 
      name: darexsu.php
    
  
```
##### Example playbook: from third-party repo
```yaml
---
- hosts: all
  become: true

  vars:
    php_merge:
      php:
        version: "8.0"
      php_repo:          # <-- enable third-party repo
        enabled: true
      php_install:
        enabled: true
  
  tasks:
  - name: include role darexsu.php
    include_role: 
      name: darexsu.php   
  
```
##### Example playbook: install php modules
```yaml
---
- hosts: all
  become: true

  vars:
    php_merge:
      php:
        version: "7.4"
      php_repo:
        enabled: true
      php_install:
        enabled: true
        packages: [common, fpm] # <-- install custom modules

  tasks:
  - name: include role darexsu.php
    include_role: 
      name: darexsu.php
    
```
##### Example playbook: php.ini
```yaml
---
- hosts: all
  become: true

  vars:
    php_merge:
      php:
        version: "7.4"
      php_repo:
        enabled: false     # <-- set "true", if php already installed from third-party
      php_ini:             # <-- setup php.ini
        enabled: true
        vars:
          php:
            engine: "On"
            short_open_tag: "Off"
            precision: "14"
            output_buffering: "4096"
            zlib_output_compression: "Off"
            implicit_flush: "Off"
  
  tasks:
  - name: include role darexsu.php
    include_role: 
      name: darexsu.php
    
```
##### Example playbook: php-fpm tcp/ip socket
```yaml
---
- hosts: all
  become: yes

  vars:
    php_merge:
      php:
        version: "7.4"
      php_repo:
        enabled: false     # <-- set "true", if php already installed from third-party
      php_fpm:             # <-- setup php-fpm
        enabled: true
        file: ["www.conf"]
        vars:
          webserver_user: "www-data"
          webserver_group: "www-data"
          pm: "dynamic"
          pm_max_children: "10"
          pm_start_servers: "5"
          pm_min_spare_servers: "5"
          pm_max_spare_servers: "5"
          pm_max_requests: "500"
          tcp_ip_socket:  # <-- setup php-fpm tcp/ip socket
            enabled: true
            listen: "127.0.0.1:9000"
  
  tasks:
  - name: include role darexsu.php
    include_role: 
      name: darexsu.php
    

```
##### Example playbook: php-fpm unix socket
```yaml
---
- hosts: all
  become: yes


  vars:
    php_merge:
      php:
        version: "7.4"
      php_repo:
        enabled: false     # <-- set "true", if php already installed from third-party
      php_fpm: 
        enabled: true
        file: ["www.conf"]
        vars:
          webserver_user: "www-data"
          webserver_group: "www-data"
          pm: "dynamic"
          pm_max_children: "10"
          pm_start_servers: "5"
          pm_min_spare_servers: "5"
          pm_max_spare_servers: "5"
          pm_max_requests: "500"
          unix_socket:     # <-- setup php-fpm unix socket
            enabled: true
            file: "php{{ php.version }}-fpm.sock"
            user: "www-data"
            group: "www-data"

    tasks:
    - name: include role darexsu.php
      include_role: 
        name: darexsu.php

```