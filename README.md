## How to create a new cluster:

1. Create new VM on the openstack cluster [howto](https://stfc.atlassian.net/wiki/spaces/CLOUDKB/pages/211943497/Create+a+Virtual+Machine+on+Openstack+using+the+Openstack+Web+interface) - you can also do it from your machine if you pull all the dependancies
	* Use a standard Ubuntu image
	* Go for a smallish flavor - eg l3.nano 
	* Attach a new FIP to it, or add the machine to the "internal" network (only available on dev)
	* Use an already existing keypair - or generate a new one

2. After the new VM is created ssh to it and clone this repo
3. CD to the correct directory and follow [these](https://stfc.atlassian.net/wiki/spaces/CLOUDKB/pages/211878034/Cluster+API+Setup#Manual-Deployment) instructions: you don't need to change any values in the configs.

## How to update the kubernetes image
1. Make sure your kube config context is set to the management cluster that manages the cluster you are trying to update: for example if updating the a dev cluster- you will need to point to a dev-management, if updating a dev-management you will need to point to a dev-management
2. Go to the appropriate folder and change the values `kubernetesVersion` and `machineImage` in the `user-values.yaml` to the new image version. To find out all the new images that are available please check https://openstack.stfc.ac.uk/project/images
	*  For example if upgrading to version 1.32.4
	* Set `kubernetesVersion` to 1.32.4
	* Set `machineImage` to `capi-ubuntu-2204-kube-v1.32.4-2025-05-02`
	* **Don't jump major versions if not on the latest path**
3. More info can be found [here](https://stfc.atlassian.net/wiki/spaces/CLOUDKB/pages/285704256/Cluster+API+Upgrade#Kubernetes-Image-and-Version-Upgrades)
4. Find out the name of the cluster you are upgrading (to find out the name run `helm list -n clusters` and look for the name that is the closest to the one you are upgrading. So for example prod cluster can be called `prod-v4`)
5. Export the name by using `export CLUSTER_NAME=<name of the cluster>`
6. After the config is updated simple run `helm upgrade` command `helm upgrade $CLUSTER_NAME capi/openstack-cluster --install -f values.yaml -f clouds.yaml -f user-values.yaml -f flavors.yaml -n clusters
7. Wait around 30 minutes and the cluster should be updated