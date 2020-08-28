# Kubernetes Demo Environment With Master/Slave Nodes
Work in progress - It's a playground environment with k8s master/slave nodes. You can simply create vms with vagrant and provision them by the ansible-playbook in the repository.

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

## TODOs
* ~~Use generic group names instead of environment specific group names for the roles regarding multi-node setup.~~
* Make the kubernetes works with multi-slave and single master at the first phase
* Put an example project into repo that uses external services db, cache, queue broker etc. with kubernetes deployment configuration
	* Deployment configuration should create multiple instance of the application container
	* The application should start with delay
	* The deployment configuration should take care of application instances' health-check
	* The configuration should allow load balancing between the application instances and provide an endpoint that external users can access the application
* Make the kubernetes works with multi-master and slave nodes.
* Put some automated tests to simulate and test downtime scenarios of master or slave nodes.


## Credits:
* [https://www.tecmint.com/install-a-kubernetes-cluster-on-centos-8/](https://www.tecmint.com/install-a-kubernetes-cluster-on-centos-8/)
* [https://stackoverflow.com/questions/59653331/kubernetes-centos-8-tc-command-missing-impact](https://stackoverflow.com/questions/59653331/kubernetes-centos-8-tc-command-missing-impact)
* [https://phoenixnap.com/kb/how-to-install-kubernetes-on-centos](https://phoenixnap.com/kb/how-to-install-kubernetes-on-centos)