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
* 192.168.20.11	- Slave Node - 1
* 192.168.20.12	- Slave Node - 2

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
$ vagrant ssh k8smaster <<EOF
sudo cp /etc/kubernetes/admin.conf /tmp/kube-config.conf &&\
sudo chown vagrant:vagrant /tmp/kube-config.conf
EOF

$ scp \
-o StrictHostKeyChecking=no \
-o UserKnownHostsFile=/dev/null \
-i .vagrant/machines/k8smaster/virtualbox/private_key \
vagrant@192.168.20.10:/tmp/kube-config.conf ~/.kube/config &&\
 vagrant ssh k8smaster -c "rm /tmp/kube-config.conf"
```

* Update/ensure the configuration file ownership
```
$ chown -R $USER:$USER ~/.kube
```

* Check the nodes' statuses by the following command:
```
$ kubectl get nodes
```

You should see a result like the following:
```
NAME        STATUS     ROLES    AGE   VERSION
k8smaster   Ready      master   28m   v1.19.0
k8sslave1   Ready      <none>   27m   v1.19.0
k8sslave2   Ready      <none>   27m   v1.19.0
```
**NOTE:** Keep it mind that nodes may become available (Ready) in several minutes.


## TODOs
* ~~Use generic group names instead of environment specific group names for the roles regarding multi-node setup.~~
* ~~Learn how to access the cluster from remote and update the document.~~
* ~~Make the kubernetes works with multi-slave and single master at the first phase~~
	* ~~Update the vagrant configuration to create a second vm in order to use it as slave kubernetes node~~
	* ~~Update ansible scripts to make the required changes on the vm OS' and install common components to the slave too~~
	* ~~Update firewall rules for the slave node by ansible~~
	* ~~Register slave kubernetes node to the master~~
* ~~Find a way to make the slave nodes works after VMs slept and woke up~~
* Put an example project into repo that uses external services db, cache, queue broker etc. with kubernetes deployment configuration
	* Deployment configuration should create multiple instance of the application container
	* The application should start with delay
	* The deployment configuration should take care of application instances' health-check
	* The configuration should allow load balancing between the application instances and provide an endpoint that external users can access the application
* Make the kubernetes works with multi-master and slave nodes.
* Put some automated tests to simulate and test downtime scenarios of master or slave nodes.

## Troubleshooting:
### Problem:
If master works and slave nodes looks NotReady <br/>
### Solution:
* Check if all machine timezones are identical
	* Goto `vagrant` folder and run the following command to print all node's datetime configuration
	```
	$ vagrant ssh k8smaster -c date &&\
	vagrant ssh k8sslave1 -c date  &&\
	vagrant ssh k8sslave2 -c date
	```

	* if you see any time differents, first, reboot the machines
	```
	$ vagrant reload
	```

	* Check the status of the nodes by `kubectl get nodes` command
	```
	$ kubectl get nodes
	```

	if you still don't see the following result in a few minutes, continue by the next steps below.
	```
	NAME        STATUS   ROLES    AGE   VERSION
	k8smaster   Ready    master   13m   v1.19.0
	k8sslave1   Ready    <none>   12m   v1.19.0
	k8sslave2   Ready    <none>   12m   v1.19.0
	```

	* Re-initialize the cluster by `reinitialize_cluster.yml` ansible playblook. Goto `ansible` folder and run the following command:
	```
	$ ansible-playbook -i inventory.yml reinitialize_cluster.yml
	```

	* Goto `vagrant` folder and update the local kubernetes configuration file by the following command series in order to access the reinitialized cluster locally.

```
$ vagrant ssh k8smaster <<EOF
sudo cp /etc/kubernetes/admin.conf /tmp/kube-config.conf &&\
sudo chown vagrant:vagrant /tmp/kube-config.conf
EOF

$ scp \
-o StrictHostKeyChecking=no \
-o UserKnownHostsFile=/dev/null \
-i .vagrant/machines/k8smaster/virtualbox/private_key \
vagrant@192.168.20.10:/tmp/kube-config.conf ~/.kube/config &&\
 vagrant ssh k8smaster -c "rm /tmp/kube-config.conf"
```

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