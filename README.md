# Ansible Role: Apache Tomcat — Install and Configure Tomcat 9, 10 & 11

[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

An Ansible role that installs and configures **Apache Tomcat** (versions 9, 10, and 11) on RHEL-based and Debian-based Linux servers. Handles Java installation, systemd service setup, JVM tuning, Manager UI credentials, firewall configuration, and post-install health checks.

**Full guide**: [Install Apache Tomcat on Linux with Ansible](https://computingforgeeks.com/install-tomcat-on-ubuntu-centos-with-ansible/)

## Table of Contents

- [Supported Platforms](#supported-platforms)
- [Requirements](#requirements)
- [Role Variables](#role-variables)
- [Quick Start](#quick-start)
- [Advanced Configuration](#advanced-configuration)
- [JVM Memory Tuning](#jvm-memory-tuning)
- [Security](#security)
- [Testing](#testing)
- [Migrating from v1](#migrating-from-v1)
- [Contributing](#contributing)
- [License](#license)
- [Author](#author)

## Supported Platforms

| OS | Versions | Status |
|----|----------|--------|
| Ubuntu | 24.04, 22.04 | Tested |
| Debian | 12 | Tested |
| Rocky Linux | 9 | Tested |
| AlmaLinux | 9 | Tested |
| Fedora | 40, 41 | Tested |
| RHEL | 9 | Compatible |

## Requirements

- Ansible >= 2.15
- `ansible.posix` collection (for firewalld on RHEL family)
- Target server with internet access (to download the Tomcat archive)

Install collection dependencies:

```
ansible-galaxy collection install -r requirements.yml
```

## Role Variables

### Tomcat Version

| Variable | Default | Description |
|----------|---------|-------------|
| `tomcat_version` | `"10.1.34"` | Full Tomcat version to install |
| `tomcat_major_version` | `"10"` | Major version number (9, 10, or 11) |

### Installation

| Variable | Default | Description |
|----------|---------|-------------|
| `tomcat_install_dir` | `"/opt/tomcat"` | Installation directory |
| `tomcat_service_name` | `"tomcat"` | Systemd service name |
| `tomcat_user` | `"tomcat"` | System user for the Tomcat process |
| `tomcat_group` | `"tomcat"` | System group for the Tomcat process |
| `tomcat_java_version` | `"17"` | Java version to install (11, 17, or 21) |

### Network

| Variable | Default | Description |
|----------|---------|-------------|
| `tomcat_port` | `8080` | HTTP connector port |
| `tomcat_shutdown_port` | `8005` | Shutdown port |

### JVM Tuning

| Variable | Default | Description |
|----------|---------|-------------|
| `tomcat_jvm_memory_min` | `"512M"` | Initial heap size (-Xms) |
| `tomcat_jvm_memory_max` | `"1024M"` | Maximum heap size (-Xmx) |
| `tomcat_catalina_opts` | See defaults/main.yml | Full CATALINA_OPTS string |
| `tomcat_java_opts` | `""` | Additional JAVA_OPTS |

### Web UI Credentials

| Variable | Default | Description |
|----------|---------|-------------|
| `tomcat_manager_user` | `"manager"` | Manager UI username |
| `tomcat_manager_password` | `"changeme"` | Manager UI password |
| `tomcat_admin_user` | `"admin"` | Admin UI username |
| `tomcat_admin_password` | `"changeme"` | Admin UI password |
| `tomcat_manager_allowed_ips` | `".*"` | Regex for allowed IPs to access Manager |

### Features

| Variable | Default | Description |
|----------|---------|-------------|
| `tomcat_configure_firewall` | `true` | Open Tomcat port in firewalld (RedHat only) |
| `tomcat_health_check_enabled` | `true` | Run post-install health check |
| `tomcat_install_utilities` | `true` | Install common sysadmin packages |
| `tomcat_extra_packages` | `[]` | Additional packages to install |

## Quick Start

1. Clone the repository:

```
git clone https://github.com/jmutai/tomcat-ansible.git
cd tomcat-ansible
```

2. Add your server IPs to the inventory:

```
vim hosts
```

```ini
[tomcat_nodes]
10.0.1.50
10.0.1.51
```

3. Run the playbook:

```
ansible-playbook tomcat-setup.yml
```

This installs Tomcat 10.1.34 with Java 17 using default settings. Override any variable with `-e`:

```
ansible-playbook tomcat-setup.yml -e "tomcat_version=11.0.6 tomcat_major_version=11"
```

## Advanced Configuration

### Installing Tomcat 9

```
ansible-playbook tomcat-setup.yml \
  -e "tomcat_version=9.0.98 tomcat_major_version=9"
```

### Installing Tomcat 11

```
ansible-playbook tomcat-setup.yml \
  -e "tomcat_version=11.0.6 tomcat_major_version=11"
```

### Custom Install Path

```
ansible-playbook tomcat-setup.yml \
  -e "tomcat_install_dir=/opt/tomcat-production"
```

### Custom Port

```
ansible-playbook tomcat-setup.yml -e "tomcat_port=9090"
```

### Restricting Manager Access

By default, the Manager UI is accessible from any IP. To restrict access to localhost and a specific subnet:

```
ansible-playbook tomcat-setup.yml \
  -e 'tomcat_manager_allowed_ips=127\\.0\\.0\\.1|10\\.0\\.1\\..*'
```

### Using Ansible Vault for Passwords

Create a vault file:

```
ansible-vault create vault.yml
```

Add your credentials:

```yaml
tomcat_manager_password: YourSecureManagerPass
tomcat_admin_password: YourSecureAdminPass
```

Run with vault:

```
ansible-playbook tomcat-setup.yml -e @vault.yml --ask-vault-pass
```

### Using Group Variables

Create `group_vars/tomcat_nodes/main.yml`:

```yaml
tomcat_version: "11.0.6"
tomcat_major_version: "11"
tomcat_java_version: "21"
tomcat_jvm_memory_max: "2048M"
tomcat_manager_allowed_ips: '127\.0\.0\.1'
```

## JVM Memory Tuning

The role deploys a `setenv.sh` script to `CATALINA_HOME/bin/` for JVM configuration. Adjust heap size with:

```yaml
tomcat_jvm_memory_min: "1024M"
tomcat_jvm_memory_max: "2048M"
```

For full control over JVM options:

```yaml
tomcat_catalina_opts: "-Xms1024M -Xmx2048M -XX:+UseG1GC -Djava.awt.headless=true -Djava.security.egd=file:/dev/urandom"
tomcat_java_opts: "-Dfile.encoding=UTF-8"
```

## Security

- **Passwords**: Never commit plaintext passwords. Use `ansible-vault` or pass them via `--extra-vars` at runtime
- **Manager access**: Set `tomcat_manager_allowed_ips` to restrict which IPs can reach `/manager/html` and `/host-manager/html`
- **Firewall**: Enabled by default on RedHat family. Opens only the Tomcat HTTP port
- **File permissions**: `tomcat-users.xml` is deployed with mode `0600`, readable only by the tomcat user
- **Service hardening**: The systemd unit runs as a dedicated `tomcat` user with auto-restart on failure

## Testing

### Automated Testing with Molecule

The role includes [Molecule](https://molecule.readthedocs.io/) tests using Docker:

```
pip install molecule molecule-docker
molecule test
```

This runs the full test sequence (create, converge, idempotence, verify, destroy) against Ubuntu 24.04, Ubuntu 22.04, Debian 12, and Rocky Linux 9 containers.

### Manual Testing

Run the playbook against your servers and verify:

```
ansible-playbook tomcat-setup.yml
```

Check Tomcat is running:

```
curl -s -o /dev/null -w "%{http_code}" http://your-server:8080
```

## Migrating from v1

If upgrading from the previous version of this role, note these breaking changes:

### Renamed Variables

| Old Variable | New Variable |
|---|---|
| `tomcat_ver` | `tomcat_version` |
| `tomcat_v_num` | `tomcat_major_version` |
| `ui_manager_user` | `tomcat_manager_user` |
| `ui_manager_pass` | `tomcat_manager_password` |
| `ui_admin_username` | `tomcat_admin_user` |
| `ui_admin_pass` | `tomcat_admin_password` |

### Other Changes

- **Install path** changed from `/usr/share/tomcat` to `/opt/tomcat`. Set `tomcat_install_dir: /usr/share/tomcat` to keep the old path
- **Passwords** are no longer in the playbook file. Use vault or extra-vars
- **`ansible.posix` collection** is now required for firewall tasks
- **Task structure** has been refactored. Custom task overrides will need updating

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Run `molecule test` to verify
5. Submit a pull request

## License

GPL-3.0

## Author

Maintained by [Josphat Mutai](https://computingforgeeks.com) — Senior DevOps/Platform Engineer.
