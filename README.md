# Setting a Load Balancer with Ansible

This task is about deploying a Load Balancer using Apache2 webserver with Ansible

## Introduction

To run this projects some considerations needs to be acknowledged first:
* This project needs an Ansible control server and N hosts servers, one of them to be configured as the Load Balancer
* The hosts machines needs to run Ubuntu as the configuration for the Load Balancer are to configure Apache2 (On RHEL distributions, its named httpd)
* Ansible needs to be installed only in the control node but Python is needed on all the hosts.
* This project works when all the hosts are configured with the same user and password.

### Ansible playbooks

This project is intended to run only two Ansible playbooks and an optional one
* The 0-add-sudoers.yml file is optional. It add the managed node user to the sudoers file. This is not recommended but to avoid run the subsequents ansible commands on the node providing a become password is needed.
* The 1-setup-app.yml file which contains the tasks to setup a simple sample application to run on Apache2 webserver.
* The 2-setup-lb.yml file which contains the tasks to setup the load balancer between the managed nodes

### Configuration files

This project have only two configuration files which are hosts and ansible.cfg
* The hosts file sets the inventory for this project
* The ansible.cfg sets the configurations, applicable to this project only

### Running the playbooks

* The optional Ansible playbook to add sudoers will run with:
```
ansible-playbook 0-add-sudoers.yml -K
```
This will ask for the become password (the user password) to run visudo as sudo in order to add the user to the sudoers group. 

* The first Ansible playbook needed to run is the setup app playbook (if the first playbook was run and succesful no -K flag is needed):
```
ansible-playbook 1-setup-app.yml
```
* The second is the the setup load balancer file.
```
ansible-playbook main.yml
```

## Accessing the Load Balancer

The sample webpage should now be running on the node. Accessing through port 80 will do on any browser:
```
<MANAGED_NODE_PUBLIC_IP>
```
Replace the placeholder with the node's IP address (no port is needed as the 80 is the default), and application should display.
The configuration sets and info.php page to check if the Load Balancer is evebnly distributing traffic between hosts. In order to access this page this will do:
```
<MANAGED_NODE_PUBLIC_IP>/info.php
```
Refreshing the page several times should display the host's nameserver providing a simple way to identify if the loads are being distributed.
