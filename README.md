# Kubernetes Demo Environment With Master/Slave Nodes
Work in progress - It's a playground environment with k8s master/slave nodes. You can simply create vms with vagrant and provision them by the ansible-playbook in the repository.

## Prerequisities
* Virtualbox
* Vagrant
* Ansible

## System Requirements:
* 6 GB Free memory space
* 8 GB Free disk space

## Setup
* Run `create-cluster` script under the `scripts` folder in order to create the virtual machines and a Kubernetes cluster on them.
```
$ ./scripts/create-cluster
```

The script would create 3 virtual machines with the following IP addresses:

* 192.168.20.10 - Master node
* 192.168.20.11	- Slave Node - 1
* 192.168.20.12	- Slave Node - 2


# How To Access Kubernetes

* Run `download-k8s-config` script under `scripts` folder.
```
$ ./scripts/download-k8s-config
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
* Check if all machine date/time configurations are identical
	* Run the following command to see the date configuration of all servers
	```
	$ ./scripts/display-servers-date
	```

	* You should see a result like the following:
	```
	Server: Master
	Thu Sep 10 20:14:36 +03 2020
	Server: Slave-1
	Thu Sep 10 20:14:36 +03 2020
	Server: Slave-2
	Thu Sep 10 17:14:37 UTC 2020
	```

	* if you see any time difference bigger than a few seconds, first, reboot the machines
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

	* Re-initialize the cluster by `reinitialize-cluster` script under scripts folder.  **IMPORTANT: This step will reset whole cluster. You may lose your data**
	```
	$ ./scripts/reinitialize-cluster
	```

	* Re-download the latest kubernetes configuration after re-initialized the kubernetes cluster.
	```
	$ ./scripts/download-k8s-config
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