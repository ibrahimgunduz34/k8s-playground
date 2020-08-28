# Kubernetes Demo Environment With Master/Slave Nodes

## Prerequisities
* Virtualbox
* Vagrant
* Ansible

# Setup
* Goto `vagrant` folder and start the machines by the following command
```shell
$ vagrant up
```
Vagrant would create two machines as master and slave with the following IP addresses
* 192.168.20.10 - Master node
* 192.168.20.21	- Slave Node - 1 (TODO)

* Goto `ansible` folder, run the following command in order to install Kubernetes and all the required packages.
```shell
$ ansible-playbook -i inventory.yml provision-dev.yml
```

