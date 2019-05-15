## Run Jenkins Master on Kubernetes with a docker-for-desktop cluster

### Introduction
This is a lab to demonstrate running a Jenkins master on a Kubernetes cluster hosted on docker-for-desktop.

The Jenkins master is configured to use local persistent storage, which will only work with a single node cluster.

### Lab Environment
 * macOS Mojave 10.14.4
 * Docker CE 2.0.0.3
 * Kubernetes v1.10.11
 * Jenkins v2.176

A Kubernetes cluster called `docker-for-desktop` is created by enabling Kubernetes in Docker preferences.

### Kubernetes Dashboard
To enable the Kubernetes dashboard:
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/aio/deploy/recommended/kubernetes-dashboard.yaml
kubectl proxy &
```

Create a Service Account and Bearer Token
```
kubectl create -f admin-user.yaml
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
```
Kubernetes dashboard URL: http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/

### Deploy
```
kubectl create -f jenkins-master.yaml
```
The local storage path is `/tmp/jenkins_home/`. The persistent volume is 1GB in size.

All resources are created in a namespace called `jenkins`

### Retrieve Jenkins Initial Admin Password
Review the logs from the running Jenkins pod to retrive the Jenkins initial admin password
```
kubectl --namespace jenkins get pods
kubectl --namespace jenkins logs [pod_name]
```
Jenkins URL: http://localhost:30000