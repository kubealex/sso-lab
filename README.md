# Red Hat SSO Demo env installer

This is a simple demo environment provisioner for SSO to install.
It creates:

- libvirt-network with DHCP/DNS
- libvirt-pool for your VM
- SSO server 

## Host setup

### Download Terraform

VM setup is based on Terraform, it instantiates one virtual machine, *sso-server* kickstarting the setup.

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

Review settings in **provision-lab.yml** file, containing some basic inputs:

    network_cidr = ["192.168.212.0/24"]

The terraform plan also creates an isolated virtual network, with DHCP and DNS for the specified domain.

Run the playbook to provision the VMs:

    ansible-playbook -i inventory provision-lab.yml

The setup will take a bit as it is a full install with a kickstarter. 

## SSO setup

With your lab up and running, you can proceed installing SSO using the provided **sso-setup.yml** playbook.

    ansible-playbook -i inventory sso-setup.yml

It will ask for:

- RHNID (Username)
- Password
- PoolID of the subscription

## Test your configuration

If the setup was good, you will be able to access your SSO server on [](https://sso-server.ssodemo.labs:8443)