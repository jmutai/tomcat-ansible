# Changelog

## v2.0.0 (2026-04-08)

### Breaking Changes

- **Variable renames**: All role variables now use `tomcat_` prefix. See README migration section for the full rename map
- **Install path**: Default changed from `/usr/share/tomcat` to `/opt/tomcat` (configurable via `tomcat_install_dir`)
- **Passwords removed from playbook**: Use `ansible-vault` or `--extra-vars` instead
- **`ansible.posix` collection required**: Needed for firewall management on RedHat family

### New Features

- **Multi-version support**: Install Tomcat 9, 10, or 11 by setting `tomcat_version` and `tomcat_major_version`
- **Configurable Java version**: Set `tomcat_java_version` to 11, 17, or 21
- **JVM memory tuning**: Control heap size and CATALINA_OPTS via variables, deployed as `setenv.sh`
- **Manager IP restriction**: `tomcat_manager_allowed_ips` controls which IPs can access the Manager and Host Manager apps
- **Health checks**: Post-install verification that Tomcat is running and responding on the configured port
- **Preflight validation**: Catches invalid inputs early with clear error messages
- **Ansible Galaxy metadata**: `meta/main.yml` for Galaxy compatibility
- **Molecule tests**: Automated testing on Ubuntu 24.04, 22.04, Debian 12, and Rocky Linux 9

### Improvements

- **DRY task structure**: Eliminated 85% code duplication between Debian and RedHat task files
- **Modern Ansible syntax**: FQCNs throughout, no deprecated modules or loops
- **Systemd improvements**: `Restart=on-failure` for automatic recovery, proper daemon-reload
- **File permissions**: `tomcat-users.xml` deployed with mode 0600
- **Configurable everything**: Install directory, ports, service name, firewall, utilities

### Removed

- Hardcoded credentials from playbook file
- `apt-transport-https` check (no longer needed on modern Debian/Ubuntu)
- `yum` module usage (replaced with `dnf`)
- Duplicate OS-specific task files (merged into common tasks)

## v1.x

- Original release supporting Tomcat 10 on CentOS/Rocky/AlmaLinux 9/8, Fedora 40, Ubuntu 24.04/22.04/20.04/18.04, Debian 12/11/10
