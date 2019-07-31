Ansible role for install Arch on a device

proposal:
	start livecd/pxe and setup ssh
	run ansible-playbook to provision basic system

requires vars set:
	format_device to completely wipe
	or root_device and boot_device to install onto
