# Archlinux UEFI build with Packer and Ansible

Credits: [binary-many/arch-ansible](https://github.com/binary-manu/arch-ansible)

An Archlinux build using Packer and Ansible based on the build by `binary-manu`. I wanted a base Archlinux build where I could test out various desktop environments. This will produce a Virtualbox image that can be imported.

## Versions used

- VirtualBox 6.0.14
- Packer 1.4.4
- Ansible 2.9
- Archlinux 2019-11-01

## Installed system

3 partition system using GRUB efibootmgr. 
/dev/sda1 256 MiB esp 
/dev/sda2 4 GiB swap
/dev/sda3 37 GiB /

The partion role does have parameters to create a home directory but was not used.

## Users

Edit `ansible\roles\users\defaults\main.yaml.txt` with accounts and words as needed, then save as `main.yaml`.