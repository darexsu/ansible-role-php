# Ansible role PHP 

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
2) Example playbook

```yaml
---
- hosts: all

  roles:
    - role: darexsu.php
      vars:
        php_install: true
        php_settings: true
```