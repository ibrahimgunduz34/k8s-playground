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

# How To Access Kubernetes
* Create `.kube` directory under your user home directory

```
mkdir -p ~/.kube
```

* Goto `vagrant` directory and copy the kubernetes configuration from the vm to the folder you created under your home directory.

```
$ vagrant ssh k8s_master <<EOF
sudo cp /etc/kubernetes/admin.conf /tmp/kube-config.conf &&\
sudo chown vagrant:vagrant /tmp/kube-config.conf
EOF

$ scp \
-o StrictHostKeyChecking=no \
-o UserKnownHostsFile=/dev/null \
-i .vagrant/machines/k8s_master/virtualbox/private_key \
vagrant@192.168.20.10:/tmp/kube-config.conf ~/.kube/config &&\
 vagrant ssh k8s_master -c "rm /tmp/kube-config.conf"
```

* Update/ensure the configuration file ownership
```
$ chown -R $USER:$USER ~/.kube
```

* Call `kubectl` command
```
$ kubectl get nodes
```

You should see a result like the following:
```
NAME        STATUS   ROLES    AGE     VERSION
k8smaster   Ready    master   3m19s   v1.19.0
k8sslave    Ready    <none>   2m45s   v1.19.0
```

## TODOs
* ~~Use generic group names instead of environment specific group names for the roles regarding multi-node setup.~~
* ~~Learn how to access the cluster from remote and update the document.~~
* ~~Make the kubernetes works with multi-slave and single master at the first phase~~
	* ~~Update the vagrant configuration to create a second vm in order to use it as slave kubernetes node~~
	* ~~Update ansible scripts to make the required changes on the vm OS' and install common components to the slave too~~
	* ~~Update firewall rules for the slave node by ansible~~
	* ~~Register slave kubernetes node to the master~~
* Put an example project into repo that uses external services db, cache, queue broker etc. with kubernetes deployment configuration
	* Deployment configuration should create multiple instance of the application container
	* The application should start with delay
	* The deployment configuration should take care of application instances' health-check
	* The configuration should allow load balancing between the application instances and provide an endpoint that external users can access the application
* Make the kubernetes works with multi-master and slave nodes.
* Put some automated tests to simulate and test downtime scenarios of master or slave nodes.


## Credits:
Some docs for kubernetes installation

* [https://www.tecmint.com/install-a-kubernetes-cluster-on-centos-8/](https://www.tecmint.com/install-a-kubernetes-cluster-on-centos-8/)

* [https://phoenixnap.com/kb/how-to-install-kubernetes-on-centos](https://phoenixnap.com/kb/how-to-install-kubernetes-on-centos)

* Solution for `tc command missing` issue <br />
[https://stackoverflow.com/questions/59653331/kubernetes-centos-8-tc-command-missing-impact](https://stackoverflow.com/questions/59653331/kubernetes-centos-8-tc-command-missing-impact)

* Solution for `misconfiguration: kubelet cgroup driver: "systemd" is different from docker cgroup driver: "cgroupfs"` issue <br />
[https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/troubleshooting-kubeadm/](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/troubleshooting-kubeadm/)

* Solution to print join token after initialized master node <br />
[https://stackoverflow.com/questions/40009831/cant-find-kubeadm-token-after-initializing-master](https://stackoverflow.com/questions/40009831/cant-find-kubeadm-token-after-initializing-master)

* Solution for `unable to update cni config: No networks found in /etc/cni/net.d` issue <br />
[https://stackoverflow.com/questions/43713509/kubernetes-v1-6-2-unable-to-update-cni-config-no-networks-found-in-etc-cni-net](https://stackoverflow.com/questions/43713509/kubernetes-v1-6-2-unable-to-update-cni-config-no-networks-found-in-etc-cni-net)