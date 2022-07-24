# Satellite Demo env installer

This is a simple demo environment provisioner for SSO to install.
It creates:

- libvirt-network with DHCP/DNS
- libvirt-pool for your VM
- SSO server 

## Host setup

### Download Terraform

VM setup is based on Terraform, it instantiates three virtual machine, *satellite*, *el7-server* and *el8-server*, kickstarting the setup.

First you need to download and install Terraform:

    sudo yum install -y yum-utils
    sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
    sudo yum -y install terraform

### Install Ansible

You need to follow the instructions in [Ansible Website](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-the-ansible-community-package) to proceed and install Ansible on your machine.

## Lab provisioning

The provisioner consists of two playbooks, that configure the underlying components (VM, network) and prepares the guests to install RH-SSO.

The first playbook is **provision-lab.yml** which takes care of creating KVM resources. It only has a single variable: 

| Variable | Value |
|--|--|
| **network_cidr** | Defaults to 192.168.212.0/24 |

The package comes with an inventory:

    localhost ansible_connection=local
    [sso]
    sso.ssodemo.labs ansible_user=sysadmin

The playbook can either download RHEL 8.6 image, or work with pre-downloaded images. If you choose not to download it, the only requirement is providing the image in the playbook directory with the name **rhel8.iso**.

To download the images via the playbook, you will be prompted to enter your [Offline Token](https://access.redhat.com/management/api) to download resources.

**IMPORTANT** If you don't want to download images (it's around 20GB), just leave the variable blank.

Since some modules rely on additional collections you will need to install them via:

    ansible-galaxy install -r requirements.yml

Once you set the *network_cidr* variable to the desired value, you can run the playbook:

    ansible-playbook -i inventory provision-lab.yml

It takes around 20-25 minutes to be up and running. If you experience last step of the playbook being hanging after the machines are completely installed, **relaunch** the playbook as sometimes the ping module gets stuck.
VM setup is based on Terraform, it instantiates a RHEL8 VM, kickstarting the setup.

First you need to download and install Terraform:

    sudo yum install -y yum-utils
    sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
    sudo yum -y install terraform

The playbook can either download RHEL 8.6 and RHEL 9 images, or work with pre-downloaded images. The only requirement is that the images need to be placed in the playbook directory with the name **rhel8.iso** and **rhel9.iso**

To download the images via the playbook, you will be prompted to enter your [Offline Token](https://access.redhat.com/management/api) to download resources.


**IMPORTANT** If you don't want to download images (it's around 20GB), just leave the variable blank.

Review settings in **provision-lab.yml** file, containing some basic inputs:

    network_cidr = ["192.168.212.0/24"]

The terraform plan also creates an isolated virtual network, with DHCP and DNS for the specified domain.

Run the playbook to provision the VMs:

    ansible-playbook -i inventory provision-lab.yml

The setup will take a bit as it is a full install with a kickstarter. 

## SSO setup

With your lab up and running, you can proceed installing Satellite using the provided **satellite-setup.yml** playbook.

    ansible-playbook -i inventory sso-setup.yml

It will ask for:

- RHNID (Username)
- Password
- PoolID of the subscription

## Test your configuration

If the setup was good, you will be able to access your IdM server on [](https://sso-server.satellitedemo.labs:8443)