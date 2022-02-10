# Ansible role PHP 

[![CI Molecule](https://github.com/darexsu/ansible-role-php/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-php/actions/workflows/ci.yml)&emsp;![Ansible Role](https://img.shields.io/ansible/role/d/57603?color=blue&label=downloads)&emsp;![](https://img.shields.io/static/v1?label=idempotence&message=ok&color=success)

Molecule testing:

| Platforms |    Debian     |    Ubuntu     |    Rocky Linux| Oracle Linux |
| --------- | ------------- | ------------- | ------------- | ------------ |
|  Version  |   10, 11      | 18.04, 20.04  |      8      |      8       |
| Repo      |  Distro, Sury    | Distro, ppa:ondrej    |  Distro, Remi    |  Distro, Remi   |

### 1) Install role from Galaxy
```
ansible-galaxy install darexsu.php --force
```

### 2) Example playbooks: 
  
  - [full playbook](#full-playbook)  
    - install
      - [from distro's repo](#example-playbook-from-distros-repo) 
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
      # - install
      php_install: true      
      php_install__version: "8.0"
      # - install  repo
      php_install__repo: true

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
##### Example playbook: from distro's repo
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.php
      # install
      php_install: true      
      php_install__version: "7.4"      
      # - install  repo
      php_install__repo: false
  
```
##### Example playbook: install php modules
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.php
      # - install
      php_install: true      
      php_install__version: "8.0"
      php_install__list: [bcmath, common, fpm, cli, gd, ldap]     
      # - install  repo
      php_install__repo: true
  
```
##### Example playbook: php.ini
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.php
      # - install  repo
      php_install__repo: true  
      
      # - config
      php_config: true
      php_config__version: "8.0"
      # - config  php.ini
      php_config__php_ini: true
      php_config__php_ini__template: "php_ini.j2"
```
##### Example playbook: php-fpm tcp/ip socket
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.php
      # - install  repo
      php_install__repo: true
      
      # - config
      php_config: true
      php_config__version: "8.0"
      # - config  php_fpm
      php_config__php_fpm: true
      php_config__php_fpm__template: "php_fpm.j2"
      php_config__php_fpm__file: "www.conf"
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
      # - install  repo
      php_install__repo: true
      
      # - config
      php_config: true
      php_config__version: "8.0"
      # - config  php_fpm
      php_config__php_fpm: true
      php_config__php_fpm__template: "php_fpm.j2"
      php_config__php_fpm__file: "www.conf"
      # - config  php_fpm  unix socket
      php_config__php_fpm__unix_socket: true
      php_config__php_fpm__unix_socket__user: "username"
      php_config__php_fpm__unix_socket__group: "username"
      php_config__php_fpm__unix_socket__name: "php{{ php_install__version }}-{{ php_config__php_fpm__unix_socket__user }}.sock"
```