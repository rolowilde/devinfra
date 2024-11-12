# devinfra

Collection of Ansible playbooks and roles for Linux workstation provisioning, mainly Arch Linux. Includes an automatic archiso installer playbook, post-installation playbooks, and GA409RK-specific playbook.

The master playbook `site.yml` is intended to be run instead of the individual playbooks located in `playbooks/`. Meta role will detect whether you're running under archiso or not, and end the play accordingly.

Localhost inventory is provided with the repository and is default.

## Playbooks

### 01-archiso.yml

Opinionated Arch Linux installer playbook. Hosts must be booted into archiso, UEFI only. Required vars: `dev` (block device **without** /dev/ part to install), `timezone`, `locales`, `hostname`, `user` (name), `user_password`. Provides only minimal setup as everything else is done in the subsequent playbook/roles. `root_password` is optional; superuser will be locked if unspecified. SSH is enabled in chroot, though `sshd` role will later take care of the port.

### 02-archlinux.yml

Contains roles to be executed post base install. Run under the user created in archiso or otherwise. Some considerations and assumptions:

- zram on swap
- services like docker and samba are configured but not enabled by default
- bare [repo](https://github.com/rowlul/dots) for dotfiles, zsh as default shell
- [ChaoticAUR](https://aur.chaotic.cx/) prefered to pkgbuild (when possible)
- libvirt-qemu set up to execute under the specified user
- random sshd port unless

### 03-extras.yml

Provisions by group or/and host (vars):

- `[group/host]_roles`
- `[group/host]_flatpak_packages`
- `[group/host]_pacman_packages`
- `[group/host]_flatpak_packages`
- `[group/host]_systemd_units`
- `[group/host]_systemd_user_units`

## Configuration

Common settings for all hosts are set in `inventories/group_vars/all.yml`. Most of the roles are configurable through vars, with some sensible defaults already set.
