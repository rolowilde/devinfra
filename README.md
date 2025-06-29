# devinfra

Ansible playbooks and roles to configure Linux workstations, mainly Arch Linux.

DISCLAIMER: this is a public repository, and I do **not** take responsibility for any lost data that may have been caused using the playbooks.

## Configuration

Configuration is done entirely in `vars.yml` which are shared across the playbooks. It does not include all possible options due to assumed defaults so it is a good idea to check roles' default vars.

## Installation

Boot to [archiso](https://archlinux.org/download/) in UEFI mode, then install ansible and git:

```sh
pacman -Sy ansible-core git
```

Pull playbook requirements:

```sh
ansible-pull -U https://github.com/rolowilde/devinfra.git pull.yml
```

Set variables and run archiso playbook:

```sh
ansible-pull -U https://github.com/rolowilde/devinfra.git -e "dev= hostname= user_password= cryptroot_passphrase=" archiso.yml
```

**ALL DATA WILL BE WIPED ON THE SPECIFIED DRIVE!**

Reboot. Enter passphrase to decrypt root partition unless `cryptroot` was set to `false`. Pull requirements again and run archlinux playbook:

```sh
ansible-pull -U https://github.com/rolowilde/devinfra.git -K archlinux.yml
```

It will only configure the system without installing a graphical environment or other necessities. For that, run a playbook with respective roles and/or tasks. The archlinux playbook itself can be imported as the first play of such playbook.

## Playbooks

Following playbooks are meant to be run locally with `ansible-pull`:

```sh
ansible-pull -U https://github.com/rolowilde/devinfra.git -K playbook.yml
```

### pull.yml

Convenience playbook to install Ansible Galaxy requirements before executing the other playbooks.

### archiso.yml

Opinionated Arch Linux installer playbook. Only supports UEFI systems and will wipe specified drive entirely for the OS. Provides minimal setup as everything else is done in the subsequent playbook/roles.

Required vars: `dev` (block device **without** /dev/ part to install), `timezone`, `locales`, `hostname`, `user` (name), `user_password`. `root_password` is optional; superuser will be locked if unspecified.

The disk is partitioned into EFI (1 GB) and the root partition. Root is formatted to BTRFS and mounted with ZSTD:1 compression level. The playbook follows current archinstall's BTRFS subvolume [layout](https://github.com/archlinux/archinstall/blob/19459731ad9a3a320fb265f55be90bd1230d079f/archinstall/lib/interactions/disk_conf.py#L319-L322). [ZRAM](https://wiki.archlinux.org/title/Zram) is assumed to be set up after boot, thus swap is not configured (zswap is disabled as recommended by the wiki).

Encryption of the root partition is enabled by default (`cryptroot` boolean), thus `cryptroot_passphrase` is required to be set. Cryptsetup is already preconfigured with optimal defaults for modern CPUs with AES instruction set, with some options being configurable.

### archlinux.yml

Optimal Arch Linux configuration for workstations after booting into the live system. Sets up btrfs-maintenence timers, limits journald log size, uses bfq and kyber for IO scheduling, enables ZRAM, uses Ananicy with CachyOS rules for renicing processes, pulls dotfiles.

This alone does not provide a graphical interface that subsequent playbooks do.

### ga402r.yml

Playbook to configure ASUS ROG Zephyrus G14 2022 as my daily driver for development and gaming. Uses GNOME as the desktop environment and provides ASUS specific utilities for profile management, graphics switching, keyboard backlight, etc. Finally, installs a bundle of my preferred software at the end.
