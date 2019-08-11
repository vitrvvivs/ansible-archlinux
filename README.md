Ansible role for installing and configuring Arch on a device  
Running it with the mounts pointing to an existing Arch install will just update configuration.  

**Assumes the host is GNU/Linux, booted from on UEFI**.  
Tested on Arch, Alpine, and Debian.  
Running it on a distro without the install tools (i.e. Debian) will compile them.  

### Usage:
- start alpine (or something) from pxe
- put this role first with `format_device`, `authorized_hosts`, `reboot=true` set
- next run, remove `format_device`, set `boot_device`, `root_device`, and `mount_dir=/`
##### OR
- Plug in a USB or SATA drive to any linux workstation
- Run this role with `format_device`
- Plug that drive into whatever system you want

##### Once installed
- unset `format_device`; set `root_device`, `boot_device`, and `mount_dir`
- run again to reapply configuration options

### Vars:
#### required
```
format_device     Wipe and format a drive; '/dev/sdc'
                  Sets 'root_device' and 'boot_device' automatically after format
root_device       Root parition; mounts and installs at {{mount_dir}}
boot_device       UEFI parition; mounts at {{mount_dir}}/boot 
mount_dir         where root_device should be mounted
                  if {{mount_dir}}/etc/arch-release exists, installation is skipped
                  default: '/mnt'
```
#### optional
```
no_verify_format  set to true to format {{fomat_device}} without warning
hostname          new hostname
root_password     new root password
authorized_hosts  /root/.ssh/authorized_hosts contents
extra_files       local directory to be merged into new / directory
extra_packages    list of packages to install from the Arch repository
		  default: ['python', 'openssh']
enable_services   list of systemd services to start at boot
                  default: 'sshd'
local_packages    list of local package builds;
                  '~/build/linux-ck.tar.xz', 'ftp://my-fileserver/package.tar.xz'
timezone          something in the tz database; 'New York', 'London', 'UTC', etc.
locale            'en_US', 'zh_TW.UTF-8', etc.
reboot            reboot after fresh install; boolean
                  default: 'no'
```

### Examples:
#### Simple install on a USB drive
```ansible -K -m include_role -a name=ansible-archlinux -e format_device=/dev/sdc localhost```
#### Exmaple playbook:
```
hosts.yml

all:
  hosts:
    archpxe:
      format_device: /dev/sda
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
ansible-playbook --vault-password-file=~/.ansible/vault_password -i hosts.yml arch.yml
```
