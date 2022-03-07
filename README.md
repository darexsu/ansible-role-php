# Ansible role PHP 7.x, 8.x

[![CI Molecule](https://github.com/darexsu/ansible-role-php/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-php/actions/workflows/ci.yml)&emsp;![](https://img.shields.io/static/v1?label=idempotence&message=ok&color=success)&emsp;![Ansible Role](https://img.shields.io/ansible/role/d/57603?color=blue&label=downloads)

|  Testing         |  Debian            |  Ubuntu         |  Rocky Linux  | Oracle Linux |
| :--------------: | :----------------: | :-------------: | :-----------: | :----------: |
| Distro release   |  10, 11            | 18.04, 20.04    |  8            | 8            |
| Third-party repo |  Sury              |   ppa:andrej    |  Remi         | Remi         |

### 1) Install role from Galaxy
```
ansible-galaxy install darexsu.php --force
```

### 2) Example playbooks: 
  
  - [full playbook](#full-playbook)  
    - install
      - [official repo](#install-from-official-repo)
      - [third-party repo](#install-from-third-party-repo)
      - [php modules](#install-php-modules) 
    - config
      - [php.ini](#configure-phpini)
      - [php-fpm tcp/ip socket](#configure-php-fpm-tcpip-socket)
      - [php-fpm unix socket](#configure-php-fpm-unix-socket)
      - [add multiple configs ](#add-multiple-configs)

Replace or Merge dictionaries (with "hash_behaviour=replace" in ansible.cfg):
```
# Replace             # Merge
---                   ---
  vars:                 vars:
    dict:                 merge:
      a: "value"            dict: 
      b: "value"              a: "value" 
                              b: "value"

# How does merge work?:
Your vars [host_vars]  -->  default vars [current role] --> default vars [include role]
  
  dict:          dict:              dict:
    a: "1" -->     a: "1"    -->      a: "1"
                   b: "2"    -->      b: "2"
                                      c: "3"
    
```
##### Full playbook
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # PHP
      php:
        enabled: true
        version: "7.4"
        src: "third_party"
      # PHP -> install
      php_install:
        enabled: true
        packages: [fpm]    
      # PHP -> config -> php.ini
      php_ini:
        enabled: true      
      # PHP -> config -> php-fpm pool
      php_fpm:
      # PHP -> config -> php-fpm pool -> delete www.conf
        www_conf:
          enabled: true
          state: "absent"
      # PHP -> config -> php.fpm pool -> add new.conf
        new_conf:
          enabled: true
          file: "new.conf"
          state: "present"
          src: "php_fpm.j2"
          backup: true
          vars:
            webserver_user: "www-data"
            webserver_group: "www-data"
            pm: "dynamic"
            pm_max_children: "10"
            pm_start_servers: "5"
            pm_min_spare_servers: "5"
            pm_max_spare_servers: "5"
            pm_max_requests: "500"
      # PHP -> config -> php.fpm pool -> new.conf -> enable unix-socket
            unix_socket:
              enabled: true
              file: "php{{ php.version }}-fpm.sock"
              user: "www-test"
              group: "www-test"  

  tasks:
  - name: include role darexsu.php
    include_role: 
      name: darexsu.php  

```
##### install from official repo
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # PHP
      php:
        enabled: true
        version: "7.4"
      # PHP -> enable distribution repo
        src: "distribution" 
      php_install:
        enabled: true
        packages: [fpm]    
  
  tasks:
  - name: include role darexsu.php
    include_role: 
      name: darexsu.php    
  
```
##### install from third-party repo
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # PHP
      php:
        enabled: true
        version: "7.4"
      # PHP -> enable third-party repo
        src: "third_party"      
      php_install:
        enabled: true
        packages: [fpm]    
  
  tasks:
  - name: include role darexsu.php
    include_role: 
      name: darexsu.php   
  
```
##### install php modules
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # PHP
      php:
        enabled: true
        version: "7.4"
        src: "third_party"
      # PHP -> install
      php_install:
        enabled: true
      # PHP -> install -> install custom modules
        packages: [common, fpm, gd]

  tasks:
  - name: include role darexsu.php
    include_role: 
      name: darexsu.php
    
```
##### configure php.ini
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # PHP
      php:
        enabled: true
        version: "7.4"
        src: "third_party"
      # PHP -> install
      php_install:
        enabled: true
        packages: [fpm]    
      # PHP -> config -> php.ini
      php_ini:
        enabled: true
        vars:
          php:
            engine: "On"
            short_open_tag: "Off"
            precision: "14"
            output_buffering: "4096"
            zlib_output_compression: "Off"
          # ...
  
  tasks:
  - name: include role darexsu.php
    include_role: 
      name: darexsu.php
    
```
##### configure php-fpm tcp/ip socket
```yaml
---
- hosts: all
  become: yes

  vars:
    merge:
      # PHP
      php:
        enabled: true
        version: "7.4"
        src: "third_party"
      # PHP -> config -> php-fpm pool
      php_fpm:
        www_conf:
          enabled: true
          state: "absent"
        new_conf:
          enabled: true
          file: "new.conf"
          state: "present"
          src: "php_fpm.j2"
          backup: true          
          vars:
            webserver_user: "www-data"
            webserver_group: "www-data"
            pm: "dynamic"
            pm_max_children: "10"
            pm_start_servers: "5"
            pm_min_spare_servers: "5"
            pm_max_spare_servers: "5"
            pm_max_requests: "500"
      # PHP -> config -> php-fpm pool -> enable tcp/ip socket   
            tcp_ip_socket:
              enabled: true
              listen: "127.0.0.1:9000"
      # PHP -> config -> php-fpm pool -> disable unix socket
            unix_socket:
              enabled: false
              file: "php{{ php.version }}-fpm.sock"
              user: "www-test"
              group: "www-test"  
  
  tasks:
  - name: include role darexsu.php
    include_role: 
      name: darexsu.php    

```
##### configure php-fpm unix socket
```yaml
---
- hosts: all
  become: yes

  vars:
    merge:
      # PHP
      php:
        enabled: true
        version: "7.4"
        src: "third_party"
      # PHP -> config -> php-fpm pool
      php_fpm:
        www_conf:
          enabled: true
          state: "absent"
        new_conf:
          enabled: true
          file: "new.conf"
          state: "present"
          src: "php_fpm.j2"
          backup: true         
          vars:
            webserver_user: "www-data"
            webserver_group: "www-data"
            pm: "dynamic"
            pm_max_children: "10"
            pm_start_servers: "5"
            pm_min_spare_servers: "5"
            pm_max_spare_servers: "5"
            pm_max_requests: "500"
      # PHP -> config -> php-fpm pool -> disable tcp/ip socket       
            tcp_ip_socket:
              enabled: false
              listen: "127.0.0.1:9000"
      # PHP -> config -> php-fpm pool -> enable unix socket
            unix_socket:
              enabled: true
              file: "php{{ php.version }}-fpm.sock"
              user: "www-test"
              group: "www-test"  

    tasks:
    - name: include role darexsu.php
      include_role: 
        name: darexsu.php
```

##### add multiple configs
```yaml
---
- hosts: all
  become: yes

  vars:
    merge:
      # PHP
      php:
        enabled: true
        version: "7.4"
        src: "third_party"
      # PHP -> config -> php-fpm pool
      php_fpm:
      # PHP -> config -> php.fpm pool -> delete www.conf  
        www_conf:
          enabled: true
          state: "absent"
      # PHP -> config -> php.fpm pool -> add new.conf
        first_conf:
          enabled: true
          file: "first.conf"
          state: "present"
          src: "php_fpm.j2"
          backup: true         
          vars:
            webserver_user: "www-data"
            webserver_group: "www-data"
            pm: "dynamic"
            pm_max_children: "10"
            pm_start_servers: "5"
            pm_min_spare_servers: "5"
            pm_max_spare_servers: "5"
            pm_max_requests: "500"      
            tcp_ip_socket:
              enabled: false
              listen: "127.0.0.1:9000"
            unix_socket:
              enabled: true
              file: "php{{ php.version }}-first-fpm.sock"
              user: "www-test"
              group: "www-test"
      # PHP -> config -> php.fpm pool -> add second.conf
        second_conf:
          enabled: true
          file: "second.conf"
          state: "present"
          src: "php_fpm.j2"
          backup: true         
          vars:
            webserver_user: "www-data"
            webserver_group: "www-data"
            pm: "dynamic"
            pm_max_children: "10"
            pm_start_servers: "5"
            pm_min_spare_servers: "5"
            pm_max_spare_servers: "5"
            pm_max_requests: "500"      
            tcp_ip_socket:
              enabled: false
              listen: "127.0.0.1:9000"
            unix_socket:
              enabled: true
              file: "php{{ php.version }}-second-fpm.sock"
              user: "www-test"
              group: "www-test"  

    tasks:
    - name: include role darexsu.php
      include_role: 
        name: darexsu.php
```