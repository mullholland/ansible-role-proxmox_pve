# [proxmox_pve](#proxmox_pve)

Manage pve repositories and asprcts of the server

|GitHub|GitLab|Quality|Downloads|Version|
|------|------|-------|---------|-------|
|[![github](https://github.com/mullholland/ansible-role-proxmox_pve/workflows/Ansible%20Molecule/badge.svg)](https://github.com/mullholland/ansible-role-proxmox_pve/actions)|[![gitlab](https://gitlab.com/opensourceunicorn/ansible-role-proxmox_pve/badges/master/pipeline.svg)](https://gitlab.com/opensourceunicorn/ansible-role-proxmox_pve)|[![quality](https://img.shields.io/ansible/quality/60150)](https://galaxy.ansible.com/mullholland/proxmox_pve)|[![downloads](https://img.shields.io/ansible/role/d/60150)](https://galaxy.ansible.com/mullholland/proxmox_pve)|[![Version](https://img.shields.io/github/release/mullholland/ansible-role-proxmox_pve.svg)](https://github.com/mullholland/ansible-role-proxmox_pve/releases/)|

## [Example Playbook](#example-playbook)

This example is taken from [`molecule/default/converge.yml`](https://github.com/mullholland/ansible-role-proxmox_pve/blob/master/molecule/default/converge.yml) and is tested on each push, pull request and release.

```yaml
---
- name: Converge
  hosts: all
  become: true
  gather_facts: true

  roles:
    - role: "mullholland.proxmox_pve"
```


## [Role Variables](#role-variables)

The default values for the variables are set in [`defaults/main.yml`](https://github.com/mullholland/ansible-role-proxmox_pve/blob/master/defaults/main.yml):

```yaml
---
# Mostly tested and planned for Proxmox 7+/Debian 11 Bullseye
# Older versions may work

# https://pve.proxmox.com/wiki/Package_Repositories#repos_secure_apt
proxmox_pve_repository_key: "https://enterprise.proxmox.com/debian/proxmox-release-{{ ansible_distribution_release }}.gpg"

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
```

## [Requirements](#requirements)

- pip packages listed in [requirements.txt](https://github.com/mullholland/ansible-role-proxmox_pve/blob/master/requirements.txt).


## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://mullholland.net) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/mullholland/ansible-role-proxmox_pve/png/requirements.png "Dependencies")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/mullholland):

|container|tags|
|---------|----|
|[Debian](https://hub.docker.com/repository/docker/mullholland/docker-debian-systemd/general)|all|

The minimum version of Ansible required is 2.10, tests have been done to:

- The previous version.
- The current version.
- The development version.

If you find issues, please register them in [GitHub](https://github.com/mullholland/ansible-role-proxmox_pve/issues)

## [License](#license)

[MIT](https://github.com/mullholland/ansible-role-proxmox_pve/blob/master/LICENSE).

## [Author Information](#author-information)

[Mullholland](https://mullholland.net)

Please consider [sponsoring me](https://github.com/sponsors/mullholland).
