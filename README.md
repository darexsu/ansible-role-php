# Ansible role PHP 

Optional:

  - install PHP {version} from Sury/Remi repositories
  - configuration ( ./pool.d/*.conf ) 
  - configuration ( ./php.ini )


|    Debian     |    Ubuntu     |    CentOS     |   CentOS     |   CentOS     |
| ------------- | ------------- | ------------- | ------------ | ------------ |
|   10, 11      | Content Cell  | Content Cell  |   CentOS     |   CentOS     |


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

3) Testing

```
php -v
```