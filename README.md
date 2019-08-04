Ansible role for install Arch on a device  
Assumes the host is GNU/Linux, tested on Arch, Alpine, and Debian.  
Running it from a non-Arch based distro will compile dependencies.

proposal:  
start livecd/pxe and setup ssh  
run ansible-playbook to provision basic system

required vars:  
    format_device to completely wipe  
    or root_device and boot_device to install onto  

optional vars:
    mount_dir - where root_device should be mounted
    hostname
    root_password
    authorized_hosts
    extra_packages
    extra_files - local directory to be merged into mount_dir
    enable_services
    timezone - something in the tz database; 'New York', 'London', 'UTC', etc.
    locale - 'en_US', 'zh_TW.UTF-8', etc.

example usage:
    To install in a USB drive
    `ansible -K -m include_role -a name=ansible-archlinux -e format_device=/dev/sdd localhost`
