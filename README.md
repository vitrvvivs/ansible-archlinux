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
    extra_packages - to install during pacstrap
    mount_dir - where root_device should be mounted

example usage:
    To install in a USB drive
    `ansible -K -m include_role -a name=ansible-archlinux -e format_device=/dev/sdd localhost`
