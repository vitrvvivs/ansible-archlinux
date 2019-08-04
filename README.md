Ansible role for installing Arch on a device  
Running it with the mounts pointing to an existing Arch install will just update configuration.  

**Assumes the host is GNU/Linux, running on UEFI**.  
Tested on Arch, Alpine, and Debian.  
Running it on a distro without the install tools will compile them.  
Should also work on Fedora and anything with the same build package names.  

### Usage:
- start alpine or arch from pxe  
- run this role to with `format_device` and `authorized_hosts` install to a drive
- unset `format_device`; set `root_device`, `boot_device`, and `mount_dir`
- reboot
##### OR
- Plug in a USB or SATA drive to any linux workstation
- Run this role with `format_device`
- Plug that drive into whatever system you want

### Vars:
#### required
```
format_device     Wipes and formats a drive; '/dev/sdc'
OR
root_device       Root parition; mounts and installs at {{mount_dir}}
boot_device       UEFI parition; mounts at {{mount_dir}}/boot 
```
#### optional
```
mount_dir         where root_device should be mounted; '/mnt'
                  can be set to '/' to skip installation
no_verify_format  set to true to format {{fomat_device}} without warning
hostname
root_password
authorized_hosts  /root/.ssh/authorized_hosts contents
extra_files       local directory to be merged into mount_dir
extra_packages    for things like networking
enable_services
timezone          something in the tz database; 'New York', 'London', 'UTC', etc.
locale            'en_US', 'zh_TW.UTF-8', etc.
```

### Examples:
#### To install in a USB drive
```ansible -K -m include_role -a name=ansible-archlinux -e format_device=/dev/sdc localhost```
#### Playbook:
```
hosts.yml

all:
  hosts:
    archbox:
      format_device: 
      mount_dir: /mnt
      hostname: testudo
      authorized_keys: |
        ssh-rsa AAAAB3NzaC1yc2EAAA...7w== vitrvvivs@onager
      timezone: UTC
      locale: en_US.UTF-8
      extra_files: testudo_root
      extra_packages:
        - wpa_supplicant
      enable_services: 
        - systemd-networkd
        - wpa_supplicant@wlan0
      root_password: !vault |
        $ANSIBLE_VAULT;1.1;AES256
        ...
      ansible_user: root

arch.yml
---
- hosts:
    - nas
  roles:
    - archlinux
```
```
ansible-playbook --vault-password-file=vault_password -i hosts.yml arch.yml
```
