## Role info

> Ansible Role to Install Tomcat 9 on CentOS, Fedora, Debian and Ubuntu Linux.


## Tested on the following operating systems

- CentOS 8
- CentOS 7
- Fedora 31
- Ubuntu 18.04
- Debian 10

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
[tomcat-nodes]
192.168.10.10
```

- Update variables in playbook file

```
$ vim tomcat-setup.yml 
- name: Tomcat deployment playbook
  hosts: tomcat-nodes     # Inventory hosts group / server to act on
  become: yes             # If to escalate privilege
  become_method: sudo     # Set become method
  remote_user: root       # Update username for remote server
  vars:
    tomcat_ver: 9.0.30    # Tomcat version to install
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
