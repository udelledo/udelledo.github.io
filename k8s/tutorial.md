# Tutorial

## Start 

[Running a free Kubernetes cluster on Oracle Cloud](https://arnoldgalovics.com/free-kubernetes-oracle-cloud/) summarizes the cost differences between cloud provider, and shows that OCI provides solution that allows to have everything **free**

**Warning**
The free solution is based on ARM architecture and might a tad more complex to create the image

## Hands on

The [second part](https://arnoldgalovics.com/oracle-cloud-kubernetes-terraform/) of the series stars with the hands-on part.

#### Prerequisite

- [X] OCI account
- [x] A compartment you want the resources to provision in – it can be the root compartment if that’s okay with you **k8s**
- [X]  A user that have permissions to access the necessary resources – can be your root user if you want to
- [X]  Optionally an SSH key you want to use to provision your Kubernetes worker nodes and wanna make sure you have some way to access them if needed
- [X]  [OCI CLI installed](https://hackernoon.com/installation-of-oci-cli-on-wsl-windows-subsystem-for-linux-in-windows-10-or-above)
- [X]  Terraform CLI installed
- [ ]  [kubectl installed](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)


