# Ansible role PHP 

[![CI Molecule](https://github.com/darexsu/ansible-role-php/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-php/actions/workflows/ci.yml)

Molecule testing:

| Platforms |    Debian     |    Ubuntu     |    CentOS     |  Rocky Linux |
| --------- | ------------- | ------------- | ------------- | ------------ |
|  Version  |   10, 11      | 18.04, 20.04  |     7, 8      |      8       |
| Repository |  Sury    | ppa:ondrej    | Epel, Remi    | Epel, Remi   |

### 1) Install role from Galaxy
```
ansible-galaxy install darexsu.php --force
```

### 2) Example playbooks: 
  
  - [full playbook](#full-playbook)  
    - install
      - [php modules](#example-playbook-install-php-modules) 
    - config
      - [php.ini](#example-playbook-phpini)
      - [php-fpm tcp/ip socket](#example-playbook-php-fpm-tcpip-socket)
      - [php-fpm unix socket](#example-playbook-php-fpm-unix-socket)


##### Full playbook
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.php
      # install
      php_install: true      
      php_install__version: "8.0"

      # config 
      php_config: true
      # - config  php.ini
      php_config__php_ini: true
      # - config  php_fpm 
      php_config__php_fpm: true     
      # - config  php_fpm  tcp_ip_socket
      php_config__php_fpm__tcp_ip_socket: true
      php_config__php_fpm__tcp_ip_socket__listen: "127.0.0.1:9000"

```
##### Example playbook: install php modules
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.php
      # install
      php_install: true      
      php_install__version: "8.0"
      php_install__list: [bcmath, common, fpm, cli, gd, ldap]
  
```
##### Example playbook: php.ini
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.php
      # - config
      php_config: true
      php_config__version: "8.0"
      # - config  php_fpm
      php_config__php_fpm: true
```
##### Example playbook: php-fpm tcp/ip socket
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.php
      # - config
      php_config: true
      php_config__version: "8.0"
      # - config  php_fpm
      php_config__php_fpm: true
      # - config  php_fpm  tcp_ip_socket
      php_config__php_fpm__tcp_ip_socket: true
      php_config__php_fpm__tcp_ip_socket_listen: "127.0.0.1:9000"
```
##### Example playbook: php-fpm unix socket
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.php
      # - config
      php_config: true
      php_config__version: "8.0"
      # - config  php_fpm
      php_config__php_fpm: true
      # - config  php_fpm  unix socket
      php_config__php_fpm__unix_socket: false
      php_config__php_fpm__unix_socket__user: "username"
      php_config__php_fpm__unix_socket__group: "username"
      php_config__php_fpm__unix_socket__listen: "/run/php/php{{ php_install__version }}-{{ php_config__php_fpm__unix_socket__user }}.sock"
```