# Kubernetes Demo Environment With Master/Slave Nodes (Work In Progress)

## Prerequisities
* Virtualbox
* Vagrant
* Ansible

## Setup
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

## Credits:
* [https://www.tecmint.com/install-a-kubernetes-cluster-on-centos-8/](https://www.tecmint.com/install-a-kubernetes-cluster-on-centos-8/)
* [https://stackoverflow.com/questions/59653331/kubernetes-centos-8-tc-command-missing-impact](https://stackoverflow.com/questions/59653331/kubernetes-centos-8-tc-command-missing-impact)
* [https://phoenixnap.com/kb/how-to-install-kubernetes-on-centos](https://phoenixnap.com/kb/how-to-install-kubernetes-on-centos)