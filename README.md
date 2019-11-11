# Vagrant Archlinux box with UEFI built using Packer and configured using Ansible

Credits: [binary-many/arch-ansible](https://github.com/binary-manu/arch-ansible)

This started as an effort to build a base Archlinux box where I could try out various destkop environments, wash, rinse and repeat. I chose to build my own box instead of using the `archlinux/archinux` Vagrant box because I wanted a different disk partition, to include a swap file. And I also wanted to understand Packer better. Starting with Packer and Ansible based on the build by `binary-manu`, this morphed eventually into a Vagrant box where I could then use Ansible tags to build desktop environments. 

The Packer build attempts to use Ansible for the initial build and configuration. There are many shell calls within the build, but it is still mostly within Ansible. Then Ansible is used again in Vagrant for box customizations. 

## Versions used

- VirtualBox 6.0.14
- Packer 1.4.4
- Vagrant 2.2.6
- Ansible 2.9
- Archlinux latest (2019-11-01)
    - Using mirrors.kernel.org/archlinux as the mirror site.

## Installed base system

- 1 CPU, 1 GB RAM
- EFI enable
- 3 partition system using GRUB efibootmgr. 
    - /dev/sda1 256 MiB esp 
    - /dev/sda2 4 GiB swap
    - /dev/sda3 ~23 GiB mounted as root drive (`/`)
        - Roughly what remains of the drive size specified.

The partion role does have parameters to create a home directory but was not used.

## Vagrant box

- 2 CPU
- 4 GB RAM
- VBoxSVGA 128 MB video ram, 3D acceleration
- CoreAudio

## Users

The Packer build uses Vagran's default `vagrant.pub` key from Hashicorp. This is changed on first build of a Vagrant box. Once in Vagrant, edit `ansible\roles\users\defaults\main.yaml.txt` with accounts and words as needed, then save as `main.yaml`. Also be sure to edit the `group_vars/all/00-default.yaml` and add in additional user to `global_admins`.

## Build

Run `build.sh` or cat the contents and edit as necessary. Results in a Vagrant box which is added by executing `vagrant box add --name[your box name] archlinux-[build date].box`. Then change to the Vagrant directory and edit the `Vagrantfile` as needed, then simply `vagrant up`. You can then choose to access the VM using `vagrant ssh`, or access the VM directly and use user credentials provided.

### Basic build flow

The ISO is download and booted, then a script is used as the boot command that simply creates the Vagrant user account, modifies sudoers, then starts the SSH service. After this step, Ansible is then able to be used as a provisioner, installed using the Vagrant account, calling the bootstrap.yaml playbook. This should export a Vagrant box build in the Packer directory.

Then switch to Vagrant and edit the Vagrantfile as needed, being sure to edit the Vagrant box name you added from the Packer build, on line 4. Using the same Ansible directory, the site.yaml playbook is called to perform the user customizations desired. 

## Summary

I realize this is not a "devops" build but it helped me learn Ansible. I expected to do this all using Puppet, which I still may do eventually. I felt Ansible was a little "lighter" for this effort. If this were a larger build with many more VMs, then I may look at Puppet as I like the agent on the VMs to maintain configuration. Trying to use Puppet in a masterless mode seemed a little overkill considering all the modules I would have needed to download and install. This was simply a desire for me to test out different destkop environments on Linux, and I thought Archlinux was the best base to start with, and Ansible was simple enough to modify the build. 