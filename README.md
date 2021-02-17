# AWS-LAMP: Playbooks to provision a basic LAMP stack on VMWare VCenter

This playbook is meant to provision, install, and deploy a simple LAMP stack website for a rough example of how you can leverage Ansible to automate provision & installation on VMWare VCenter. It will create 2 instance in VCenter, install LAMP stack on them, and deploy a basic website. I hope that this repository can provide you insight on how to provision VM on VCenter environment and fiddle with it!

There are two playbooks you need to run:
- provision-vmware.yml
- install-stack.yml

And one optional playbook you can run:
- configure-web.yml (On Progress)

When you are finished and want to terminate all instances, you can run:
- terminate-vmware.yml

## Provisioning your Control Node & Ansible (Inside VCenter)
To use ansible automation script, you will need ansible on a separate machine or virtual machine, in this case I created a dedicated VM inside my VCenter. For this project, I am using ubuntu 20.04 as my Control Node. You can also use other linux machine for your control node. This playbook is working on:
- Ansible version >= 2.9
- Python version = 3.8.5

To Provision a new VM, you will first need to upload an ISO OS to VCenter Datastore. This upload will fail if you do not configure your certificate(Accept or install root CA certificate) & apply hosts name on your windows system. You can google it for more information on how to do it.
1. Login to your vSphere Client.
2. Go to Storage > Your Desired Datastore(Ex: DC1) > Files > Upload New Files.
3. Browse your ISO file (I downloaded it from )

To install Ansible in ubuntu 20.04 is pretty simple:

```
sudo apt-get update
sudo apt-get install ansible
```

Check the installation by issuing:

```
ansible --version
```

## Configuring VM Template & Network
One of the hardest thing to configure for me in VCenter is the network, so this will not contain full debugging nor explanation of each network element. Basically what we need to do is create a VM template, assign static IP address, and check network connection.

Provisioning ec2 instance requires you to first update the following variables in group_vars/all:

To provision all instances, run:

```
ansible-playbook provision-ec2.yml
```

## Updating the Hosts
As I'm still figuring out how to use the ec2 dynamic inventory, updating hosts file needs to be done manually. After the provisioning have completed, add each server public ipv4 address to ANSIBLE_HOST parameter on the 'hosts' file.

Updating the hosts requires you to update the following variable in group_vars/all:

- db1_hosts
- web1_hosts

## Install the Stack
This playbook will install and configure all the necessary packages on the Apache, MySQL, and PHP.

First you need to disable ssh key checking by running:

```
export ANSIBLE_HOST_KEY_CHECKING=False
```

Then run the playbook:

```
ansible-playbook -i hosts install-stack.yml
```

Once the playbook has completed successfully, you can reach the site by going to

```
http://<public_ip_of_web_server>
```

## Configure the Website (Optional)
There are simple and ready to deploy website in this project. Keep in mind that this configuration only works for Ubuntu OS. For another linux os, you will need to reconfigure MySQL bind-address configuration yourself. To deploy, run:

```
ansible-playbook -i hosts configure-web.yml
```

After the playbook finished, check you website at:

```
http://<public_ip_of_web_server>
```

## Terminating All Instances
To terminate Web & DB Instance, simply run:

```
ansible-playbook terminate-vmware.yml
```

This will destroy Web & DB VM you have just provision.