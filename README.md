# devinfra

Ansible playbooks and roles to configure Linux workstations, mainly Arch Linux.

## Playbooks

### archiso.yml

Opinionated Arch Linux installer playbook. Hosts must be booted into archiso, UEFI only. Required vars:
`dev` (block device **without** /dev/ part to install), `timezone`, `locales`, `hostname`, `user` (name), `user_password`.
Provides only minimal setup as everything else is done in the subsequent playbook/roles.
`root_password` is optional; superuser will be locked if unspecified.

Encryption of the root partition is enabled by default (`cryptroot` boolean), thus `cryptroot_passphrase` is required to be set. Cryptsetup is already preconfigured with optimal defaults for modern CPUs with AES instruction set, with some options being configurable.

### archlinux.yml

Contains roles to be executed post base install. Run under the user created in archiso or otherwise. Some considerations and assumptions:

- zram on swap
- services like docker and samba are configured but not enabled by default
- bare [repo](https://github.com/rolowilde/dots) for dotfiles
- [ChaoticAUR](https://aur.chaotic.cx/) instead of AUR pkgbuild
- libvirt-qemu set up to execute under the specified user
- random sshd port unless `ssh_port` is set

### linux.yml

Configures a generic Linux host depending on set vars.

## Configuration

Common settings for all hosts are set in `group_vars/all.yml`. Individual hosts may want to have vars set in `host_vars/`. Some roles have their default configuration that may be overriden.
