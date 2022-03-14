# Ansible role PHP 7.x, 8.x

[![CI Molecule](https://github.com/darexsu/ansible-role-php/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-php/actions/workflows/ci.yml)&emsp;![](https://img.shields.io/static/v1?label=idempotence&message=ok&color=success)&emsp;![Ansible Role](https://img.shields.io/ansible/role/d/57603?color=blue&label=downloads)

  - Role:
      - [platforms](#platforms)
      - [install](#install)
      - [behaviour](#behaviour)
  - Playbooks (short version):
      - [install and configure: PHP](#install-and-configure-php-short-version)
          - [install: PHP from official repo](#install-php-from-official-repo-short-version)
          - [install: PHP from third-party repo](#install-php-from-third-party-repo-short-version)
          - [install: PHP modules](#install-php-modules-short-version)
          - [configure: php.ini](#configure-phpini-short-version)
          - [configure: php-fpm tcp/ip socket](#configure-php-fpm-tcpip-socket-short-version)
          - [configure: php-fpm unix socket](#configure-php-fpm-unix-socket-short-version)
          - [configure: add multiple configs ](#configure-add-multiple-configs-short-version)
  - Playbooks (full version):
      - [install and configure: PHP](#install-and-configure-php-full-version)
          - [install: PHP from official repo](#install-php-from-official-repo-full-version)
          - [install: PHP from third-party repo](#install-php-from-third-party-repo-full-version)
          - [install: PHP modules](#install-php-modules-full-version)
          - [configure: php.ini](#configure-phpini-full-version)
          - [configure: php-fpm tcp/ip socket](#configure-php-fpm-tcpip-socket-full-version)
          - [configure: php-fpm unix socket](#configure-php-fpm-unix-socket-full-version)
          - [configure: add multiple configs ](#configure-add-multiple-configs-full-version)

### Platforms

|  Testing         |  Official repo     |  Third-party repo |
| :--------------: | :----------------: | :-------------:   |
| Debian 11        |  PHP 7.4           |    Sury           |
| Debian 10        |  PHP 7.3           |    Sury           |
| Ubuntu 20.04     |  PHP 7.4           |    ppa:andrej     |
| Ubuntu 18.04     |  PHP 7.2           |    ppa:andrej     |
| Oracle Linux 8   |  PHP 7.2-7.4       |    Remi           |
| Rocky Linux 8    |  PHP 7.2-7.4       |    Remi           |

### Install

```
ansible-galaxy install darexsu.php --force
```

### Behaviour

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
##### install and configure: PHP (short version)
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
      # PHP -> install
      php_install:
        enabled: true
        packages: [common, fpm]
      # PHP -> config -> php.ini
      php_ini:
        enabled: true
        file: "php.ini"
        src: "php_ini.j2"
        backup: false
        vars:
          php:
            max_execution_time: "30"
            max_input_time: "60"
            memory_limit: "128M"
            upload_max_filesize: "2M"
      # PHP -> config -> php.fpm
      php_fpm:
        www_conf:
          enabled: true
          file: "www.conf"
          state: "present"
          vars:
            webserver_user: "www-data"
            webserver_group: "www-data"
            pm: "dynamic"
            pm_max_children: "10"
            pm_start_servers: "5"
            pm_min_spare_servers: "5"
            pm_max_spare_servers: "5"
            pm_max_requests: "500"
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
##### install: PHP from official repo (short version)
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
        src: "distribution"
      # PHP -> install
      php_install:
        enabled: true
        packages: [common, fpm]
  
  tasks:
  - name: include role darexsu.php
    include_role: 
      name: darexsu.php    
  
```
##### install: PHP from third-party repo (short version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # PHP
      php:
        enabled: true
        version: "8.0"
        src: "third_party"

      # PHP -> install
      php_install:
        enabled: true
        packages: [common, fpm]
  
  tasks:
  - name: include role darexsu.php
    include_role: 
      name: darexsu.php   
  
```
##### install: php modules (short version)
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
        src: "distribution"
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
##### configure: php.ini (short version)
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
        src: "distribution"
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
            implicit_flush: "Off"
       #    ...
  
  tasks:
  - name: include role darexsu.php
    include_role: 
      name: darexsu.php
    
```
##### configure: php-fpm tcpip-socket (short version)
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
          file: "www.conf"
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
              user: "www-data"
              group: "www-data"  
  
  tasks:
  - name: include role darexsu.php
    include_role: 
      name: darexsu.php    

```
##### configure: php-fpm unix-socket (short version)
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
          file: "www.conf"
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
              user: "www-data"
              group: "www-data"  

    tasks:
    - name: include role darexsu.php
      include_role: 
        name: darexsu.php
```

##### configure: add multiple configs (short version)
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
              user: "www-data"
              group: "www-data"
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
              user: "www-data"
              group: "www-data"  

    tasks:
    - name: include role darexsu.php
      include_role: 
        name: darexsu.php
```
##### install and configure: PHP (full version)
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
        src: "distribution"
        service:
          enabled: true
          state: "started"
      # PHP -> install
      php_install:
        enabled: true
        packages: [common, fpm]
        dependencies:
          Debian: [apt-transport-https, ca-certificates, curl, gnupg2, lsb-release]
          RedHat: []
      # PHP -> config -> php.ini
      php_ini:
        enabled: true
        file: "php.ini"
        src: "php_ini.j2"
        backup: false
        vars:
          php:
            engine: "On"
            short_open_tag: "Off"
            precision: "14"
            output_buffering: "4096"
            zlib_output_compression: "Off"
            implicit_flush: "Off"
            unserialize_callback_func: ""
            serialize_precision: "-1"
            disable_functions: ""
            disable_classes: ""
            zend_enable_gc: ""
            zend_exception_ignore_args: "On"
            zend_exception_string_param_max_len: "0"
            expose_php: "On"
            max_execution_time: "30"
            max_input_time: "60"
            memory_limit: "128M"
            error_reporting: "E_ALL & ~E_DEPRECATED & ~E_STRICT"
            display_errors: "Off"
            display_startup_errors: "Off"
            log_errors: "On"
            log_errors_max_len: "1024"
            ignore_repeated_errors: "Off"
            ignore_repeated_source: "Off"
            report_memleaks: "On"
            variables_order: "GPCS"
            request_order: "GP"
            register_argc_argv: "Off"
            auto_globals_jit: "On"
            post_max_size: "8M"
            auto_prepend_file: ""
            auto_append_file: ""
            default_mimetype: "text/html"
            default_charset: "UTF-8"
            doc_root: ""
            user_dir: ""
            enable_dl: "Off"
            file_uploads: "On"
            upload_max_filesize: "2M"
            max_file_uploads: "20"
            allow_url_fopen: "On"
            allow_url_include: "Off"
            default_socket_timeout: "60"
      # PHP -> config -> php.fpm
      php_fpm:
        www_conf:
          enabled: true
          file: "www.conf"
          state: "present"
          src: "php_fpm.j2"
          backup: false
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
              file: "php{{ php.version }}-fpm.sock"
              user: "www-data"
              group: "www-data"

  tasks:
  - name: include role darexsu.php
    include_role: 
      name: darexsu.php  

```
##### install: PHP from official repo (full version)
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
        src: "distribution"
        service:
          enabled: true
          state: "started"
      # PHP -> install
      php_install:
        enabled: true
        packages: [common, fpm]
        dependencies:
          Debian: [apt-transport-https, ca-certificates, curl, gnupg2, lsb-release]
          RedHat: []
  
  tasks:
  - name: include role darexsu.php
    include_role: 
      name: darexsu.php    
  
```
##### install: PHP from third-party repo (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # PHP
      php:
        enabled: true
        version: "8.0"
        src: "third_party"
        service:
          enabled: true
          state: "started"
      # PHP -> install
      php_install:
        enabled: true
        packages: [common, fpm]
        dependencies:
          Debian: [apt-transport-https, ca-certificates, curl, gnupg2, lsb-release]
          RedHat: []
  
  tasks:
  - name: include role darexsu.php
    include_role: 
      name: darexsu.php   
  
```
##### install: php modules (full version)
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
        src: "distribution"
        service:
          enabled: true
          state: "started"
      # PHP -> install
      php_install:
        enabled: true
      # PHP -> install -> install custom modules
        packages: [common, fpm, gd]
        dependencies:
          Debian: [apt-transport-https, ca-certificates, curl, gnupg2, lsb-release]
          RedHat: []

  tasks:
  - name: include role darexsu.php
    include_role: 
      name: darexsu.php
    
```
##### configure: php.ini (full version)
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
        src: "distribution"
        service:
          enabled: true
          state: "started"
      # PHP -> config -> php.ini
      php_ini:
        enabled: true
        file: "php.ini"
        src: "php_ini.j2"
        backup: false
        vars:
          php:
            engine: "On"
            short_open_tag: "Off"
            precision: "14"
            output_buffering: "4096"
            zlib_output_compression: "Off"
            implicit_flush: "Off"
            unserialize_callback_func: ""
            serialize_precision: "-1"
            disable_functions: ""
            disable_classes: ""
            zend_enable_gc: ""
            zend_exception_ignore_args: "On"
            zend_exception_string_param_max_len: "0"
            expose_php: "On"
            max_execution_time: "30"
            max_input_time: "60"
            memory_limit: "128M"
            error_reporting: "E_ALL & ~E_DEPRECATED & ~E_STRICT"
            display_errors: "Off"
            display_startup_errors: "Off"
            log_errors: "On"
            log_errors_max_len: "1024"
            ignore_repeated_errors: "Off"
            ignore_repeated_source: "Off"
            report_memleaks: "On"
            variables_order: "GPCS"
            request_order: "GP"
            register_argc_argv: "Off"
            auto_globals_jit: "On"
            post_max_size: "8M"
            auto_prepend_file: ""
            auto_append_file: ""
            default_mimetype: "text/html"
            default_charset: "UTF-8"
            doc_root: ""
            user_dir: ""
            enable_dl: "Off"
            file_uploads: "On"
            upload_max_filesize: "2M"
            max_file_uploads: "20"
            allow_url_fopen: "On"
            allow_url_include: "Off"
            default_socket_timeout: "60"
  
  tasks:
  - name: include role darexsu.php
    include_role: 
      name: darexsu.php
    
```
##### configure: php-fpm tcpip-socket (full version)
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
          file: "www.conf"
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
              user: "www-data"
              group: "www-data"  
  
  tasks:
  - name: include role darexsu.php
    include_role: 
      name: darexsu.php    

```
##### configure: php-fpm unix-socket (full version)
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
          file: "www.conf"
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
              user: "www-data"
              group: "www-data"  

    tasks:
    - name: include role darexsu.php
      include_role: 
        name: darexsu.php
```

##### configure: add multiple configs (full version)
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
              user: "www-data"
              group: "www-data"
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
              user: "www-data"
              group: "www-data"  

    tasks:
    - name: include role darexsu.php
      include_role: 
        name: darexsu.php
```