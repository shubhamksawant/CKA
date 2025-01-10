OS Upgrades
<details>
<summary>
Senario -
Let us explore the environment first. How many nodes do you see in the cluster?
Including the controlplane and worker nodes.

</summary>

```bash

controlplane ~ ➜  k get nodes 
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   16m   v1.31.0
node01         Ready    <none>          15m   v1.31.0

  ```
</details>



<details>
<summary>
Senario -
How many applications do you see hosted on the cluster?

Check the number of deployments in the default namespace.


</summary>

```bash

controlplane ~ ➜  k get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/blue-56dd475db5-468s6   1/1     Running   0          2m36s
pod/blue-56dd475db5-7nlj7   1/1     Running   0          2m36s
pod/blue-56dd475db5-p822f   1/1     Running   0          2m36s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   172.20.0.1   <none>        443/TCP   18m

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/blue   3/3     3            3           2m36s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/blue-56dd475db5   3         3         3       2m36s


  ```
</details>

<details>
<summary>
Senario -
Which nodes are the applications hosted on?

</summary>

```bash


controlplane ~ ➜  k get all -o wide
NAME                        READY   STATUS    RESTARTS   AGE     IP           NODE           NOMINATED NODE   READINESS GATES
pod/blue-56dd475db5-468s6   1/1     Running   0          3m28s   172.17.1.3   node01         <none>           <none>
pod/blue-56dd475db5-7nlj7   1/1     Running   0          3m28s   172.17.1.2   node01         <none>           <none>
pod/blue-56dd475db5-p822f   1/1     Running   0          3m28s   172.17.0.4   controlplane   <none>           <none>

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE   SELECTOR
service/kubernetes   ClusterIP   172.20.0.1   <none>        443/TCP   19m   <none>

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE     CONTAINERS   IMAGES         SELECTOR
deployment.apps/blue   3/3     3            3           3m28s   nginx        nginx:alpine   app=blue

NAME                              DESIRED   CURRENT   READY   AGE     CONTAINERS   IMAGES         SELECTOR
replicaset.apps/blue-56dd475db5   3         3         3       3m28s   nginx        nginx:alpine   app=blue,pod-template-hash=56dd475db5
  ```
</details>

<details>
<summary>
Senario -

We need to take node01 out for maintenance. Empty the node of all applications and mark it unschedulable.

</summary>

```bash
Examples:
  # Drain node "foo", even if there are pods not managed by a replication controller, replica set, job, daemon set, or
stateful set on it
  kubectl drain foo --force
  
  # As above, but abort if there are pods not managed by a replication controller, replica set, job, daemon set, or
stateful set, and use a grace period of 15 minutes
  kubectl drain foo --grace-period=900


controlplane ~ ✖ k drain node01  --ignore-daemonsets 
node/node01 already cordoned
Warning: ignoring DaemonSet-managed Pods: kube-flannel/kube-flannel-ds-dpwvt, kube-system/kube-proxy-kqc4w
evicting pod default/blue-56dd475db5-7nlj7
evicting pod default/blue-56dd475db5-468s6
pod/blue-56dd475db5-468s6 evicted
pod/blue-56dd475db5-7nlj7 evicted
node/node01 drained

  ```
</details>

<details>
<summary>
Senario -
The maintenance tasks have been completed. Configure the node node01 to be schedulable again.

```bash

controlplane ~ ➜  k get nodes -o wide
NAME           STATUS                     ROLES           AGE   VERSION   INTERNAL-IP      EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION    CONTAINER-RUNTIME
controlplane   Ready                      control-plane   22m   v1.31.0   192.168.75.183   <none>        Ubuntu 22.04.4 LTS   5.15.0-1072-gcp   containerd://1.6.26
node01         Ready,SchedulingDisabled   <none>          22m   v1.31.0   192.168.139.41   <none>        Ubuntu 22.04.4 LTS   5.15.0-1071-gcp   containerd://1.6.26
  ```

</summary>

```bash

controlplane ~ ➜  k uncordon --help
Mark node as schedulable.

Examples:
  # Mark node "foo" as schedulable
  kubectl uncordon foo


controlplane ~ ➜  k uncordon node01 
node/node01 uncordoned

controlplane ~ ➜  k get nodes -o wide
NAME           STATUS   ROLES           AGE   VERSION   INTERNAL-IP      EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION    CONTAINER-RUNTIME
controlplane   Ready    control-plane   26m   v1.31.0   192.168.75.183   <none>        Ubuntu 22.04.4 LTS   5.15.0-1072-gcp   containerd://1.6.26
node01         Ready    <none>          25m   v1.31.0   192.168.139.41   <none>        Ubuntu 22.04.4 LTS   5.15.0-1071-gcp   containerd://1.6.26

controlplane ~ ➜  

  ```
</details>

<details>
<summary>
Senario -

How many pods are scheduled on node01 now in the default namespace?


</summary>

```bash

controlplane ~ ➜    kubectl get pods -o wide
NAME                    READY   STATUS    RESTARTS   AGE     IP           NODE           NOMINATED NODE   READINESS GATES
blue-56dd475db5-22jz7   1/1     Running   0          7m29s   172.17.0.5   controlplane   <none>           <none>
blue-56dd475db5-5fln8   1/1     Running   0          7m29s   172.17.0.6   controlplane   <none>           <none>
blue-56dd475db5-p822f   1/1     Running   0          12m     172.17.0.4   controlplane   <none>           <none>


  ```
</details>

<details>
<summary>
Senario -
After shcheduling enbled Why are there no pods on node01?

</summary>

```bash
Running the uncordon command on a node will not automatically schedule pods on the node. When new pods are created, they will be placed on node01.

  ```
</details>

<details>
<summary>
Senario -

Why are the pods placed on the controlplane node?
Check the controlplane node details.

</summary>

```bash

controlplane ~ ✖ kubectl describe node controlplane
Name:               controlplane
Roles:              control-plane
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=controlplane
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/control-plane=
                    node.kubernetes.io/exclude-from-external-load-balancers=
Annotations:        flannel.alpha.coreos.com/backend-data: {"VNI":1,"VtepMAC":"8a:f5:c6:e7:93:cf"}
                    flannel.alpha.coreos.com/backend-type: vxlan
                    flannel.alpha.coreos.com/kube-subnet-manager: true
                    flannel.alpha.coreos.com/public-ip: 192.168.75.183
                    kubeadm.alpha.kubernetes.io/cri-socket: unix:///var/run/containerd/containerd.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Wed, 08 Jan 2025 11:45:01 +0000
Taints:             <none>
Unschedulable:      false
Lease:
  HolderIdentity:  controlplane
  AcquireTime:     <unset>
  RenewTime:       Wed, 08 Jan 2025 12:16:03 +0000
Conditions:
  Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----                 ------  -----------------                 ------------------                ------                       -------
  NetworkUnavailable   False   Wed, 08 Jan 2025 11:45:13 +0000   Wed, 08 Jan 2025 11:45:13 +0000   FlannelIsUp                  Flannel is running on this node
  MemoryPressure       False   Wed, 08 Jan 2025 12:15:50 +0000   Wed, 08 Jan 2025 11:45:01 +0000   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure         False   Wed, 08 Jan 2025 12:15:50 +0000   Wed, 08 Jan 2025 11:45:01 +0000   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure          False   Wed, 08 Jan 2025 12:15:50 +0000   Wed, 08 Jan 2025 11:45:01 +0000   KubeletHasSufficientPID      kubelet has sufficient PID available
  Ready                True    Wed, 08 Jan 2025 12:15:50 +0000   Wed, 08 Jan 2025 11:45:11 +0000   KubeletReady                 kubelet is posting ready status
Addresses:
  InternalIP:  192.168.75.183
  Hostname:    controlplane
Capacity:
  cpu:                16
  ephemeral-storage:  772706776Ki
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             65838284Ki
  pods:               110
Allocatable:
  cpu:                16
  ephemeral-storage:  712126563583
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             65735884Ki
  pods:               110
System Info:
  Machine ID:                 132e3d2451f947fe9214456160254717
  System UUID:                560fb121-3970-e01f-d582-5979e8198045
  Boot ID:                    debfa6b1-ebd1-4e4d-9490-a86525f1db1d
  Kernel Version:             5.15.0-1072-gcp
  OS Image:                   Ubuntu 22.04.4 LTS
  Operating System:           linux
  Architecture:               amd64
  Container Runtime Version:  containerd://1.6.26
  Kubelet Version:            v1.31.0
  Kube-Proxy Version:         
PodCIDR:                      172.17.0.0/24
PodCIDRs:                     172.17.0.0/24
Non-terminated Pods:          (11 in total)
  Namespace                   Name                                    CPU Requests  CPU Limits  Memory Requests  Memory Limits  Age
  ---------                   ----                                    ------------  ----------  ---------------  -------------  ---
  default                     blue-56dd475db5-22jz7                   0 (0%)        0 (0%)      0 (0%)           0 (0%)         9m48s
  default                     blue-56dd475db5-5fln8                   0 (0%)        0 (0%)      0 (0%)           0 (0%)         9m48s
  default                     blue-56dd475db5-p822f                   0 (0%)        0 (0%)      0 (0%)           0 (0%)         14m
  kube-flannel                kube-flannel-ds-6rvg4                   100m (0%)     0 (0%)      50Mi (0%)        0 (0%)         30m
  kube-system                 coredns-77d6fd4654-phbhz                100m (0%)     0 (0%)      70Mi (0%)        170Mi (0%)     30m
  kube-system                 coredns-77d6fd4654-w8q4s                100m (0%)     0 (0%)      70Mi (0%)        170Mi (0%)     30m
  kube-system                 etcd-controlplane                       100m (0%)     0 (0%)      100Mi (0%)       0 (0%)         31m
  kube-system                 kube-apiserver-controlplane             250m (1%)     0 (0%)      0 (0%)           0 (0%)         31m
  kube-system                 kube-controller-manager-controlplane    200m (1%)     0 (0%)      0 (0%)           0 (0%)         31m
  kube-system                 kube-proxy-64wjw                        0 (0%)        0 (0%)      0 (0%)           0 (0%)         30m
  kube-system                 kube-scheduler-controlplane             100m (0%)     0 (0%)      0 (0%)           0 (0%)         31m
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource           Requests    Limits
  --------           --------    ------
  cpu                950m (5%)   0 (0%)
  memory             290Mi (0%)  340Mi (0%)
  ephemeral-storage  0 (0%)      0 (0%)
  hugepages-1Gi      0 (0%)      0 (0%)
  hugepages-2Mi      0 (0%)      0 (0%)
Events:
  Type     Reason                    Age                From             Message
  ----     ------                    ----               ----             -------
  Normal   Starting                  30m                kube-proxy       
  Warning  CgroupV1                  31m                kubelet          Cgroup v1 support is in maintenance mode, please migrate to Cgroup v2.
  Warning  CheckLimitsForResolvConf  31m                kubelet          open /run/systemd/resolve/resolv.conf: no such file or directory
  Normal   NodeAllocatableEnforced   31m                kubelet          Updated Node Allocatable limit across pods
  Normal   NodeHasSufficientMemory   31m (x7 over 31m)  kubelet          Node controlplane status is now: NodeHasSufficientMemory
  Normal   NodeHasNoDiskPressure     31m (x7 over 31m)  kubelet          Node controlplane status is now: NodeHasNoDiskPressure
  Normal   NodeHasSufficientPID      31m (x6 over 31m)  kubelet          Node controlplane status is now: NodeHasSufficientPID
  Warning  CgroupV1                  31m                kubelet          Cgroup v1 support is in maintenance mode, please migrate to Cgroup v2.
  Warning  InvalidDiskCapacity       31m                kubelet          invalid capacity 0 on image filesystem
  Normal   NodeAllocatableEnforced   31m                kubelet          Updated Node Allocatable limit across pods
  Normal   NodeHasSufficientMemory   31m                kubelet          Node controlplane status is now: NodeHasSufficientMemory
  Normal   NodeHasNoDiskPressure     31m                kubelet          Node controlplane status is now: NodeHasNoDiskPressure
  Normal   NodeHasSufficientPID      31m                kubelet          Node controlplane status is now: NodeHasSufficientPID
  Normal   Starting                  31m                kubelet          Starting kubelet.
  Normal   RegisteredNode            30m                node-controller  Node controlplane event: Registered Node controlplane in Controller
  Normal   NodeReady                 30m                kubelet          Node controlplane status is now: NodeReady

there is no taint on controlplane

  ```
</details>

<details>
<summary>
Senario -

We need to take node01 outagain  for maintenance. Empty the node of all applications and mark it unschedulable.
```bash

controlplane ~ ✖ k get po -o wide
NAME                    READY   STATUS    RESTARTS   AGE     IP           NODE           NOMINATED NODE   READINESS GATES
blue-56dd475db5-22jz7   1/1     Running   0          14m     172.17.0.5   controlplane   <none>           <none>
blue-56dd475db5-5fln8   1/1     Running   0          14m     172.17.0.6   controlplane   <none>           <none>
blue-56dd475db5-p822f   1/1     Running   0          19m     172.17.0.4   controlplane   <none>           <none>
hr-app                  1/1     Running   0          2m46s   172.17.1.4   node01         <none>           <none>

controlplane ~ ➜  k get all -o wide
NAME                        READY   STATUS    RESTARTS   AGE    IP           NODE           NOMINATED NODE   READINESS GATES
pod/blue-56dd475db5-22jz7   1/1     Running   0          14m    172.17.0.5   controlplane   <none>           <none>
pod/blue-56dd475db5-5fln8   1/1     Running   0          14m    172.17.0.6   controlplane   <none>           <none>
pod/blue-56dd475db5-p822f   1/1     Running   0          19m    172.17.0.4   controlplane   <none>           <none>
pod/hr-app                  1/1     Running   0          3m7s   172.17.1.4   node01         <none>           <none>

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE   SELECTOR
service/kubernetes   ClusterIP   172.20.0.1   <none>        443/TCP   35m   <none>

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES         SELECTOR
deployment.apps/blue   3/3     3            3           19m   nginx        nginx:alpine   app=blue

NAME                              DESIRED   CURRENT   READY   AGE   CONTAINERS   IMAGES         SELECTOR
replicaset.apps/blue-56dd475db5   3         3         3       19m   nginx        nginx:alpine   app=blue,pod-template-hash=56dd475db5

  ```

</summary>

```bash

controlplane ~ ✖ k drain node01 --ignore-daemonsets 
node/node01 cordoned
error: unable to drain node "node01" due to error: cannot delete cannot delete Pods that declare no controller (use --force to override): default/hr-app, continuing command...
There are pending nodes to be drained:
 node01
cannot delete cannot delete Pods that declare no controller (use --force to override): default/hr-app


Run: kubectl get pods -o wide and you will see that there is a single pod scheduled on node01 which is not part of a replicaset
The drain command will not work in this case. To forcefully drain the node we now have to use the --force flag.


controlplane ~ ✖ k drain node01 --force
node/node01 already cordoned
error: unable to drain node "node01" due to error: cannot delete DaemonSet-managed Pods (use --ignore-daemonsets to ignore): kube-flannel/kube-flannel-ds-dpwvt, kube-system/kube-proxy-kqc4w, continuing command...
There are pending nodes to be drained:
 node01
cannot delete DaemonSet-managed Pods (use --ignore-daemonsets to ignore): kube-flannel/kube-flannel-ds-dpwvt, kube-system/kube-proxy-kqc4w

controlplane ~ ✖ k drain node01 --force --ignore-daemonsets 
node/node01 already cordoned
Warning: ignoring DaemonSet-managed Pods: kube-flannel/kube-flannel-ds-dpwvt, kube-system/kube-proxy-kqc4w; deleting Pods that declare no controller: default/hr-app
evicting pod default/hr-app


  ```
</details>

<details>
<summary>
Senario -
What would happen to hr-app if node01 is drained forcefully?
Try it and see for yourself.

</summary>
will be deleted forever
</details>




<details>
<summary>
Senario -
hr-app is a critical app and we do not want it to be removed and we do not want to schedule any more pods on node01.
Mark node01 as unschedulable so that no new pods are scheduled on this node.
Make sure that hr-app is not affected.


</summary>

```bash


controlplane ~ ➜  k get po -o wide
NAME                      READY   STATUS    RESTARTS   AGE   IP           NODE           NOMINATED NODE   READINESS GATES
blue-56dd475db5-22jz7     1/1     Running   0          22m   172.17.0.5   controlplane   <none>           <none>
blue-56dd475db5-5fln8     1/1     Running   0          22m   172.17.0.6   controlplane   <none>           <none>
blue-56dd475db5-p822f     1/1     Running   0          27m   172.17.0.4   controlplane   <none>           <none>
hr-app-79dcd6fd8f-qwqcn   1/1     Running   0          66s   172.17.1.5   node01         <none>           <none>


Examples:
  # Mark node "foo" as unschedulable
  kubectl cordon foo


controlplane ~ ➜  k get nodes -o wide 
NAME           STATUS   ROLES           AGE   VERSION   INTERNAL-IP      EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION    CONTAINER-RUNTIME
controlplane   Ready    control-plane   46m   v1.31.0   192.168.75.183   <none>        Ubuntu 22.04.4 LTS   5.15.0-1072-gcp   containerd://1.6.26
node01         Ready    <none>          45m   v1.31.0   192.168.139.41   <none>        Ubuntu 22.04.4 LTS   5.15.0-1071-gcp   containerd://1.6.26

controlplane ~ ➜  k cordon node01 
node/node01 cordoned

controlplane ~ ➜  k get nodes -o wide 
NAME           STATUS                     ROLES           AGE   VERSION   INTERNAL-IP      EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION    CONTAINER-RUNTIME
controlplane   Ready                      control-plane   46m   v1.31.0   192.168.75.183   <none>        Ubuntu 22.04.4 LTS   5.15.0-1072-gcp   containerd://1.6.26
node01         Ready,SchedulingDisabled   <none>          46m   v1.31.0   192.168.139.41   <none>        Ubuntu 22.04.4 LTS   5.15.0-1071-gcp   containerd://1.6.26

controlplane ~ ➜  k get all -o wide 
NAME                          READY   STATUS    RESTARTS   AGE     IP           NODE           NOMINATED NODE   READINESS GATES
pod/blue-56dd475db5-22jz7     1/1     Running   0          26m     172.17.0.5   controlplane   <none>           <none>
pod/blue-56dd475db5-5fln8     1/1     Running   0          26m     172.17.0.6   controlplane   <none>           <none>
pod/blue-56dd475db5-p822f     1/1     Running   0          31m     172.17.0.4   controlplane   <none>           <none>
pod/hr-app-79dcd6fd8f-qwqcn   1/1     Running   0          5m20s   172.17.1.5   node01         <none>           <none>



  ```
</details>

Cluster Upgrade Process

<details>
<summary>
Senario -

This lab tests your skills on upgrading a kubernetes cluster. We have a production cluster with applications running on it. Let us explore the setup first.

What is the current version of the cluster?

</summary>

```bash

controlplane ~ ✖ k get nodes -o wide 
NAME           STATUS   ROLES           AGE     VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION   CONTAINER-RUNTIME
controlplane   Ready    control-plane   4m26s   v1.30.0   192.11.149.3   <none>        Ubuntu 22.04.5 LTS   5.4.0-1106-gcp   containerd://1.6.26
node01         Ready    <none>          3m40s   v1.30.0   192.11.149.6   <none>        Ubuntu 22.04.5 LTS   5.4.0-1106-gcp   containerd://1.6.26

  ```
</details>


<details>
<summary>
Senario -

How many nodes are part of this cluster?

Including controlplane and worker nodes

</summary>

```bash
controlplane ~ ➜  k get nodes 
NAME           STATUS   ROLES           AGE     VERSION
controlplane   Ready    control-plane   5m40s   v1.30.0
node01         Ready    <none>          4m54s   v1.30.0

controlplane ~ ➜  k get nodes -o wide 
NAME           STATUS   ROLES           AGE     VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION   CONTAINER-RUNTIME
controlplane   Ready    control-plane   6m46s   v1.30.0   192.11.149.3   <none>        Ubuntu 22.04.5 LTS   5.4.0-1106-gcp   containerd://1.6.26
node01         Ready    <none>          6m      v1.30.0   192.11.149.6   <none>        Ubuntu 22.04.5 LTS   5.4.0-1106-gcp   containerd://1.6.26

controlplane ~ ➜  k describe nodes | grep Taint
Taints:             <none>
Taints:             <none>

both

  ```
</details>


<details>
<summary>
Senario -

How many applications are hosted on the cluster?

Count the number of deployments in the default namespace.

</summary>

```bash
controlplane ~ ➜  k get all
NAME                       READY   STATUS    RESTARTS   AGE
pod/blue-fffb6db8d-2mr5g   1/1     Running   0          6m56s
pod/blue-fffb6db8d-djzk8   1/1     Running   0          6m56s
pod/blue-fffb6db8d-dl889   1/1     Running   0          6m56s
pod/blue-fffb6db8d-t7jvg   1/1     Running   0          6m56s
pod/blue-fffb6db8d-w8t5k   1/1     Running   0          6m56s

NAME                  TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/kubernetes    ClusterIP   10.96.0.1       <none>        443/TCP        9m14s
service/red-service   NodePort    10.100.148.62   <none>        80:30080/TCP   6m56s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/blue   5/5     5            5           6m56s

NAME                             DESIRED   CURRENT   READY   AGE
replicaset.apps/blue-fffb6db8d   5    

  ```
</details>


<details>
<summary>
Senario -
What nodes are the pods hosted on?

</summary>

```bash


controlplane ~ ➜  k get all -o wide
NAME                       READY   STATUS    RESTARTS   AGE     IP           NODE           NOMINATED NODE   READINESS GATES
pod/blue-fffb6db8d-2mr5g   1/1     Running   0          7m43s   10.244.0.4   controlplane   <none>           <none>
pod/blue-fffb6db8d-djzk8   1/1     Running   0          7m43s   10.244.0.5   controlplane   <none>           <none>
pod/blue-fffb6db8d-dl889   1/1     Running   0          7m43s   10.244.1.4   node01         <none>           <none>
pod/blue-fffb6db8d-t7jvg   1/1     Running   0          7m43s   10.244.1.2   node01         <none>           <none>
pod/blue-fffb6db8d-w8t5k   1/1     Running   0          7m43s   10.244.1.3   node01         <none>           <none>


  ```
</details>


<details>
<summary>
Senario -

You are tasked to upgrade the cluster. Users accessing the applications must not be impacted, and you cannot provision new VMs. What strategy would you use to upgrade the cluster?


</summary>

```bash

In order to ensure minimum downtime, upgrade the cluster one node at a time, while moving the workloads to another node.
In the upcoming tasks you will get to practice how to do that.

  ```
</details>


<details>
<summary>
Senario -

What is the latest version available for an upgrade with the current version of the kubeadm tool installed?

Use the kubeadm tool
```bash
 kubeadm 


    ┌──────────────────────────────────────────────────────────┐
    │ KUBEADM                                                  │
    │ Easily bootstrap a secure Kubernetes cluster             │
    │                                                          │
    │ Please give us feedback at:                              │
    │ https://github.com/kubernetes/kubeadm/issues             │
    └──────────────────────────────────────────────────────────┘

Example usage:

    Create a two-machine cluster with one control-plane node
    (which controls the cluster), and one worker node
    (where your workloads, like Pods and Deployments run).

    ┌──────────────────────────────────────────────────────────┐
    │ On the first machine:                                    │
    ├──────────────────────────────────────────────────────────┤
    │ control-plane# kubeadm init                              │
    └──────────────────────────────────────────────────────────┘

    ┌──────────────────────────────────────────────────────────┐
    │ On the second machine:                                   │
    ├──────────────────────────────────────────────────────────┤
    │ worker# kubeadm join <arguments-returned-from-init>      │
    └──────────────────────────────────────────────────────────┘

    You can then repeat the second step on as many other machines as you like.

Usage:
  kubeadm [command]

Available Commands:
  certs       Commands related to handling kubernetes certificates
  completion  Output shell completion code for the specified shell (bash or zsh)
  config      Manage configuration for a kubeadm cluster persisted in a ConfigMap in the cluster
  help        Help about any command
  init        Run this command in order to set up the Kubernetes control plane
  join        Run this on any machine you wish to join an existing cluster
  kubeconfig  Kubeconfig file utilities
  reset       Performs a best effort revert of changes made to this host by 'kubeadm init' or 'kubeadm join'
  token       Manage bootstrap tokens
  upgrade     Upgrade your cluster smoothly to a newer version with this command
  version     Print the version of kubeadm

Flags:
      --add-dir-header   If true, adds the file directory to the header of the log messages
  -h, --help             help for kubeadm
      --rootfs string    [EXPERIMENTAL] The path to the 'real' host root filesystem.
      --skip-headers     If true, avoid header prefixes in the log messages
  -v, --v Level          number for the log level verbosity

Additional help topics:
  kubeadm alpha      Kubeadm experimental sub-commands

  ```

</summary>

```bash


controlplane ~ ✖ kubeadm version
kubeadm version: &version.Info{Major:"1", Minor:"30", GitVersion:"v1.30.0", GitCommit:"7c48c2bd72b9bf5c44d21d7338cc7bea77d0ad2a", GitTreeState:"clean", BuildDate:"2024-04-17T17:34:08Z", GoVersion:"go1.22.2", Compiler:"gc", Platform:"linux/amd64"}


controlplane ~ ➜  kubeadm upgrade plan
[upgrade/config] Making sure the configuration is correct:
[preflight] Running pre-flight checks.
[upgrade/config] Reading configuration from the cluster...
[upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
[upgrade] Running cluster health checks
[upgrade] Fetching available versions to upgrade to
[upgrade/versions] Cluster version: 1.30.0
[upgrade/versions] kubeadm version: v1.30.0
I0108 13:15:27.356167   14100 version.go:256] remote version is much newer: v1.32.0; falling back to: stable-1.30
[upgrade/versions] Target version: v1.30.8
[upgrade/versions] Latest version in the v1.30 series: v1.30.8

Components that must be upgraded manually after you have upgraded the control plane with 'kubeadm upgrade apply':
COMPONENT   NODE           CURRENT   TARGET
kubelet     controlplane   v1.30.0   v1.30.8
kubelet     node01         v1.30.0   v1.30.8

Upgrade to the latest version in the v1.30 series:

COMPONENT                 NODE           CURRENT    TARGET
kube-apiserver            controlplane   v1.30.0    v1.30.8
kube-controller-manager   controlplane   v1.30.0    v1.30.8
kube-scheduler            controlplane   v1.30.0    v1.30.8
kube-proxy                               1.30.0     v1.30.8
CoreDNS                                  v1.10.1    v1.11.1
etcd                      controlplane   3.5.12-0   3.5.12-0

You can now apply the upgrade by executing the following command:

        kubeadm upgrade apply v1.30.8

Note: Before you can perform this upgrade, you have to update kubeadm to v1.30.8.

_____________________________________________________________________


The table below shows the current state of component configs as understood by this version of kubeadm.
Configs that have a "yes" mark in the "MANUAL UPGRADE REQUIRED" column require manual config upgrade or
resetting to kubeadm defaults before a successful upgrade can be performed. The version to manually
upgrade to is denoted in the "PREFERRED VERSION" column.

API GROUP                 CURRENT VERSION   PREFERRED VERSION   MANUAL UPGRADE REQUIRED
kubeproxy.config.k8s.io   v1alpha1          v1alpha1            no
kubelet.config.k8s.io     v1beta1           v1beta1             no
_____________________________________________________________________




  ```
</details>


<details>
<summary>
Senario -
We will be upgrading the controlplane node first. Drain the controlplane node of workloads and mark it UnSchedulable

</summary>

```bash

controlplane ~ ➜  k get nodes -o wide 
NAME           STATUS   ROLES           AGE   VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION   CONTAINER-RUNTIME
controlplane   Ready    control-plane   15m   v1.30.0   192.11.149.3   <none>        Ubuntu 22.04.5 LTS   5.4.0-1106-gcp   containerd://1.6.26
node01         Ready    <none>          14m   v1.30.0   192.11.149.6   <none>        Ubuntu 22.04.5 LTS   5.4.0-1106-gcp   containerd://1.6.26


controlplane ~ ✖ k drain node01  --ignore-daemonsets 

controlplane ~ ➜  k get nodes -o wide 
NAME           STATUS   ROLES           AGE   VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION   CONTAINER-RUNTIME
controlplane   Ready    control-plane   17m   v1.30.0   192.11.149.3   <none>        Ubuntu 22.04.5 LTS   5.4.0-1106-gcp   containerd://1.6.26
node01         Ready    <none>          16m   v1.30.0   192.11.149.6   <none>        Ubuntu 22.04.5 LTS   5.4.0-1106-gcp   containerd://1.6.26

controlplane ~ ➜  k drain controlplane --ignore-daemonsets 
node/controlplane cordoned
Warning: ignoring DaemonSet-managed Pods: kube-flannel/kube-flannel-ds-ckmlq, kube-system/kube-proxy-kn26h
evicting pod kube-system/coredns-768b85b76f-glt4j
evicting pod default/blue-fffb6db8d-kjhzp
evicting pod default/blue-fffb6db8d-2mr5g
evicting pod kube-system/coredns-768b85b76f-gfmt5
evicting pod default/blue-fffb6db8d-xblkk
evicting pod default/blue-fffb6db8d-wppk7
evicting pod default/blue-fffb6db8d-djzk8
I0108 13:19:18.143718   17411 request.go:697] Waited for 1.060160947s due to client-side throttling, not priority and fairness, request: GET:https://controlplane:6443/api/v1/namespaces/default/pods/blue-fffb6db8d-kjhzp
pod/blue-fffb6db8d-djzk8 evicted
pod/blue-fffb6db8d-2mr5g evicted
pod/blue-fffb6db8d-wppk7 evicted
pod/blue-fffb6db8d-kjhzp evicted
pod/blue-fffb6db8d-xblkk evicted
pod/coredns-768b85b76f-glt4j evicted
pod/coredns-768b85b76f-gfmt5 evicted
node/controlplane drained

controlplane ~ ➜  k get nodes -o wide 
NAME           STATUS                     ROLES           AGE   VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION   CONTAINER-RUNTIME
controlplane   Ready,SchedulingDisabled   control-plane   17m   v1.30.0   192.11.149.3   <none>        Ubuntu 22.04.5 LTS   5.4.0-1106-gcp   containerd://1.6.26
node01         Ready                      <none>          16m   v1.30.0   192.11.149.6   <none>        Ubuntu 22.04.5 LTS   5.4.0-1106-gcp   containerd://1.6.26


controlplane ~ ➜  k get all -o wide 
NAME                       READY   STATUS    RESTARTS   AGE   IP            NODE     NOMINATED NODE   READINESS GATES
pod/blue-fffb6db8d-6vk5n   1/1     Running   0          31s   10.244.1.10   node01   <none>           <none>
pod/blue-fffb6db8d-k8d7f   1/1     Running   0          33s   10.244.1.8    node01   <none>           <none>
pod/blue-fffb6db8d-l7jx8   1/1     Running   0          31s   10.244.1.12   node01   <none>           <none>
pod/blue-fffb6db8d-t67kl   1/1     Running   0          32s   10.244.1.11   node01   <none>           <none>
pod/blue-fffb6db8d-wt92l   1/1     Running   0          31s   10.244.1.14   node01   <none>           <none>

NAME                  TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE   SELECTOR
service/kubernetes    ClusterIP   10.96.0.1       <none>        443/TCP        17m   <none>
service/red-service   NodePort    10.100.148.62   <none>        80:30080/TCP   15m   app=red

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES         SELECTOR
deployment.apps/blue   5/5     5            5           15m   nginx        nginx:alpine   app=blue

NAME                             DESIRED   CURRENT   READY   AGE   CONTAINERS   IMAGES         SELECTOR
replicaset.apps/blue-fffb6db8d   5         5         5       15m   nginx        nginx:alpine   app=blue,pod-template-hash=fffb6db8d




  ```
</details>


<details>
<summary>
Senario -
Upgrade the controlplane components to exact version v1.31.0

Upgrade the kubeadm tool (if not already), then the controlplane components, and finally the kubelet. Practice referring to the Kubernetes documentation page.
</summary>

```bash

controlplane ~ ✖ kubeadm upgrade plan
[upgrade/config] Making sure the configuration is correct:
[preflight] Running pre-flight checks.
[upgrade/config] Reading configuration from the cluster...
[upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
[upgrade] Running cluster health checks
[upgrade] Fetching available versions to upgrade to
[upgrade/versions] Cluster version: 1.30.0
[upgrade/versions] kubeadm version: v1.30.0
I0108 13:24:14.634631   20439 version.go:256] remote version is much newer: v1.32.0; falling back to: stable-1.30
[upgrade/versions] Target version: v1.30.8
[upgrade/versions] Latest version in the v1.30 series: v1.30.8

Components that must be upgraded manually after you have upgraded the control plane with 'kubeadm upgrade apply':
COMPONENT   NODE           CURRENT   TARGET
kubelet     controlplane   v1.30.0   v1.30.8
kubelet     node01         v1.30.0   v1.30.8

Upgrade to the latest version in the v1.30 series:

COMPONENT                 NODE           CURRENT    TARGET
kube-apiserver            controlplane   v1.30.0    v1.30.8
kube-controller-manager   controlplane   v1.30.0    v1.30.8
kube-scheduler            controlplane   v1.30.0    v1.30.8
kube-proxy                               1.30.0     v1.30.8
CoreDNS                                  v1.10.1    v1.11.1
etcd                      controlplane   3.5.12-0   3.5.12-0

You can now apply the upgrade by executing the following command:

        kubeadm upgrade apply v1.30.8

Note: Before you can perform this upgrade, you have to update kubeadm to v1.30.8.

_____________________________________________________________________


The table below shows the current state of component configs as understood by this version of kubeadm.
Configs that have a "yes" mark in the "MANUAL UPGRADE REQUIRED" column require manual config upgrade or
resetting to kubeadm defaults before a successful upgrade can be performed. The version to manually
upgrade to is denoted in the "PREFERRED VERSION" column.

API GROUP                 CURRENT VERSION   PREFERRED VERSION   MANUAL UPGRADE REQUIRED
kubeproxy.config.k8s.io   v1alpha1          v1alpha1            no
kubelet.config.k8s.io     v1beta1           v1beta1             no
_____________________________________________________________________


controlplane ~ ➜  vi /etc/apt/sources.list.d/kubernetes.list 

controlplane ~ ➜  cat /etc/ap
apparmor/   apparmor.d/ apt/        

controlplane ~ ➜  cat /etc/apt/sources.list
sources.list    sources.list.d/ 

controlplane ~ ➜  cat /etc/apt/sources.list.d/kubernetes.list 
deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /

controlplane ~ ➜  apt update
Hit:2 http://security.ubuntu.com/ubuntu jammy-security InRelease                            
Hit:3 https://download.docker.com/linux/ubuntu jammy InRelease                              
Get:1 https://prod-cdn.packages.k8s.io/repositories/isv:/kubernetes:/core:/stable:/v1.31/deb  InRelease [1,186 B]
Hit:4 http://archive.ubuntu.com/ubuntu jammy InRelease                                      
Hit:5 http://archive.ubuntu.com/ubuntu jammy-updates InRelease
Get:6 https://prod-cdn.packages.k8s.io/repositories/isv:/kubernetes:/core:/stable:/v1.31/deb  Packages [8,514 B]
Hit:7 http://archive.ubuntu.com/ubuntu jammy-backports InRelease
Fetched 9,700 B in 1s (13.0 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
35 packages can be upgraded. Run 'apt list --upgradable' to see them.

controlplane ~ ➜  sudo apt-cache madison kubeadm
   kubeadm | 1.31.4-1.1 | https://pkgs.k8s.io/core:/stable:/v1.31/deb  Packages
   kubeadm | 1.31.3-1.1 | https://pkgs.k8s.io/core:/stable:/v1.31/deb  Packages
   kubeadm | 1.31.2-1.1 | https://pkgs.k8s.io/core:/stable:/v1.31/deb  Packages
   kubeadm | 1.31.1-1.1 | https://pkgs.k8s.io/core:/stable:/v1.31/deb  Packages
   kubeadm | 1.31.0-1.1 | https://pkgs.k8s.io/core:/stable:/v1.31/deb  Packages

controlplane ~ ➜  apt-get install kubeadm=1.31.0-1.1
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages will be upgraded:
  kubeadm
1 upgraded, 0 newly installed, 0 to remove and 34 not upgraded.
Need to get 11.4 MB of archives.
After this operation, 8,040 kB of additional disk space will be used.
Get:1 https://prod-cdn.packages.k8s.io/repositories/isv:/kubernetes:/core:/stable:/v1.31/deb  kubeadm 1.31.0-1.1 [11.4 MB]
Fetched 11.4 MB in 0s (38.6 MB/s)
debconf: delaying package configuration, since apt-utils is not installed
(Reading database ... 21088 files and directories currently installed.)
Preparing to unpack .../kubeadm_1.31.0-1.1_amd64.deb ...
Unpacking kubeadm (1.31.0-1.1) over (1.30.0-1.1) ...
Setting up kubeadm (1.31.0-1.1) ...

controlplane ~ ➜  kubeadm version
kubeadm version: &version.Info{Major:"1", Minor:"31", GitVersion:"v1.31.0", GitCommit:"9edcffcde5595e8a5b1a35f88c421764e575afce", GitTreeState:"clean", BuildDate:"2024-08-13T07:35:57Z", GoVersion:"go1.22.5", Compiler:"gc", Platform:"linux/amd64"}

controlplane ~ ➜  kubeadm upgrade plan
[preflight] Running pre-flight checks.
[upgrade/config] Reading configuration from the cluster...
[upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
[upgrade] Running cluster health checks
[upgrade] Fetching available versions to upgrade to
[upgrade/versions] Cluster version: 1.30.0
[upgrade/versions] kubeadm version: v1.31.0
I0108 13:31:42.523470   23382 version.go:261] remote version is much newer: v1.32.0; falling back to: stable-1.31
[upgrade/versions] Target version: v1.31.4
[upgrade/versions] Latest version in the v1.30 series: v1.30.8

Components that must be upgraded manually after you have upgraded the control plane with 'kubeadm upgrade apply':
COMPONENT   NODE           CURRENT   TARGET
kubelet     controlplane   v1.30.0   v1.30.8
kubelet     node01         v1.30.0   v1.30.8

Upgrade to the latest version in the v1.30 series:

COMPONENT                 NODE           CURRENT    TARGET
kube-apiserver            controlplane   v1.30.0    v1.30.8
kube-controller-manager   controlplane   v1.30.0    v1.30.8
kube-scheduler            controlplane   v1.30.0    v1.30.8
kube-proxy                               1.30.0     v1.30.8
CoreDNS                                  v1.10.1    v1.11.1
etcd                      controlplane   3.5.12-0   3.5.15-0

You can now apply the upgrade by executing the following command:

        kubeadm upgrade apply v1.30.8

_____________________________________________________________________

Components that must be upgraded manually after you have upgraded the control plane with 'kubeadm upgrade apply':
COMPONENT   NODE           CURRENT   TARGET
kubelet     controlplane   v1.30.0   v1.31.4
kubelet     node01         v1.30.0   v1.31.4

Upgrade to the latest stable version:

COMPONENT                 NODE           CURRENT    TARGET
kube-apiserver            controlplane   v1.30.0    v1.31.4
kube-controller-manager   controlplane   v1.30.0    v1.31.4
kube-scheduler            controlplane   v1.30.0    v1.31.4
kube-proxy                               1.30.0     v1.31.4
CoreDNS                                  v1.10.1    v1.11.1
etcd                      controlplane   3.5.12-0   3.5.15-0

You can now apply the upgrade by executing the following command:

        kubeadm upgrade apply v1.31.4

Note: Before you can perform this upgrade, you have to update kubeadm to v1.31.4.

_____________________________________________________________________


The table below shows the current state of component configs as understood by this version of kubeadm.
Configs that have a "yes" mark in the "MANUAL UPGRADE REQUIRED" column require manual config upgrade or
resetting to kubeadm defaults before a successful upgrade can be performed. The version to manually
upgrade to is denoted in the "PREFERRED VERSION" column.

API GROUP                 CURRENT VERSION   PREFERRED VERSION   MANUAL UPGRADE REQUIRED
kubeproxy.config.k8s.io   v1alpha1          v1alpha1            no
kubelet.config.k8s.io     v1beta1           v1beta1             no
_____________________________________________________________________


controlplane ~ ➜    kubeadm upgrade apply v1.31.0
[preflight] Running pre-flight checks.
[upgrade/config] Reading configuration from the cluster...
[upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
[upgrade] Running cluster health checks
[upgrade/version] You have chosen to change the cluster version to "v1.31.0"
[upgrade/versions] Cluster version: v1.30.0
[upgrade/versions] kubeadm version: v1.31.0
[upgrade] Are you sure you want to proceed? [y/N]: y
[upgrade/prepull] Pulling images required for setting up a Kubernetes cluster
[upgrade/prepull] This might take a minute or two, depending on the speed of your internet connection
[upgrade/prepull] You can also perform this action beforehand using 'kubeadm config images pull'
W0108 13:32:33.323567   23693 checks.go:846] detected that the sandbox image "registry.k8s.io/pause:3.8" of the container runtime is inconsistent with that used by kubeadm.It is recommended to use "registry.k8s.io/pause:3.10" as the CRI sandbox image.
[upgrade/apply] Upgrading your Static Pod-hosted control plane to version "v1.31.0" (timeout: 5m0s)...
[upgrade/staticpods] Writing new Static Pod manifests to "/etc/kubernetes/tmp/kubeadm-upgraded-manifests3961908094"
[upgrade/staticpods] Preparing for "etcd" upgrade
[upgrade/staticpods] Renewing etcd-server certificate
[upgrade/staticpods] Renewing etcd-peer certificate
[upgrade/staticpods] Renewing etcd-healthcheck-client certificate
[upgrade/staticpods] Moving new manifest to "/etc/kubernetes/manifests/etcd.yaml" and backing up old manifest to "/etc/kubernetes/tmp/kubeadm-backup-manifests-2025-01-08-13-32-49/etcd.yaml"
[upgrade/staticpods] Waiting for the kubelet to restart the component
[upgrade/staticpods] This can take up to 5m0s
[apiclient] Found 1 Pods for label selector component=etcd
[upgrade/staticpods] Component "etcd" upgraded successfully!
[upgrade/etcd] Waiting for etcd to become available
[upgrade/staticpods] Preparing for "kube-apiserver" upgrade
[upgrade/staticpods] Renewing apiserver certificate
[upgrade/staticpods] Renewing apiserver-kubelet-client certificate
[upgrade/staticpods] Renewing front-proxy-client certificate
[upgrade/staticpods] Renewing apiserver-etcd-client certificate
[upgrade/staticpods] Moving new manifest to "/etc/kubernetes/manifests/kube-apiserver.yaml" and backing up old manifest to "/etc/kubernetes/tmp/kubeadm-backup-manifests-2025-01-08-13-32-49/kube-apiserver.yaml"
[upgrade/staticpods] Waiting for the kubelet to restart the component
[upgrade/staticpods] This can take up to 5m0s
[apiclient] Found 1 Pods for label selector component=kube-apiserver
[upgrade/staticpods] Component "kube-apiserver" upgraded successfully!
[upgrade/staticpods] Preparing for "kube-controller-manager" upgrade
[upgrade/staticpods] Renewing controller-manager.conf certificate
[upgrade/staticpods] Moving new manifest to "/etc/kubernetes/manifests/kube-controller-manager.yaml" and backing up old manifest to "/etc/kubernetes/tmp/kubeadm-backup-manifests-2025-01-08-13-32-49/kube-controller-manager.yaml"
[upgrade/staticpods] Waiting for the kubelet to restart the component
[upgrade/staticpods] This can take up to 5m0s
[apiclient] Found 1 Pods for label selector component=kube-controller-manager
[upgrade/staticpods] Component "kube-controller-manager" upgraded successfully!
[upgrade/staticpods] Preparing for "kube-scheduler" upgrade
[upgrade/staticpods] Renewing scheduler.conf certificate
[upgrade/staticpods] Moving new manifest to "/etc/kubernetes/manifests/kube-scheduler.yaml" and backing up old manifest to "/etc/kubernetes/tmp/kubeadm-backup-manifests-2025-01-08-13-32-49/kube-scheduler.yaml"
[upgrade/staticpods] Waiting for the kubelet to restart the component
[upgrade/staticpods] This can take up to 5m0s
[apiclient] Found 1 Pods for label selector component=kube-scheduler
[upgrade/staticpods] Component "kube-scheduler" upgraded successfully!
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config" in namespace kube-system with the configuration for the kubelets in the cluster
[upgrade] Backing up kubelet config file to /etc/kubernetes/tmp/kubeadm-kubelet-config305147061/config.yaml
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to get nodes
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] Configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] Configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

[upgrade/successful] SUCCESS! Your cluster was upgraded to "v1.31.0". Enjoy!

[upgrade/kubelet] Now that your control plane is upgraded, please proceed with upgrading your kubelets if you haven't already done so.

controlplane ~ ➜  kubeadm upgrade plan
[preflight] Running pre-flight checks.
[upgrade/config] Reading configuration from the cluster...
[upgrade/config] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -o yaml'
[upgrade] Running cluster health checks
[upgrade] Fetching available versions to upgrade to
[upgrade/versions] Cluster version: 1.31.0
[upgrade/versions] kubeadm version: v1.31.0
I0108 13:38:13.275901   27487 version.go:261] remote version is much newer: v1.32.0; falling back to: stable-1.31
[upgrade/versions] Target version: v1.31.4
[upgrade/versions] Latest version in the v1.31 series: v1.31.4

Components that must be upgraded manually after you have upgraded the control plane with 'kubeadm upgrade apply':
COMPONENT   NODE           CURRENT   TARGET
kubelet     controlplane   v1.30.0   v1.31.4
kubelet     node01         v1.30.0   v1.31.4

Upgrade to the latest version in the v1.31 series:

COMPONENT                 NODE           CURRENT    TARGET
kube-apiserver            controlplane   v1.31.0    v1.31.4
kube-controller-manager   controlplane   v1.31.0    v1.31.4
kube-scheduler            controlplane   v1.31.0    v1.31.4
kube-proxy                               1.31.0     v1.31.4
CoreDNS                                  v1.11.1    v1.11.1
etcd                      controlplane   3.5.15-0   3.5.15-0

You can now apply the upgrade by executing the following command:

        kubeadm upgrade apply v1.31.4

Note: Before you can perform this upgrade, you have to update kubeadm to v1.31.4.

_____________________________________________________________________


The table below shows the current state of component configs as understood by this version of kubeadm.
Configs that have a "yes" mark in the "MANUAL UPGRADE REQUIRED" column require manual config upgrade or
resetting to kubeadm defaults before a successful upgrade can be performed. The version to manually
upgrade to is denoted in the "PREFERRED VERSION" column.

API GROUP                 CURRENT VERSION   PREFERRED VERSION   MANUAL UPGRADE REQUIRED
kubeproxy.config.k8s.io   v1alpha1          v1alpha1            no
kubelet.config.k8s.io     v1beta1           v1beta1             no
_____________________________________________________________________


controlplane ~ ➜  apy-get install kubelet=1.31.0-1.1
-bash: apy-get: command not found

controlplane ~ ✖ apt-get install kubelet=1.31.0-1.1
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages will be upgraded:
  kubelet
1 upgraded, 0 newly installed, 0 to remove and 34 not upgraded.
Need to get 15.2 MB of archives.
After this operation, 23.2 MB disk space will be freed.
Get:1 https://prod-cdn.packages.k8s.io/repositories/isv:/kubernetes:/core:/stable:/v1.31/deb  kubelet 1.31.0-1.1 [15.2 MB]
Fetched 15.2 MB in 0s (33.9 MB/s)
debconf: delaying package configuration, since apt-utils is not installed
(Reading database ... 21088 files and directories currently installed.)
Preparing to unpack .../kubelet_1.31.0-1.1_amd64.deb ...
Unpacking kubelet (1.31.0-1.1) over (1.30.0-1.1) ...
dpkg: warning: unable to delete old directory '/etc/sysconfig': Directory not empty
Setting up kubelet (1.31.0-1.1) ...

controlplane ~ ➜  systemctl daemon-reload

controlplane ~ ➜  systemctl restart kubelet

controlplane ~ ➜  k get nodes -o wide 
NAME           STATUS                     ROLES           AGE   VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION   CONTAINER-RUNTIME
controlplane   Ready,SchedulingDisabled   control-plane   38m   v1.31.0   192.11.149.3   <none>        Ubuntu 22.04.5 LTS   5.4.0-1106-gcp   containerd://1.6.26
node01         Ready                      <none>          37m   v1.30.0   192.11.149.6   <none>        Ubuntu 22.04.5 LTS   5.4.0-1106-gcp   containerd://1.6.26

controlplane ~ ➜  

  ```

  ```bash
To seamlessly transition from Kubernetes v1.30 to v1.31 and gain access to the packages specific to the desired Kubernetes minor version, follow these essential steps during the upgrade process. This ensures that your environment is appropriately configured and aligned with the features and improvements introduced in Kubernetes v1.31.

On the controlplane node:

Use any text editor you prefer to open the file that defines the Kubernetes apt repository.

vim /etc/apt/sources.list.d/kubernetes.list
Update the version in the URL to the next available minor release, i.e v1.31.

deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /
After making changes, save the file and exit from your text editor. Proceed with the next instruction.

apt update

apt-cache madison kubeadm
Based on the version information displayed by apt-cache madison, it indicates that for Kubernetes version 1.31.0, the available package version is 1.31.0-1.1. Therefore, to install kubeadm for Kubernetes v1.31.0, use the following command:

apt-get install kubeadm=1.31.0-1.1
Run the following command to upgrade the Kubernetes cluster.

kubeadm upgrade plan v1.31.0

kubeadm upgrade apply v1.31.0
Note that the above steps can take a few minutes to complete.

Now, upgrade the Kubelet version. Also, mark the node (in this case, the "controlplane" node) as schedulable.

apt-get install kubelet=1.31.0-1.1
Run the following commands to refresh the systemd configuration and apply changes to the Kubelet service:

systemctl daemon-reload

systemctl restart kubelet


  ```
</details>


<details>
<summary>
Senario -
Mark the controlplane node as "Schedulable" again

</summary>

```bash

controlplane ~ ➜  k uncordon controlplane 
node/controlplane uncordoned

controlplane ~ ➜  k get nodes -o wide
NAME           STATUS   ROLES           AGE   VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION   CONTAINER-RUNTIME
controlplane   Ready    control-plane   51m   v1.31.0   192.11.149.3   <none>        Ubuntu 22.04.5 LTS   5.4.0-1106-gcp   containerd://1.6.26
node01         Ready    <none>          50m   v1.30.0   192.11.149.6   <none>        Ubuntu 22.04.5 LTS   5.4.0-1106-gcp   containerd://1.6.26



  ```
</details>


<details>
<summary>
Senario -
11 / 13
Next is the worker node. Drain the worker node of the workloads and mark it UnSchedulable
Upgrade the worker node to the exact version v1.31.0
Remove the restriction and mark the worker node as schedulable again.
</summary>

```bash

controlplane ~ ➜  k drain node01 --ignore-daemonsets 
node/node01 cordoned
Warning: ignoring DaemonSet-managed Pods: kube-flannel/kube-flannel-ds-th46j, kube-system/kube-proxy-9tc7r
evicting pod kube-system/coredns-6f6b679f8f-hhtnh
evicting pod default/blue-fffb6db8d-l7jx8
evicting pod default/blue-fffb6db8d-6vk5n
evicting pod default/blue-fffb6db8d-wt92l
evicting pod default/blue-fffb6db8d-k8d7f
evicting pod default/blue-fffb6db8d-t67kl
evicting pod kube-system/coredns-6f6b679f8f-gxxdl
pod/blue-fffb6db8d-wt92l evicted
pod/blue-fffb6db8d-k8d7f evicted
pod/blue-fffb6db8d-t67kl evicted
pod/blue-fffb6db8d-l7jx8 evicted
pod/blue-fffb6db8d-6vk5n evicted
pod/coredns-6f6b679f8f-hhtnh evicted
pod/coredns-6f6b679f8f-gxxdl evicted
node/node01 drained


On the node01 node, run the following commands:

If you are on the controlplane node, run ssh node01 to log in to the node01.

Use any text editor you prefer to open the file that defines the Kubernetes apt repository.

vim /etc/apt/sources.list.d/kubernetes.list
Update the version in the URL to the next available minor release, i.e v1.31.

deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /
After making changes, save the file and exit from your text editor. Proceed with the next instruction.

apt update

apt-cache madison kubeadm
Based on the version information displayed by apt-cache madison, it indicates that for Kubernetes version 1.31.0, the available package version is 1.31.0-1.1. Therefore, to install kubeadm for Kubernetes v1.31.0, use the following command:

apt-get install kubeadm=1.31.0-1.1

# Upgrade the node 
kubeadm upgrade node
Now, upgrade the Kubelet version.

apt-get install kubelet=1.31.0-1.1
Run the following commands to refresh the systemd configuration and apply changes to the Kubelet service:

systemctl daemon-reload

systemctl restart kubelet
Type exit or logout or enter CTRL + d to go back to the controlplane node.


controlplane ~ ➜  k uncordon node01 
node/node01 uncordoned

controlplane ~ ➜  k get nodes -o wide 
NAME           STATUS   ROLES           AGE   VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION   CONTAINER-RUNTIME
controlplane   Ready    control-plane   58m   v1.31.0   192.11.149.3   <none>        Ubuntu 22.04.5 LTS   5.4.0-1106-gcp   containerd://1.6.26
node01         Ready    <none>          58m   v1.31.0   192.11.149.6   <none>        Ubuntu 22.04.5 LTS   5.4.0-1106-gcp   containerd://1.6.26


  ```
</details>

Test Backup and Restore Methods

<details>
<summary>
Senario -

We have a working Kubernetes cluster with a set of web applications running. Let us first explore the setup.

How many deployments exist in the cluster in default namespace?
```bash

controlplane ~ ➜  k get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/blue-56dd475db5-lpkz2   1/1     Running   0          110s
pod/blue-56dd475db5-m26b2   1/1     Running   0          110s
pod/blue-56dd475db5-sjj7d   1/1     Running   0          110s
pod/red-8bd48b898-k68qv     1/1     Running   0          110s
pod/red-8bd48b898-vpdqq     1/1     Running   0          110s

NAME                   TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
service/blue-service   NodePort    172.20.174.106   <none>        80:30082/TCP   110s
service/kubernetes     ClusterIP   172.20.0.1       <none>        443/TCP        2m19s
service/red-service    NodePort    172.20.56.146    <none>        80:30080/TCP   110s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/blue   3/3     3            3           110s
deployment.apps/red    2/2     2            2           110s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/blue-56dd475db5   3         3         3       110s
replicaset.apps/red-8bd48b898     2         2         2       110s

  ```

  What is the version of ETCD running on the cluster?

Check the ETCD Pod or Process

</summary>

```bash

controlplane ~ ➜  k get all -n kube-system 
NAME                                       READY   STATUS    RESTARTS   AGE
pod/coredns-77d6fd4654-7twg5               1/1     Running   0          2m47s
pod/coredns-77d6fd4654-zrnsk               1/1     Running   0          2m47s
pod/etcd-controlplane                      1/1     Running   0          2m53s
pod/kube-apiserver-controlplane            1/1     Running   0          2m53s
pod/kube-controller-manager-controlplane   1/1     Running   0          2m53s
pod/kube-proxy-sw7vl                       1/1     Running   0          2m47s
pod/kube-scheduler-controlplane            1/1     Running   0          2m53s

NAME               TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)                  AGE
service/kube-dns   ClusterIP   172.20.0.10   <none>        53/UDP,53/TCP,9153/TCP   2m54s

NAME                        DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
daemonset.apps/kube-proxy   1         1         1       1            1           kubernetes.io/os=linux   2m54s

NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/coredns   2/2     2            2           2m54s

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/coredns-77d6fd4654   2         2         2       2m47s


controlplane ~ ➜  k describe pod/etcd-controlplane -n kube-system 
Name:                 etcd-controlplane
Namespace:            kube-system
Priority:             2000001000
Priority Class Name:  system-node-critical
Node:                 controlplane/192.168.28.34
Start Time:           Wed, 08 Jan 2025 14:27:07 +0000
Labels:               component=etcd
                      tier=control-plane
Annotations:          kubeadm.kubernetes.io/etcd.advertise-client-urls: https://192.168.28.34:2379
                      kubernetes.io/config.hash: 76ca12c631ba6d501968c667c6077175
                      kubernetes.io/config.mirror: 76ca12c631ba6d501968c667c6077175
                      kubernetes.io/config.seen: 2025-01-08T14:27:06.811588241Z
                      kubernetes.io/config.source: file
Status:               Running
SeccompProfile:       RuntimeDefault
IP:                   192.168.28.34
IPs:
  IP:           192.168.28.34
Controlled By:  Node/controlplane
Containers:
  etcd:
    Container ID:  containerd://a37f9a2fcc54591450022669a31b681824c204c42bbf360b47f06746a9589dc9
    Image:         registry.k8s.io/etcd:3.5.15-0
    Image ID:      registry.k8s.io/etcd@sha256:a6dc63e6e8cfa0307d7851762fa6b629afb18f28d8aa3fab5a6e91b4af60026a
    Port:          <none>
    Host Port:     <none>
    Command:
      etcd
      --advertise-client-urls=https://192.168.28.34:2379
      --cert-file=/etc/kubernetes/pki/etcd/server.crt
      --client-cert-auth=true
      --data-dir=/var/lib/etcd
      --experimental-initial-corrupt-check=true
      --experimental-watch-progress-notify-interval=5s
      --initial-advertise-peer-urls=https://192.168.28.34:2380
      --initial-cluster=controlplane=https://192.168.28.34:2380
      --key-file=/etc/kubernetes/pki/etcd/server.key
      --listen-client-urls=https://127.0.0.1:2379,https://192.168.28.34:2379
      --listen-metrics-urls=http://127.0.0.1:2381
      --listen-peer-urls=https://192.168.28.34:2380
      --name=controlplane
      --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
      --peer-client-cert-auth=true
      --peer-key-file=/etc/kubernetes/pki/etcd/peer.key
      --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
      --snapshot-count=10000
      --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
    State:          Running
      Started:      Wed, 08 Jan 2025 14:27:01 +0000
    Ready:          True
    Restart Count:  0
    Requests:
      cpu:        100m
      memory:     100Mi
    Liveness:     http-get http://127.0.0.1:2381/livez delay=10s timeout=15s period=10s #success=1 #failure=8
    Readiness:    http-get http://127.0.0.1:2381/readyz delay=0s timeout=15s period=1s #success=1 #failure=3
    Startup:      http-get http://127.0.0.1:2381/readyz delay=10s timeout=15s period=10s #success=1 #failure=24
    Environment:  <none>
    Mounts:
      /etc/kubernetes/pki/etcd from etcd-certs (rw)
      /var/lib/etcd from etcd-data (rw)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  etcd-certs:
    Type:          HostPath (bare host directory volume)
    Path:          /etc/kubernetes/pki/etcd
    HostPathType:  DirectoryOrCreate
  etcd-data:
    Type:          HostPath (bare host directory volume)
    Path:          /var/lib/etcd
    HostPathType:  DirectoryOrCreate
QoS Class:         Burstable
Node-Selectors:    <none>
Tolerations:       :NoExecute op=Exists
Events:
  Type     Reason                  Age    From     Message
  ----     ------                  ----   ----     -------
  Warning  FailedCreatePodSandBox  3m57s  kubelet  Failed to create pod sandbox: open /run/systemd/resolve/resolv.conf: no such file or directory
  Normal   Pulled                  3m42s  kubelet  Container image "registry.k8s.io/etcd:3.5.15-0" already present on machine
  Normal   Created                 3m42s  kubelet  Created container etcd
  Normal   Started                 3m42s  kubelet  Started container etcd



controlplane ~ ➜  k logs pod/etcd-controlplane -n kube-system | grep version
{"level":"info","ts":"2025-01-08T14:27:01.865783Z","caller":"embed/etcd.go:310","msg":"starting an etcd server","etcd-version":"3.5.15","git-sha":"9a5533382","go-version":"go1.21.12","go-os":"linux","go-arch":"amd64","max-cpu-set":16,"max-cpu-available":16,"member-initialized":false,"name":"controlplane","data-dir":"/var/lib/etcd","wal-dir":"","wal-dir-dedicated":"","member-dir":"/var/lib/etcd/member","force-new-cluster":false,"heartbeat-interval":"100ms","election-timeout":"1s","initial-election-tick-advance":true,"snapshot-count":10000,"max-wals":5,"max-snapshots":5,"snapshot-catchup-entries":5000,"initial-advertise-peer-urls":["https://192.168.28.34:2380"],"listen-peer-urls":["https://192.168.28.34:2380"],"advertise-client-urls":["https://192.168.28.34:2379"],"listen-client-urls":["https://127.0.0.1:2379","https://192.168.28.34:2379"],"listen-metrics-urls":["http://127.0.0.1:2381"],"cors":["*"],"host-whitelist":["*"],"initial-cluster":"controlplane=https://192.168.28.34:2380","initial-cluster-state":"new","initial-cluster-token":"etcd-cluster","quota-backend-bytes":2147483648,"max-request-bytes":1572864,"max-concurrent-streams":4294967295,"pre-vote":true,"initial-corrupt-check":true,"corrupt-check-time-interval":"0s","compact-check-time-enabled":false,"compact-check-time-interval":"1m0s","auto-compaction-mode":"periodic","auto-compaction-retention":"0s","auto-compaction-interval":"0s","discovery-url":"","discovery-proxy":"","downgrade-check-interval":"5s"}
{"level":"info","ts":"2025-01-08T14:27:01.869389Z","caller":"etcdserver/server.go:867","msg":"starting etcd server","local-member-id":"9f8ae0efe72af80c","local-server-version":"3.5.15","cluster-version":"to_be_decided"}
{"level":"info","ts":"2025-01-08T14:27:02.868252Z","caller":"etcdserver/server.go:2629","msg":"setting up initial cluster version using v2 API","cluster-version":"3.5"}
{"level":"info","ts":"2025-01-08T14:27:02.868350Z","caller":"membership/cluster.go:584","msg":"set initial cluster version","cluster-id":"e7f093258ffa9568","local-member-id":"9f8ae0efe72af80c","cluster-version":"3.5"}
{"level":"info","ts":"2025-01-08T14:27:02.868416Z","caller":"api/capability.go:75","msg":"enabled capabilities for version","cluster-version":"3.5"}
{"level":"info","ts":"2025-01-08T14:27:02.868440Z","caller":"etcdserver/server.go:2653","msg":"cluster version is updated","cluster-version":"3.5"}

controlplane ~ ➜  
  ```
</details>


<details>
<summary>
Senario -

At what address can you reach the ETCD cluster from the controlplane node?

Check the ETCD Service configuration in the ETCD POD

</summary>

```bash
controlplane ~ ➜  k describe pod/etcd-controlplane -n kube-system 
Name:                 etcd-controlplane
Namespace:            kube-system
Priority:             2000001000
Priority Class Name:  system-node-critical
Node:                 controlplane/192.168.28.34
Start Time:           Wed, 08 Jan 2025 14:27:07 +0000
Labels:               component=etcd
                      tier=control-plane
Annotations:          kubeadm.kubernetes.io/etcd.advertise-client-urls: https://192.168.28.34:2379
                      kubernetes.io/config.hash: 76ca12c631ba6d501968c667c6077175
                      kubernetes.io/config.mirror: 76ca12c631ba6d501968c667c6077175
                      kubernetes.io/config.seen: 2025-01-08T14:27:06.811588241Z
                      kubernetes.io/config.source: file
Status:               Running
SeccompProfile:       RuntimeDefault
IP:                   192.168.28.34
IPs:
  IP:           192.168.28.34
Controlled By:  Node/controlplane
Containers:
  etcd:
    Container ID:  containerd://a37f9a2fcc54591450022669a31b681824c204c42bbf360b47f06746a9589dc9
    Image:         registry.k8s.io/etcd:3.5.15-0
    Image ID:      registry.k8s.io/etcd@sha256:a6dc63e6e8cfa0307d7851762fa6b629afb18f28d8aa3fab5a6e91b4af60026a
    Port:          <none>
    Host Port:     <none>
    Command:
      etcd
      --advertise-client-urls=https://192.168.28.34:2379
      --cert-file=/etc/kubernetes/pki/etcd/server.crt
      --client-cert-auth=true
      --data-dir=/var/lib/etcd
      --experimental-initial-corrupt-check=true
      --experimental-watch-progress-notify-interval=5s
      --initial-advertise-peer-urls=https://192.168.28.34:2380
      --initial-cluster=controlplane=https://192.168.28.34:2380
      --key-file=/etc/kubernetes/pki/etcd/server.key
      --listen-client-urls=https://127.0.0.1:2379,https://192.168.28.34:2379
      --listen-metrics-urls=http://127.0.0.1:2381
      --listen-peer-urls=https://192.168.28.34:2380
      --name=controlplane
      --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
      --peer-client-cert-auth=true
      --peer-key-file=/etc/kubernetes/pki/etcd/peer.key
      --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
      --snapshot-count=10000
      --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
    State:          Running
      Started:      Wed, 08 Jan 2025 14:27:01 +0000
    Ready:          True
    Restart Count:  0
    Requests:
      cpu:        100m
      memory:     100Mi
    Liveness:     http-get http://127.0.0.1:2381/livez delay=10s timeout=15s period=10s #success=1 #failure=8
    Readiness:    http-get http://127.0.0.1:2381/readyz delay=0s timeout=15s period=1s #success=1 #failure=3
    Startup:      http-get http://127.0.0.1:2381/readyz delay=10s timeout=15s period=10s #success=1 #failure=24
    Environment:  <none>
    Mounts:
      /etc/kubernetes/pki/etcd from etcd-certs (rw)
      /var/lib/etcd from etcd-data (rw)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  etcd-certs:
    Type:          HostPath (bare host directory volume)
    Path:          /etc/kubernetes/pki/etcd
    HostPathType:  DirectoryOrCreate
  etcd-data:
    Type:          HostPath (bare host directory volume)
    Path:          /var/lib/etcd
    HostPathType:  DirectoryOrCreate
QoS Class:         Burstable
Node-Selectors:    <none>
Tolerations:       :NoExecute op=Exists
Events:
  Type     Reason                  Age    From     Message
  ----     ------                  ----   ----     -------
  Warning  FailedCreatePodSandBox  3m57s  kubelet  Failed to create pod sandbox: open /run/systemd/resolve/resolv.conf: no such file or directory
  Normal   Pulled                  3m42s  kubelet  Container image "registry.k8s.io/etcd:3.5.15-0" already present on machine
  Normal   Created                 3m42s  kubelet  Created container etcd
  Normal   Started                 3m42s  kubelet  Started container etcd


      --listen-client-urls=https://127.0.0.1:2379,https://192.168.28.34:2379
  ```
</details>

<details>
<summary>
Senario -

Where is the ETCD server certificate file located?
Note this path down as you will need to use it later

Where is the ETCD CA Certificate file located?
Note this path down as you will need to use it later.

</summary>

```bash
  Command:
      etcd
      --advertise-client-urls=https://192.168.28.34:2379
      --cert-file=/etc/kubernetes/pki/etcd/server.crt
      --client-cert-auth=true
      --data-dir=/var/lib/etcd
      --experimental-initial-corrupt-check=true
      --experimental-watch-progress-notify-interval=5s
      --initial-advertise-peer-urls=https://192.168.28.34:2380
      --initial-cluster=controlplane=https://192.168.28.34:2380
      --key-file=/etc/kubernetes/pki/etcd/server.key
      --listen-client-urls=https://127.0.0.1:2379,https://192.168.28.34:2379
      --listen-metrics-urls=http://127.0.0.1:2381
      --listen-peer-urls=https://192.168.28.34:2380
      --name=controlplane
      --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
      --peer-client-cert-auth=true
      --peer-key-file=/etc/kubernetes/pki/etcd/peer.key
      --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
      --snapshot-count=10000
      --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
    State:          Running

      --cert-file=/etc/kubernetes/pki/etcd/server.crt

      --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
  ```
</details>


<details>
<summary>
Senario -
The master node in our cluster is planned for a regular maintenance reboot tonight. While we do not anticipate anything to go wrong, we are required to take the necessary backups. Take a snapshot of the ETCD database using the built-in snapshot functionality.

Store the backup file at location /opt/snapshot-pre-boot.db

</summary>

```bash
Use the etcdctl snapshot save command. You will have to make use of additional flags to connect to the ETCD server.
--endpoints: Optional Flag, points to the address where ETCD is running (127.0.0.1:2379)
--cacert: Mandatory Flag (Absolute Path to the CA certificate file)
--cert: Mandatory Flag (Absolute Path to the Server certificate file)
--key: Mandatory Flag (Absolute Path to the Key file)


controlplane ~ ✖ ETCDCTL_API=3 etcdctl --endpoints 127.0.0.1:2379 \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  snapshot save /opt/snapshot-pre-boot.db

Snapshot saved at /opt/snapshot-pre-boot.db


  ```
</details>


<details>
<summary>
Senario -
Wake up! We have a conference call! After the reboot the master nodes came back online, but none of our applications are accessible. Check the status of the applications on the cluster. What's wrong?

```bash
controlplane ~ ➜  k get all
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   172.20.0.1   <none>        443/TCP   18s


  ```
  Luckily we took a backup. Restore the original state of the cluster using the backup file.

</summary>

```bash
First Restore the snapshot:

root@controlplane:~# ETCDCTL_API=3 etcdctl  --data-dir /var/lib/etcd-from-backup \
snapshot restore /opt/snapshot-pre-boot.db


2022-03-25 09:19:27.175043 I | mvcc: restore compact to 2552
2022-03-25 09:19:27.266709 I | etcdserver/membership: added member 8e9e05c52164694d [http://localhost:2380] to cluster cdf818194e3a8c32
root@controlplane:~# 


Note: In this case, we are restoring the snapshot to a different directory but in the same server where we took the backup (the controlplane node) As a result, the only required option for the restore command is the --data-dir.



Next, update the /etc/kubernetes/manifests/etcd.yaml:

We have now restored the etcd snapshot to a new path on the controlplane - /var/lib/etcd-from-backup, so, the only change to be made in the YAML file, is to change the hostPath for the volume called etcd-data from old directory (/var/lib/etcd) to the new directory (/var/lib/etcd-from-backup).

  volumes:
  - hostPath:
      path: /var/lib/etcd-from-backup
      type: DirectoryOrCreate
    name: etcd-data
With this change, /var/lib/etcd on the container points to /var/lib/etcd-from-backup on the controlplane (which is what we want).

When this file is updated, the ETCD pod is automatically re-created as this is a static pod placed under the /etc/kubernetes/manifests directory.



Note 1: As the ETCD pod has changed it will automatically restart, and also kube-controller-manager and kube-scheduler. Wait 1-2 to mins for this pods to restart. You can run the command: watch "crictl ps | grep etcd" to see when the ETCD pod is restarted.

Note 2: If the etcd pod is not getting Ready 1/1, then restart it by kubectl delete pod -n kube-system etcd-controlplane and wait 1 minute.

Note 3: This is the simplest way to make sure that ETCD uses the restored data after the ETCD pod is recreated. You don't have to change anything else.



If you do change --data-dir to /var/lib/etcd-from-backup in the ETCD YAML file, make sure that the volumeMounts for etcd-data is updated as well, with the mountPath pointing to /var/lib/etcd-from-backup (THIS COMPLETE STEP IS OPTIONAL AND NEED NOT BE DONE FOR COMPLETING THE RESTORE)

  ```
</details>

 Backup and Restore Methods 2
 In this lab environment, you will get to work with multiple kubernetes clusters where we will practice backing up and restoring the ETCD database.
<details>
<summary>
Senario -
You will notice that, you are logged in to the student-node (instead of the controlplane).
The student-node has the kubectl client and has access to all the Kubernetes clusters that are configured in this lab environment.
Before proceeding to the next question, explore the student-node and the clusters it has access to.
```bash

student-node ~ ➜  cat .kube/config
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCVENDQWUyZ0F3SUJBZ0lJTVMyRXRiMk9FaWd3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TlRBeE1EZ3hORE13TlRGYUZ3MHpOVEF4TURZeE5ETTFOVEZhTUJVeApFekFSQmdOVkJBTVRDbXQxWW1WeWJtVjBaWE13Z2dFaU1BMEdDU3FHU0liM0RRRUJBUVVBQTRJQkR3QXdnZ0VLCkFvSUJBUUMwSDA4dGpFd3FPb1BTV085NmY5U1FHNXU5R0J1eVpxMERJeVFjdlRLMmllanZqRDVoRkp1TmJ6Ui8KUi9zdHRwbDBIZ0JYVnErZlR2NEs0Sk0xSksxc2tTVzZXb1JtZC9PM1VVR3lWVGlhZEQrd0JaRTEzK3U2U1ZtMgpwMTZXMW1nMXpndVNLemFycjFLOHdqNjdLVWJTdkpXWmRzR21SUEFSL0pvQkRkTDdjcThRK1hmN0ZxaXlTNFhiCnJBbndYYkhxZjRDeGJtaDIrcGlUcWZUeVU1TmRmemJZUWlpV3R3U2xVeUttWmQyOFhWazJKemd2NWF0SWhKUWQKa1I3QkgxYkpGMGl3R1pidUFsdEEzTndpL2lhTzhsbit5dCtQb0IyRU5SYjhsb01xdGtGRUhQSG1qN2E2NDJ3bgpTZEZrV2ZZbnByQnpiRFRBSVNLK2l6RzRuQXFkQWdNQkFBR2pXVEJYTUE0R0ExVWREd0VCL3dRRUF3SUNwREFQCkJnTlZIUk1CQWY4RUJUQURBUUgvTUIwR0ExVWREZ1FXQkJTSk9zOU5ZcHZESC9RYWJoVC9DMVEwUlV4aklUQVYKQmdOVkhSRUVEakFNZ2dwcmRXSmxjbTVsZEdWek1BMEdDU3FHU0liM0RRRUJDd1VBQTRJQkFRQUNFeHpmM3pUaQp3M3k3UkhvU01UK0pFYzIxVG5vWGZJbVc4a0czWTNSc0I0QmNLN3lRQWxaL3RqRGxYSXhtQXFYN0IyWjR5UzZMCmlmY0RLb3FHSTltTWRpNXplc1BuZ3h4R3psTXdYUWNEOE9vUVNyclRBY2tybTBjdUQ2TThLem8zSVhuZFI1SWUKQXFlWm91NmN3S1lWdHNUSU5WVU5PVTU5N0cwN0FDUUJYc1pJT1hLaGIwdFdnUHhWbS94VzBFSzhXWDNwNVUvcgpRUStCaW43K3NxLy9ieCtRSzlQbmh0UnVYQnpKYS9PeVYvWDJPSlpIQ0FFYVhrQ3JiN0JuTTAyRjhtdVJxMUtLCnI1OTY1YWFNaTc3SmkyUEFSNzlUUjFjcFFXajVtTUIwTzVSNFVWWlZYdWNwRWhJUE5pRlQraVVHSERvUE9IYXkKcjhtL0MyQzRtdGZtCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    server: https://cluster1-controlplane:6443
  name: cluster1
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCVENDQWUyZ0F3SUJBZ0lJTUg2M2pybnpUMFV3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TlRBeE1EZ3hORE13TXpWYUZ3MHpOVEF4TURZeE5ETTFNelZhTUJVeApFekFSQmdOVkJBTVRDbXQxWW1WeWJtVjBaWE13Z2dFaU1BMEdDU3FHU0liM0RRRUJBUVVBQTRJQkR3QXdnZ0VLCkFvSUJBUURCMDl6RFlva3JZS3d1cGtGVzVLOVlodkV3OEJ6ZVh2VXY5TzdiaFFMVUNBdFhtcW9Lalo3TW01S20KTnlidUdvUmFvcmxBNWYwbG5nK3duaWtEN1BKZ2w1eFlqbEZWY2w0L0Q2TEJpalBFamxLVmVYbkJ4V2V1VWd1agpxWXRzQ0Z0K0FqN1MrMklWSUZNUUh5UUVOUGRHVk8xUnB2aEdYSTBwcEl6MEk0ZEh1eUxiRndYM1R3a3JraGNYCnREOEx1U09ZUVFMN0paZ0dONmFIYWU1MXNhWHJndmk2aFhWVVB6eDFibE9jaFhCUFJ3TEwyL21XMCtITkZ3algKOVcrenpLNGZjRXhQT0o0eHRORW4zTyt5NXlZS3h5MDJYOTcvL0p5WkF1RWZLclhDajJldFAzVEFpR1FaV2tpcAp2VHdpT2VodnVRV3ZnQWZ3N3YrUUJtUjBQNCtiQWdNQkFBR2pXVEJYTUE0R0ExVWREd0VCL3dRRUF3SUNwREFQCkJnTlZIUk1CQWY4RUJUQURBUUgvTUIwR0ExVWREZ1FXQkJST1AzOG1PcFpPNHNyS21va0dubHR2eStmamJUQVYKQmdOVkhSRUVEakFNZ2dwcmRXSmxjbTVsZEdWek1BMEdDU3FHU0liM0RRRUJDd1VBQTRJQkFRQzNIU1NRWU81eQp4SlNMakNBQTNMTnhnZWtXMjkrZGxBcUxCa2g4c0FFaXJWVGpDN1UxMnppaFRPZ1dPYXdWdjArdGVUa21VN2VDCmk2WmY4RXJuNm9ZekhlK0FNZmJKWGpBSllsK3V4QWlVSlRpQkRhSFJSUDI5UUxVb1F0Ym5sa2ozcG04ZG92YlAKTGdUdTBBeDZlYklRTmd4Z3ZqWTd5TWJtOGk3S3FhVVlVQ2VQUWN1Q1dGWDd3NXozOGFSTzBRTXpqUnVzaFRXawpXK2NPeGhKdlZnVkxyUmwyQVVKQzVuR0hpSUEzYWlUNHNZamZHUGZmVnI2dFRnczNDejhXTThVQjFBS3llTEUxCkFNZmg3WTlacnZOOHE4dldKZCtGNlBTNEliNTlEYTFMWWwwOXl1TFl3dzNmQ2poV21wRnNVdjBQTW9PR3Zab3UKdXljcUtOSWUzYnZqCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    server: https://192.14.144.12:6443
  name: cluster2
contexts:
- context:
    cluster: cluster1
    user: cluster1
  name: cluster1
- context:
    cluster: cluster2
    user: cluster2
  name: cluster2
current-context: cluster1
kind: Config
preferences: {}
users:
- name: cluster1
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURLVENDQWhHZ0F3SUJBZ0lJUXZ2NnpBNk9jSXN3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TlRBeE1EZ3hORE13TlRGYUZ3MHlOakF4TURneE5ETTFOVFJhTUR3eApIekFkQmdOVkJBb1RGbXQxWW1WaFpHMDZZMngxYzNSbGNpMWhaRzFwYm5NeEdUQVhCZ05WQkFNVEVHdDFZbVZ5CmJtVjBaWE10WVdSdGFXNHdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUtBb0lCQVFETGh5TnIKenp2SytBMjV3N0JLZEN2c0gxeU1jckNYWFZBcThWZXJnNk45RUN6Y0pzdEhDUzVTZEdBWkNDT2dodG9iS09NaApXSENPSXpGSXRja1VidGREcTVESGhsVHZIS044Q1F5U2E4QjBRMkpIOFRTU0I0ZzhnQklTcXNTR2F4Yk9ib2lPCm05VTQvdDhSN2pZcWF5ZTVwTkRKMGc4d1diQ3RBekVJYm41OWpsMTU0TVlFNGd2SU0vSVNYcUQ3Qy9qbGJXYVcKNVR6aWg2MEw2YjdJSloxZ2RleVJpcEluam5KWUVtKzlBV1U1MlhMdTY3UEl4OG5vUE96S1JxQmJTUWlTQkFDSwplTVlTUHFwMWRsZkE1UkxaUk4yMnphdFhiMVFRQXZidzYxaVRhdjJjbXF2bHhRbWpLVUN5MUlhdDlTYzVPSWhZCjk2ajI1bTlCK2U0cjBGQXpBZ01CQUFHalZqQlVNQTRHQTFVZER3RUIvd1FFQXdJRm9EQVRCZ05WSFNVRUREQUsKQmdnckJnRUZCUWNEQWpBTUJnTlZIUk1CQWY4RUFqQUFNQjhHQTFVZEl3UVlNQmFBRklrNnowMWltOE1mOUJwdQpGUDhMVkRSRlRHTWhNQTBHQ1NxR1NJYjNEUUVCQ3dVQUE0SUJBUUNrQ3V2TjFnTDAvS2VWcjVISU1ZNW9NdTFrCmx0Zm5jRTg5VXlUWnN0MzlFRTl0ZExlZ1dmdndpRUJ6QlMrVG1xNkEzdXZiZFhtVXpZc1h4UDRrdXpHRjNubloKWWxSWHVSeWtKL3BrdlRvY1BVS2dpcUdZV2R6TUVYajVoU3BmeWVkVnUzTFVEZW9jbjFkYXhkUEpYMTdRbERkOQpoTDB5c1pUR0s2aHhQZ2E2MGgxY25XVzhxUVZSTnFQa3lqdVBHSFZaZmtjVXlqRi9BSjRuaGc0a24rR0FBRG50CnA5RlEyTDhJSklnZnBNUUZ6OUg3K3AxTzBKSGpoYVZMbFdXaWViNFpJblZ3bXdIZGdNTEdhQmR1TkUyWGtCZUQKbCtIZEFNd3orbDNXUkQ3U1VZN1BhZWtYb3FaVWxXWjVxUG44WUFMOEtrdDNRd05aY0JOejBSS2ZJeGlvCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFcEFJQkFBS0NBUUVBeTRjamE4ODd5dmdOdWNPd1NuUXI3QjljakhLd2wxMVFLdkZYcTRPamZSQXMzQ2JMClJ3a3VVblJnR1Fnam9JYmFHeWpqSVZod2ppTXhTTFhKRkc3WFE2dVF4NFpVN3h5amZBa01rbXZBZEVOaVIvRTAKa2dlSVBJQVNFcXJFaG1zV3ptNklqcHZWT1A3ZkVlNDJLbXNudWFUUXlkSVBNRm13clFNeENHNStmWTVkZWVERwpCT0lMeURQeUVsNmcrd3Y0NVcxbWx1VTg0b2V0QyttK3lDV2RZSFhza1lxU0o0NXlXQkp2dlFGbE9kbHk3dXV6CnlNZko2RHpzeWthZ1cwa0lrZ1FBaW5qR0VqNnFkWFpYd09VUzJVVGR0czJyVjI5VUVBTDI4T3RZazJyOW5KcXIKNWNVSm95bEFzdFNHcmZVbk9UaUlXUGVvOXVadlFmbnVLOUJRTXdJREFRQUJBb0lCQUdraWo5dWJHMm9IcFI5Mgp4TlJSR25kSTVBZnhIdU94NGoyc21mc1JieHlsaEdiYzYxandseXNrenBkZnF2ZTdjV05pUmFIdUM0YUJDMjVoCjZWTlN4b1JBVmZPYm9yZzU5UUdYc3hUWDhIeWVhbUI3YXdxY05YWDdPbHJHTitxdjJ3SWIreEtmMFE4MktZa2IKb29OYkF4UlUzVDNEOHZDcEJDajdZeExWOFNWdHZxcjk5YmZWTEs4bDYrZFdFVjl1YVdSbUZJK2lZS3dHRk5KRgpOaStHKzgzMmc3clJ2cUV2MXBsTEJTNWVlUk84OVdhTUdOcnVyOXJTMyttTlo4L2JZbmdFK2JmYURzWDB3WVRxCnhsRjIyWkZPWVVqMmxoUTZhTnJ6Nk9CZHNUbTkyT1g1R1QxWjEvNVhRUEI0M0gyc25ZblZNUU8xZ3MxdWlXTFQKckNqbFdURUNnWUVBOTBVY3EzYUxHN0pLUUl0a0phL3FoV21DWERmZlBZNlBhQ1BBQWpUU2pzemF1RC9rbWQ3Mgo1M0V3RFZjWUQzTnBid0JzVHFSUERYRnVVdGc0V1lseEZSSU1NdHFDZ1ordG5CdmF4V2dtWDhGeThiN2RGb1hyCnc1ekRkdEQxd2lVU1NyWlpRUldqaWQ2bVdqallWSjZ4NFFHMXBhSUJsNk90NjVzeXZ3aEMxODBDZ1lFQTByYXMKcUJJWFVzZm1YNWVoUXRyNWRTZ2JML1MySEgrcituN0xwZkhhc1NLQ0lhbEhocTZSWjJtNUVBbm1yVzVQcEZLcQppRmZDZ1FRcDBvVXpOclFPU2RTeURmekRIVE9FNm1jL1F4RHB6MFIzUERSL0RmQkJ3S2VLVGM1VkFhMk9ReXFaCmtJTmVtcVFqRk9EWDZBWlUxc1F6d0d3Y0ZiL01uWGo1ck1obngvOENnWUFBdmtkd3lJRndpdCtvUkFNQURnQlIKZUZpNCtENEdqRmdvQXZ0L3hqTGYxUVBQUy9uZThJbWpkbmFqZzZDQjQySHN3SDZTMmZHVzQ4Rm1KRm8vQXFmQgpYWFdGN1p3NDBZY1BFaEpkUjR2ZCs5SGhvaVk5L0JjU1ZuVkw5U0xYKzQ2TGhQTE52RWZxa1MzRTFham8wRFZPCnJOZWo4Tkc2Wi9hTTM4R09TM01QL1FLQmdRQys2SFRidmJCVjl2a1pLTGNaYkczUklLb3BNOHgvVzRrRVpqYTYKUGJyRy9CM3N3NE1qV2l3eEJvOStJWXBYSFhlWXlMRHE0S0EvNWZiQ1hwNDg5THR4VnZ5UFFhL0JXRGtYYXIzZApWSWlSbWxWeFBaclRsUmJ0cHAzZXlvOG4rd2ZHZ09VckVtcjNMcmorSkl4WkVzdHczY2lKa1NtTTV2NUt1aDF6CjF3cDRId0tCZ1FEMWREK3UrZUd6NmhkTDZRRFlkM1cvRGd4dGxOUnlBa2hjeXA5TnFzZy9jbXo2czg2aGJYeGUKR1pVamNoUkZUUmg3NnY1MFRDVXE2YTNTaDNWaWtINEg1cE14bXNUMFpBNU4yM1A4MHg0Z0pXaEVhMDkzeW96UApBYzJZeFhzc21QQmgxcmlPUlhZTmcrVC9HamZJMlBtTkZHNENVNjVTckRLcXcxZjRMejdaY3c9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=
- name: cluster2
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURLVENDQWhHZ0F3SUJBZ0lJSEkvQ0Q0emNDU1F3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TlRBeE1EZ3hORE13TXpWYUZ3MHlOakF4TURneE5ETTFNelphTUR3eApIekFkQmdOVkJBb1RGbXQxWW1WaFpHMDZZMngxYzNSbGNpMWhaRzFwYm5NeEdUQVhCZ05WQkFNVEVHdDFZbVZ5CmJtVjBaWE10WVdSdGFXNHdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUtBb0lCQVFER0pvOVkKZUdYczZSenVGbFRDNUg0R1REelhsZ200NmZBbGNhYnZXbFNLZjZBaVl3d291TkJPMTl3bVVGdVIrYUk2SWUweQorSURKRFVkV1RyZUM1dmk0NERoS1UxdHVQOWV6dEp1aUdkQ29VZWJVbDZrZE5RN0xhNCtjek5DdXBhTzFVcGVECm40RDRYUzJsYVpTRTk3aitlZmNjd3RuZWh5Wm0wcUZ4ZUNOcTNRaFpjekIxQVFSTWRaRzZuTE4vcUZwMWVWTUEKakwrbGZzS2kxLy8vbFhRRkx4bDJMQmlabXQrdHBZK1MwRkptWUppdGx2UzVzWGlrN0FpOXROZUYwZnQ4anRZYgowU2dqNFhpdGZnNGJFam0wcW1pUmFMaWppWXhHT0xmMnFGREFqT0pDL09YUUtxU3d4QUdwS1hlc2h0ZDcwbkgvCkREOTJRc2txOGhTK1BwNUxBZ01CQUFHalZqQlVNQTRHQTFVZER3RUIvd1FFQXdJRm9EQVRCZ05WSFNVRUREQUsKQmdnckJnRUZCUWNEQWpBTUJnTlZIUk1CQWY4RUFqQUFNQjhHQTFVZEl3UVlNQmFBRkU0L2Z5WTZsazdpeXNxYQppUWFlVzIvTDUrTnRNQTBHQ1NxR1NJYjNEUUVCQ3dVQUE0SUJBUUJDTmlhb1dXZGhDQ05Fc0VhNUJKMTRiVS9nCnplWGp2Nmp6b1JSSHo3ZFhZdGp6N2xMREFPaTlBUVdUbGF0RTdHaTNQOWZZd2xaOC9DR0dYMHpzQmc4QlZpQWYKUzhqM3NMZ3VuOCtjNXV1bERTdkhrQm5rNXhYSlIvcDl6WmozblBuaFVHNUh1RFMzczc2NlI4OU9kb3gzMDhnVwowOC85UFVSYTc3dExnOTBLOSsvY21HdkY0R0R3WDBpQmdLL2hYOWVmMEtrMUh0Wnd1dGhodjR3bXRXQzR4SUFzClNzeXNTSUU5MDRETWdTc2ZCUisxeGFPNUt0OCs4S3lTNFZDRnBOd2EzazBla055Ny9udmJFa0szekoxdW03d1AKTU9UdStQQzB0NUpGOE9vWU9rMTNPejF2QmJWNTRLVGFvQzhqWGlWQUpMdGVVM0RlZnNmbERDN3JnQWhXCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFcEFJQkFBS0NBUUVBeGlhUFdIaGw3T2tjN2haVXd1UitCa3c4MTVZSnVPbndKWEdtNzFwVWluK2dJbU1NCktMalFUdGZjSmxCYmtmbWlPaUh0TXZpQXlRMUhWazYzZ3ViNHVPQTRTbE5iYmovWHM3U2JvaG5RcUZIbTFKZXAKSFRVT3kydVBuTXpRcnFXanRWS1hnNStBK0YwdHBXbVVoUGU0L25uM0hNTFozb2NtWnRLaGNYZ2phdDBJV1hNdwpkUUVFVEhXUnVweXpmNmhhZFhsVEFJeS9wWDdDb3RmLy81VjBCUzhaZGl3WW1acmZyYVdQa3RCU1ptQ1lyWmIwCnViRjRwT3dJdmJUWGhkSDdmSTdXRzlFb0krRjRyWDRPR3hJNXRLcG9rV2k0bzRtTVJqaTM5cWhRd0l6aVF2emwKMENxa3NNUUJxU2wzckliWGU5Sngvd3cvZGtMSkt2SVV2ajZlU3dJREFRQUJBb0lCQVFDTzhFbi9tL3FLeUM2aQpZV0dMcGFCemtwTmhRVTZLSWRlbWF6SHMvOUIwaUZSUCtmdFBkT0p2dmErRncvb0tkM1hiVVpYMmF1VWNJNFFPCmNvSlczNThVWGtxODh4ekZqekJlNWk1TzVTaHNBNGpzV3VzWHNxcVZlMllCeW8zUExwWEZ1MnFvL0dyZ3Z2YXEKbUZzWEFFeG01anlvZHJjTG01ck1HYTEwV1NIVGQ1Rjh2RXZwTGxjejhxYmdtamlzc2Z1cDBRWHMwdmFjajNuaAphU2JCNEN0VnYzZkw4MVVoUlF3dVBkYll1eTBkVzRXbW5LZVZqd2JhT25NbTJEWUFkb2tYWWJpazY4bUJuQlF1Ci9ESnJjc25HdEM1WUgxTzlzNEVsUm0wMHgrSkw2RGtBaTFmaysxdnltY1psWmczZFVFSkNsZjNaanZRTDIzQ3cKNXdYTy8wQkJBb0dCQU85eDUrWDhNUGptSlpyZmZrMVVSU0lyVlBxaGJYVEtOVUQxeVRNc3lKTkV1SVVTSTZ6MwpyUDd1NGlMWHQvNWphZklKbHRJRjJMSFdrdXNSdE1xMi8rZmFaNGNYczk5ZHZDRGduVG5wMFZic3M1YnpxTFZMCi9TZkFwYnkzVDBmTWljeTAvTElMVWtvYzNHamRWK1R1UXF2NmJBbEhVaGs2Rkp3NWlFelN5b1FaQW9HQkFOUFoKd2plZW8veG1CVTc1RngxS0VNSVhoTXg3SlVDQmM1OUNRZVVzc2YzRUVMUkJmV2FFZXo3QTZ4OG0vcEI3eDV4WApMbm5rMCtFU3FDVmFxNUpBY2o2SUZNK1RaS21oVUxKWUpuVlN4dE10aEhoRVZBOW1xOVBmTHgxL0MwZktRbTlVCjFvd3U5akpCLzYxcnhvMWYvWVJuanVrMUhOMzd6enlVYUJPbmFlSURBb0dBS3ZrMGQzQkZrY1lzWlpsSzM3SUsKSGxITWxKYTRRRWV5T3hCaHBjK1p4K1NEZ1U1NkhLYnU0Mm9ZSjh5emR2SjhwelhWWGFneTllZmV3bFdSTHZpZApBUTJQbXcxRCtRaW5wUGJyQU9QWDhGUFcvM2FIaHRJMmd4WHVLT3AxVGh1RmQ3bmJ5RkNEMmsxSHVZSlFMZ1FjCjd5U29reG03d0U5T042T1R0TVg0RnJFQ2dZQjhzc0RQWEdIS1A0TUFhVDVoenlidUdFbmkwdG1xam9nd1JoL2oKZGR1UE04bGhVT3N2WE92dWxEL1JwbkpST1lyVlM3dmZwQUhUaWRmUHBQODRweGFZR0NHbnpXcURMcUVCV1VISgo3Vjk3RGwvYkt0MFA4NjlRbHg1MGprTmxqbmhzYjlPWjBFcTZhbjdFNVp1SnUrYk4rT2IxL24xdmJMa2ZQM3ZECmxkZU5ZUUtCZ1FEZmdwVS9oajgvN2pIeDhVRjRNTVE1L2t6V25jTERyWUtYejhDQkZSUmdhdmMxaXNUZW5xYTcKZ3lkV09pTVFYbkl5ZDRUQ2x4NDZBU1JIMjFnUVNKbk41YjExdDVReWsxSU1RaVBxQkllZVBBZEtvZTNRQ0FzMAo0YzZYOXQvNmYvRXpNd05vWWUyRkVhdmYvLys5czQ1STd1ekNEOTdsRUwzbTd0M2lJSjVMbWc9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=


dent-node ~ ➜    kubectl config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://cluster1-controlplane:6443
  name: cluster1
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://192.14.144.12:6443
  name: cluster2
contexts:
- context:
    cluster: cluster1
    user: cluster1
  name: cluster1
- context:
    cluster: cluster2
    user: cluster2
  name: cluster2
current-context: cluster1
kind: Config
preferences: {}
users:
- name: cluster1
  user:
    client-certificate-data: DATA+OMITTED
    client-key-data: DATA+OMITTED
- name: cluster2
  user:
    client-certificate-data: DATA+OMITTED
    client-key-data: DATA+OMITTED


  ```
How many nodes (both controlplane and worker) are part of cluster1?
Make sure to switch the context to cluster1:

What is the name of the controlplane node in cluster2?
Make sure to switch the context to cluster2:

</summary>

```bash

student-node ~ ➜  k config use-context cluster1 
Switched to context "cluster1".

student-node ~ ➜  k get nodes 
NAME                    STATUS   ROLES           AGE   VERSION
cluster1-controlplane   Ready    control-plane   39m   v1.29.0
cluster1-node01         Ready    <none>          39m   v1.29.0



student-node ~ ➜  k config use-context cluster2
Switched to context "cluster2".

student-node ~ ➜  k get nodes 
NAME                    STATUS   ROLES           AGE   VERSION
cluster2-controlplane   Ready    control-plane   41m   v1.29.0
cluster2-node01         Ready    <none>          40m   v1.29.0



  ```
</details>

You can SSH to all the nodes (of both clusters) from the student-node.


For example:

student-node ~ ➜  ssh cluster1-controlplane
Welcome to Ubuntu 18.04.6 LTS (GNU/Linux 5.4.0-1086-gcp x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.

cluster1-controlplane ~ ➜ 

To get back to the student node, use the logout or exit command, or, hit Control + D

cluster1-controlplane ~ ➜  logout
Connection to cluster1-controlplane closed.

 

<details>
<summary>
Senario -
How is ETCD configured for cluster1?
Remember, you can access the clusters from student-node using the kubectl tool. You can also ssh to the cluster nodes from the student-node.
Make sure to switch the context to cluster1:
kubectl config use-context cluster1

```bash
If you check out the pods running in the kube-system namespace in cluster1, you will notice that etcd is running as a pod:

student-node ~ ➜  kubectl config use-context cluster1
Switched to context "cluster1".

student-node ~ ➜  kubectl get pods -n kube-system | grep etcd
etcd-cluster1-controlplane                      1/1     Running   0              30m

student-node ~ ➜  
This means that ETCD is set up as a Stacked ETCD Topology where the distributed data storage cluster provided by etcd is stacked on top of the cluster formed by the nodes managed by kubeadm that run control plane components.
  ```

How is ETCD configured for cluster2?
Remember, you can access the clusters from student-node using the kubectl tool. You can also ssh to the cluster nodes from the student-node.
Make sure to switch the context to cluster2:
kubectl config use-context cluster2

```bash
If you check out the pods running in the kube-system namespace in cluster2, you will notice that there are NO etcd pods running in this cluster!

student-node ~ ➜  kubectl config use-context cluster2
Switched to context "cluster2".

student-node ~ ➜  kubectl get pods -n kube-system  | grep etcd

student-node ~ ✖
Also, there is NO static pod configuration for etcd under the static pod path:

student-node ~ ➜  ssh cluster2-controlplane
Welcome to Ubuntu 23.10 (GNU/Linux 5.4.0-1106-gcp x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
Last login: Thu Jul 25 19:31:58 2024 from 192.160.244.4

cluster2-controlplane ~ ➜  ls /etc/kubernetes/manifests/ | grep -i etcd

cluster2-controlplane ~ ✖ 
However, if you inspect the process on the controlplane for cluster2, you will see that that the process for the kube-apiserver is referencing an external etcd datastore:

cluster2-controlplane ~ ✖  ps -ef | grep etcd
root        2906    2515  0 19:26 ?        00:01:17 kube-apiserver --advertise-address=192.160.244.12 --allow-privileged=true --authorization-mode=Node,RBAC --client-ca-file=/etc/kubernetes/pki/ca.crt --enable-admission-plugins=NodeRestriction --enable-bootstrap-token-auth=true --etcd-cafile=/etc/kubernetes/pki/etcd/ca.pem --etcd-certfile=/etc/kubernetes/pki/etcd/etcd.pem --etcd-keyfile=/etc/kubernetes/pki/etcd/etcd-key.pem --etcd-servers=https://192.160.244.3:2379 --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key --requestheader-allowed-names=front-proxy-client --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6443 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/etc/kubernetes/pki/sa.pub --service-account-signing-key-file=/etc/kubernetes/pki/sa.key --service-cluster-ip-range=10.96.0.0/12 --tls-cert-file=/etc/kubernetes/pki/apiserver.crt --tls-private-key-file=/etc/kubernetes/pki/apiserver.key
root        9228    8883  0 20:01 pts/0    00:00:00 grep etcd
You can see the same information by inspecting the kube-apiserver pod (which runs as a static pod in the kube-system namespace):

cluster2-controlplane ~ ➜  kubectl -n kube-system describe pod kube-apiserver-cluster2-controlplane 
Name:                 kube-apiserver-cluster2-controlplane
Namespace:            kube-system
Priority:             2000001000
Priority Class Name:  system-node-critical
Node:                 cluster2-controlplane/192.160.244.12
Start Time:           Thu, 25 Jul 2024 19:26:33 +0000
Labels:               component=kube-apiserver
                      tier=control-plane
Annotations:          kubeadm.kubernetes.io/kube-apiserver.advertise-address.endpoint: 192.160.244.12:6443
                      kubernetes.io/config.hash: 469506630571d428e4ddc39d3735a142
                      kubernetes.io/config.mirror: 469506630571d428e4ddc39d3735a142
                      kubernetes.io/config.seen: 2024-07-25T19:26:32.901498317Z
                      kubernetes.io/config.source: file
Status:               Running
SeccompProfile:       RuntimeDefault
IP:                   192.160.244.12
IPs:
  IP:           192.160.244.12
Controlled By:  Node/cluster2-controlplane
Containers:
  kube-apiserver:
    Container ID:  containerd://00e16e0d46214d1bdd969738966c660eb41a0f3a03ef0d2e0d8c009642381f82
    Image:         registry.k8s.io/kube-apiserver:v1.29.0
    Image ID:      registry.k8s.io/kube-apiserver@sha256:921d9d4cda40bd481283375d39d12b24f51281682ae41f6da47f69cb072643bc
    Port:          <none>
    Host Port:     <none>
    Command:
      kube-apiserver
      --advertise-address=192.160.244.12
      --allow-privileged=true
      --authorization-mode=Node,RBAC
      --client-ca-file=/etc/kubernetes/pki/ca.crt
      --enable-admission-plugins=NodeRestriction
      --enable-bootstrap-token-auth=true
      --etcd-cafile=/etc/kubernetes/pki/etcd/ca.pem
      --etcd-certfile=/etc/kubernetes/pki/etcd/etcd.pem
      --etcd-keyfile=/etc/kubernetes/pki/etcd/etcd-key.pem
      --etcd-servers=https://192.160.244.3:2379
--------- End of Snippet---------
  ```
What is the IP address of the External ETCD datastore used in cluster2?

```bash
You can inspect the process on the controlplane node on cluster2 as shown below:

student-node ~ ➜  ssh cluster2-controlplane ps -ef | grep --color=auto etcd
root        2906    2515  0 19:26 ?        00:01:27 kube-apiserver --advertise-address=192.160.244.12 --allow-privileged=true --authorization-mode=Node,RBAC --client-ca-file=/etc/kubernetes/pki/ca.crt --enable-admission-plugins=NodeRestriction --enable-bootstrap-token-auth=true --etcd-cafile=/etc/kubernetes/pki/etcd/ca.pem --etcd-certfile=/etc/kubernetes/pki/etcd/etcd.pem --etcd-keyfile=/etc/kubernetes/pki/etcd/etcd-key.pem --etcd-servers=https://192.160.244.3:2379 --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key --requestheader-allowed-names=front-proxy-client --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6443 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/etc/kubernetes/pki/sa.pub --service-account-signing-key-file=/etc/kubernetes/pki/sa.key --service-cluster-ip-range=10.96.0.0/12 --tls-cert-file=/etc/kubernetes/pki/apiserver.crt --tls-private-key-file=/etc/kubernetes/pki/apiserver.key
Alternatively, inspect the kube-apiserver pod and look at the value used for --etcd-servers
  ```

What is the default data directory used the for ETCD datastore used in cluster1?
Remember, this cluster uses a Stacked ETCD topology.

Make sure to switch the context to cluster1:

kubectl config use-context cluster1

```bash

student-node ~ ➜  kubectl config use-context cluster1
Switched to context "cluster1".


student-node ~ ✖ kubectl -n kube-system describe pod etcd-cluster1-controlplane 
Name:                 etcd-cluster1-controlplane
Namespace:            kube-system
Priority:             2000001000
Priority Class Name:  system-node-critical
Node:                 cluster1-controlplane/192.14.144.9
Start Time:           Wed, 08 Jan 2025 14:36:12 +0000
Labels:               component=etcd
                      tier=control-plane
Annotations:          kubeadm.kubernetes.io/etcd.advertise-client-urls: https://192.14.144.9:2379
                      kubernetes.io/config.hash: e06f6e8b788dddd26c9125c84c702a56
                      kubernetes.io/config.mirror: e06f6e8b788dddd26c9125c84c702a56
                      kubernetes.io/config.seen: 2025-01-08T14:36:11.781216379Z
                      kubernetes.io/config.source: file
Status:               Running
SeccompProfile:       RuntimeDefault
IP:                   192.14.144.9
IPs:
  IP:           192.14.144.9
Controlled By:  Node/cluster1-controlplane
Containers:
  etcd:
    Container ID:  containerd://10a9d15a1c28a6ee9ab09890fe8244849b54a4e23ac38cd7774d6255751b00ab
    Image:         registry.k8s.io/etcd:3.5.10-0
    Image ID:      registry.k8s.io/etcd@sha256:22f892d7672adc0b9c86df67792afdb8b2dc08880f49f669eaaa59c47d7908c2
    Port:          <none>
    Host Port:     <none>
    Command:
      etcd
      --advertise-client-urls=https://192.14.144.9:2379
      --cert-file=/etc/kubernetes/pki/etcd/server.crt
      --client-cert-auth=true
      --data-dir=/var/lib/etcd
      --experimental-initial-corrupt-check=true
      --experimental-watch-progress-notify-interval=5s
      --initial-advertise-peer-urls=https://192.14.144.9:2380
      --initial-cluster=cluster1-controlplane=https://192.14.144.9:2380
      --key-file=/etc/kubernetes/pki/etcd/server.key
      --listen-client-urls=https://127.0.0.1:2379,https://192.14.144.9:2379
      --listen-metrics-urls=http://127.0.0.1:2381
      --listen-peer-urls=https://192.14.144.9:2380
      --name=cluster1-controlplane
      --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
      --peer-client-cert-auth=true
      --peer-key-file=/etc/kubernetes/pki/etcd/peer.key
      --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
      --snapshot-count=10000
      --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
    State:          Running
      Started:      Wed, 08 Jan 2025 14:35:59 +0000
    Ready:          True
    Restart Count:  0
    Requests:
      cpu:        100m
      memory:     100Mi
    Liveness:     http-get http://127.0.0.1:2381/health%3Fexclude=NOSPACE&serializable=true delay=10s timeout=15s period=10s #success=1 #failure=8
    Startup:      http-get http://127.0.0.1:2381/health%3Fserializable=false delay=10s timeout=15s period=10s #success=1 #failure=24
    Environment:  <none>
    Mounts:
      /etc/kubernetes/pki/etcd from etcd-certs (rw)
      /var/lib/etcd from etcd-data (rw)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  etcd-certs:
    Type:          HostPath (bare host directory volume)
    Path:          /etc/kubernetes/pki/etcd
    HostPathType:  DirectoryOrCreate
  etcd-data:
    Type:          HostPath (bare host directory volume)
    Path:          /var/lib/etcd
    HostPathType:  DirectoryOrCreate
QoS Class:         Burstable
Node-Selectors:    <none>
Tolerations:       :NoExecute op=Exists
Events:
  Type    Reason   Age   From     Message
  ----    ------   ----  ----     -------
  Normal  Pulled   50m   kubelet  Container image "registry.k8s.io/etcd:3.5.10-0" already present on machine
  Normal  Created  50m   kubelet  Created container etcd
  Normal  Started  50m   kubelet  Started container etcd

student-node ~ ➜  kubectl -n kube-system describe pod etcd-cluster1-controlplane | grep data-dir
      --data-dir=/var/lib/etcd


  ```

For the subsequent questions, you would need to login to the External ETCD server.
To do this, open a new terminal (using the + button located above the default terminal).
From the new terminal you can now SSH from the student-node to either the IP of the ETCD datastore (that you identified in the previous questions) OR the hostname etcd-server:

What is the default data directory used the for ETCD datastore used in cluster2?
Remember, this cluster uses an External ETCD topology.

```bash
SSH to the etcd-server and inspect the etcd process as shown below:

etcd-server ~ ➜  ps -ef | grep --color=auto etcd
etcd         853       1  0 19:25 ?        00:00:45 /usr/local/bin/etcd --name etcd-server --data-dir=/var/lib/etcd-data --cert-file=/etc/etcd/pki/etcd.pem --key-file=/etc/etcd/pki/etcd-key.pem --peer-cert-file=/etc/etcd/pki/etcd.pem --peer-key-file=/etc/etcd/pki/etcd-key.pem --trusted-ca-file=/etc/etcd/pki/ca.pem --peer-trusted-ca-file=/etc/etcd/pki/ca.pem --peer-client-cert-auth --client-cert-auth --initial-advertise-peer-urls https://192.160.244.3:2380 --listen-peer-urls https://192.160.244.3:2380 --advertise-client-urls https://192.160.244.3:2379 --listen-client-urls https://192.160.244.3:2379,https://127.0.0.1:2379 --initial-cluster-token etcd-cluster-1 --initial-cluster etcd-server=https://192.160.244.3:2380 --initial-cluster-state new
root        1065     985  0 20:13 pts/0    00:00:00 grep --color=auto etcd

etcd-server ~ ➜  
Look at the value of --data-dir

  ```
How many nodes are part of the ETCD cluster that etcd-server is a part of?


```bash
Check the members of the cluster:

etcd-server ~ ➜  ETCDCTL_API=3 etcdctl \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/etcd/pki/ca.pem \
  --cert=/etc/etcd/pki/etcd.pem \
  --key=/etc/etcd/pki/etcd-key.pem \
   member list
59ee55985632d394, started, etcd-server, https://192.160.244.3:2380, https://192.160.244.3:2379, false

etcd-server ~ ➜  
This shows that there is only one member in this cluster.
  ```
Take a backup of etcd on cluster1 and save it on the student-node at the path /opt/cluster1.db



If needed, make sure to set the context to cluster1 (on the student-node):

student-node ~ ➜  kubectl config use-context cluster1
Switched to context "cluster1".

```bash
On the student-node:

First set the context to cluster1:
student-node ~ ➜  kubectl config use-context cluster1
Switched to context "cluster1".
Next, inspect the endpoints and certificates used by the etcd pod. We will make use of these to take the backup.

student-node ~ ✖ kubectl describe  pods -n kube-system etcd-cluster1-controlplane  | grep advertise-client-urls
      --advertise-client-urls=https://192.160.244.10:2379

student-node ~ ➜  

student-node ~ ➜  kubectl describe  pods -n kube-system etcd-cluster1-controlplane  | grep pki
      --cert-file=/etc/kubernetes/pki/etcd/server.crt
      --key-file=/etc/kubernetes/pki/etcd/server.key
      --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
      --peer-key-file=/etc/kubernetes/pki/etcd/peer.key
      --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
      --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
      /etc/kubernetes/pki/etcd from etcd-certs (rw)
    Path:          /etc/kubernetes/pki/etcd

student-node ~ ➜
NOTE: The IP address (192.160.244.10) shown in the above command could be different in your lab environment. Make sure to note the correct IP address before taking the backup of the cluster.

SSH to the controlplane node of cluster1 and then take the backup using the endpoints and certificates we identified above:

cluster1-controlplane ~ ➜  ETCDCTL_API=3 etcdctl --endpoints=https://192.160.244.10:2379 --cacert=/etc/kubernetes/pki/etcd/ca.crt --cert=/etc/kubernetes/pki/etcd/server.crt --key=/etc/kubernetes/pki/etcd/server.key snapshot save /opt/cluster1.db
Snapshot saved at /opt/cluster1.db

cluster1-controlplane ~ ➜  
Finally, copy the backup to the student-node. To do this, go back to the student-node and use scp as shown below:

student-node ~ ➜  scp cluster1-controlplane:/opt/cluster1.db /opt
cluster1.db                                                                                                        100% 2088KB 112.3MB/s   00:00    

student-node ~ ➜

  ```
An ETCD backup for cluster2 is stored at /opt/cluster2.db. Use this snapshot file to carryout a restore on cluster2 to a new path /var/lib/etcd-data-new.



Once the restore is complete, ensure that the controlplane components on cluster2 are running.


The snapshot was taken when there were objects created in the critical namespace on cluster2. These objects should be available post restore.


If needed, make sure to set the context to cluster2 (on the student-node):

student-node ~ ➜  kubectl config use-context cluster2
Switched to context "cluster2".

student-node ~ ➜  

```bash
Step 1. Copy the snapshot file from the student-node to the etcd-server. In the example below, we are copying it to the /root directory:

student-node ~  scp /opt/cluster2.db etcd-server:/root
cluster2.db                                                                                                        100% 1108KB 178.5MB/s   00:00    

student-node ~ ➜
Step 2: Restore the snapshot on the cluster2. Since we are restoring directly on the etcd-server, we can use the endpoint https:/127.0.0.1. Use the same certificates that were identified earlier. Make sure to use the data-dir as /var/lib/etcd-data-new:

etcd-server ~ ➜  ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 --cacert=/etc/etcd/pki/ca.pem --cert=/etc/etcd/pki/etcd.pem --key=/etc/etcd/pki/etcd-key.pem snapshot restore /root/cluster2.db --data-dir /var/lib/etcd-data-new
{"level":"info","ts":1721940922.0441437,"caller":"snapshot/v3_snapshot.go:296","msg":"restoring snapshot","path":"/root/cluster2.db","wal-dir":"/var/lib/etcd-data-new/member/wal","data-dir":"/var/lib/etcd-data-new","snap-dir":"/var/lib/etcd-data-new/member/snap"}
{"level":"info","ts":1721940922.060755,"caller":"mvcc/kvstore.go:388","msg":"restored last compact revision","meta-bucket-name":"meta","meta-bucket-name-key":"finishedCompactRev","restored-compact-revision":951}
{"level":"info","ts":1721940922.0667593,"caller":"membership/cluster.go:392","msg":"added member","cluster-id":"cdf818194e3a8c32","local-member-id":"0","added-peer-id":"8e9e05c52164694d","added-peer-peer-urls":["http://localhost:2380"]}
{"level":"info","ts":1721940922.0732546,"caller":"snapshot/v3_snapshot.go:309","msg":"restored snapshot","path":"/root/cluster2.db","wal-dir":"/var/lib/etcd-data-new/member/wal","data-dir":"/var/lib/etcd-data-new","snap-dir":"/var/lib/etcd-data-new/member/snap"}

etcd-server ~ ➜
Step 3: Update the systemd service unit file for etcd by running vi /etc/systemd/system/etcd.service and add the new value for data-dir:

[Unit]
Description=etcd key-value store
Documentation=https://github.com/etcd-io/etcd
After=network.target

[Service]
User=etcd
Type=notify
ExecStart=/usr/local/bin/etcd \
  --name etcd-server \
  --data-dir=/var/lib/etcd-data-new \
---End of Snippet---
Step 4: make sure the permissions on the new directory is correct (should be owned by etcd user):

etcd-server /var/lib ➜  chown -R etcd:etcd /var/lib/etcd-data-new

etcd-server /var/lib ➜ 


etcd-server /var/lib ➜  ls -ld /var/lib/etcd-data-new/
drwx------ 3 etcd etcd 4096 Jul 15 20:55 /var/lib/etcd-data-new/
etcd-server /var/lib ➜
Step 5: Finally, reload and restart the etcd service.

etcd-server ~ ➜  systemctl daemon-reload 
etcd-server ~ ➜  systemctl restart etcd
etcd-server ~ ➜  
Step 6 (optional): It is recommended to restart controlplane components (e.g. kube-scheduler, kube-controller-manager, kubelet) to ensure that they don't rely on some stale data.
  ```

</summary>

