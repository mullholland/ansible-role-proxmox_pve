# [proxmox_pve](#proxmox_pve)

|GitHub|GitLab|
|------|------|
|[![github](https://github.com/mullholland/ansible-role-proxmox_pve/workflows/Ansible%20Molecule/badge.svg)](https://github.com/mullholland/ansible-role-proxmox_pve/actions)|[![gitlab](https://gitlab.com/mullholland/ansible-role-proxmox_pve/badges/main/pipeline.svg)](https://gitlab.com/mullholland/ansible-role-proxmox_pve)|

description

## [Role Variables](#role-variables)

These variables are set in `defaults/main.yml`:
```yaml
---
# Mostly tested and planned for Proxmox 7+/Debian 11 Bullseye
# Older versions may work

# https://pve.proxmox.com/wiki/Package_Repositories#repos_secure_apt
proxmox_pve_repository_key: "https://enterprise.proxmox.com/debian/proxmox-release-bullseye.gpg"

# manages the Proxmox /etc/apt/sources.list
proxmox_pve_enable_default_repository: true

# Disable Enterprise Repository
# https://pve.proxmox.com/wiki/Package_Repositories#sysadmin_enterprise_repo
proxmox_pve_enable_enterprise_repository: false

# Enable the no subscription repository
# https://pve.proxmox.com/wiki/Package_Repositories#sysadmin_no_subscription_repo
# NOT recommended for production use
proxmox_pve_enable_no_subscription_repository: true

# Enable the Ceph Repository
# https://pve.proxmox.com/wiki/Package_Repositories#sysadmin_package_repositories_ceph
proxmox_pve_enable_ceph_repository: false
# default is "pacific" can be "octopus" for legacy installations
proxmox_pve_enable_ceph_repository_release: "pacific"
# possible "main" or "test"
proxmox_pve_enable_ceph_repository_version: "main"

# manage cpu govenor
proxmox_pve_cpu_govenor_manage: true
# possible values
# - "conservative"
# - "ondemand"
# - "userspace"
# - "powersave"
# - "performance"
# - "schedutil"
proxmox_pve_cpu_govenor: "performance"
proxmox_pve_cpu_govenor_conf: "/etc/default/cpufrequtils"
proxmox_pve_cpu_govenor_dependencies:
  - "cpufrequtils"

# install pve dark theme (https://github.com/Weilbyte/PVEDiscordDark)
# Setting can be
# - install   => install the dark theme
# - update    => install/updates the dark theme
# - uninstall => unisntall the dark theme
proxmox_pve_dark_theme: "update"
proxmox_pve_dark_theme_install_options: ""
```


## [Example Playbook](#example-playbook)

This example is taken from `molecule/default/converge.yml` and is tested on each push, pull request and release.
```yaml
---
- name: Converge
  hosts: all
  become: true
  gather_facts: true
  vars:
    # molecule tests ins docker without working proxmox installation
    proxmox_pve_dark_theme: "proxmox"

  roles:
    - role: "mullholland.proxmox_pve"
```

The machine needs to be prepared in CI this is done using `molecule/default/prepare.yml`:
```yaml
---
- name: Prepare
  hosts: all
  become: true
  gather_facts: true

  tasks:
    - name: Copy PVE Repository Template
      ansible.builtin.copy:
        content: |
            deb https://enterprise.proxmox.com/debian/pve {{ ansible_distribution_release }} pve-enterprise
        dest: /etc/apt/sources.list.d/pve-enterprise.list
```





## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/mullholland):

-   [debian10](https://hub.docker.com/r/mullholland/docker-molecule-debian10)
-   [debian11](https://hub.docker.com/r/mullholland/docker-molecule-debian11)

The minimum version of Ansible required is 2.10, tests have been done to:

-   The previous versions.
-   The current version.



## [Exceptions](#exceptions)

Some variations of the build matrix do not work. These are the variations and reasons why the build won't work:

| variation                 | reason                 |
|---------------------------|------------------------|
| Redhat/CentOS/Fedora | Proxmox is based on Debian |
| Rocky Linux | Proxmox is based on Debian |
| Almalinux | Proxmox is based on Debian |
| Ubuntu | Proxmox is based on Debian |
| AmazonLinux | Proxmox is based on Debian |


If you find issues, please register them in [GitHub](https://github.com/mullholland/ansible-role-proxmox_pve/issues)

## [License](#license)

MIT


## [Author Information](#author-information)

[Mullholland](https://github.com/mullholland)

## [Special Thanks](#special-thanks)

Template inspired by [Robert de Bock](https://github.com/robertdebock)
