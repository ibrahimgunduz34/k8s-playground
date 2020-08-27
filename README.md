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

* Goto `ansible` folder, run the following command in order to install Kubernetes and all the required packages.
```shell
$ ansible-playbook -i inventory.yml provision-dev.yml
```

