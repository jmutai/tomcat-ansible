## Role info

> Ansible Role to Install Tomcat on CentOS, Fedora, Debian and Ubuntu Linux. This works for any version of Tomcat: https://computingforgeeks.com/install-tomcat-on-ubuntu-centos-with-ansible/


## Tested on the following operating systems

- CentOS / Rocky / AlmaLinux 9|8
- Fedora 40
- Ubuntu 24.04/22.04/20.04/18.04
- Debian 12/11/10

## Tasks in the role

This role contains tasks to:

- Install basic packages required
- Install Java
- Add tomcat user and group
- Download tomcat and install - configure systemd
- Configure firewall

## How to use this role

- Clone the Project:

```
$ git clone https://github.com/jmutai/tomcat-ansible.git
$ cd tomcat-ansible
```

- Update your inventory, e.g:

```
$ vim hosts
[tomcat_nodes]
192.168.10.20       # Remote user to act on
```

- Update variables in playbook file - Set Tomcat version, remote user and Tomcat UI access credentials
- Update ansible.cfg file if necessary

```
$ vim tomcat-setup.yml
---
- name: Tomcat deployment playbook
  hosts: tomcat_nodes       # Inventory hosts group / server to act on
  become: yes               # If to escalate privilege
  become_method: sudo       # Set become method
  remote_user: root         # Update username for remote server
  vars:
    tomcat_ver: 10.1.19                         # 9.0.64 - Tomcat full version number to install, see https://archive.apache.org/dist/tomcat/
    tomcat_v_num: 10                            # Tomcat major release number, e.g 10 or 9
    ui_manager_user: manager                    # User who can access the UI manager section only
    ui_manager_pass: Str0ngManagerP@ssw3rd      # UI manager user password
    ui_admin_username: admin                    # User who can access bpth manager and admin UI sections
    ui_admin_pass: Str0ngAdminP@ssw3rd          # UI admin password
  roles:
    - tomcat
```

If you are using non root remote user, then set username and enable sudo:

```
become: yes
become_method: sudo
```

## Running Playbook

Once all values are updated, you can then run the playbook against your nodes.

Playbook executed as root user - with ssh key:

```
$ ansible-playbook -i hosts tomcat-setup.yml
```

Playbook executed as root user - with password:

```
$ ansible-playbook -i hosts tomcat-setup.yml --ask-pass
```

Playbook executed as sudo user - with password:

```
$ ansible-playbook -i hosts tomcat-setup.yml --ask-pass --ask-become-pass
```

Playbook executed as sudo user - with ssh key and sudo password:

```
$ ansible-playbook -i hosts tomcat-setup.yml --ask-become-pass
```

Playbook executed as sudo user - with ssh key and passwordless sudo:

```
$ ansible-playbook -i hosts tomcat-setup.yml --ask-become-pass
```

Execution should be successful without errors:

```
```
