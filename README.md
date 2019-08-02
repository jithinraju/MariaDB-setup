# MariaDB installation and setup

Playbook for installing and configuring MariaDB in a Redhat based Server

## Variables

    mysql_root: Root password for MariaDB
    mysql_user: MariaDB user
    mysql_password: Password for MariaDB user
    mysql_db: Database


```bash

---
- name: 'mariadb'
  hosts: all
  become: yes
  vars:
    mysql_root: mysqlroot123
    mysql_user: wpuser
    mysql_password: wppassword
    mysql_db: wordpress
  pre_tasks:
       - name: 'mysql extension'
         yum: name=MySQL-python state=present
  tasks:
      - name: 'mysql install'
        yum: name=mariadb-server state=present

      - name: 'restart'
        service: name=mariadb state=restarted enabled=yes

      - name: 'reset root password'
        ignore_errors: yes
        mysql_user:
              login_user: root
              login_password: ''
              name: root
              password: "{{mysql_root}}"
              host_all: yes

      - name: 'anonymous'
        mysql_user:
              login_user: root
              login_password: "{{mysql_root}}"
              name: ''
              state: absent
              host_all: yes

      - name: 'db creation'
        mysql_db:
              login_user: root
              login_password: "{{mysql_root}}"
              name: "{{mysql_db}}"
              state: present

      - name: 'privilage'
        mysql_user:
               login_user: root
               login_password: "{{mysql_root}}"
               name: "{{mysql_user}}"
               password: "{{mysql_password}}"
               state: present
               host: localhost
               priv: "{{mysql_db}}.*:ALL"

```
