

<details>
<summary>
Senario -
Manually schedule the pod on node01.
</summary>

```bash
To list and know the names of available nodes on the cluster:

$ kubectl get nodes
Add the nodeName field under the spec section in the nginx.yaml file with node01 as the value:

---
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeName: node01
  containers:
  -  image: nginx
     name: nginx
Then run the command kubectl create -f nginx.yaml to create a pod from the definition file.

To check the status of a nginx pod and to know the node name: 

$ kubectl get pods -o wide

NAME    READY   STATUS    RESTARTS   AGE   IP           NODE     NOMINATED NODE   READINESS GATES
nginx   1/1     Running   0          29s   172.17.1.2   node01   <none>           <none>

  ```
</details>



<details>
<summary>
Senario -
We have deployed a number of PODs. They are labelled with tier, env and bu. How many PODs exist in the dev environment (env)?

Use selectors to filter the output
</summary>

```bash

controlplane ~ ➜  k get po --show-labels | grep dev
app-1-mtnzm   1/1     Running   0          9m33s   bu=finance,env=dev,tier=frontend
app-1-rvrpd   1/1     Running   0          9m33s   bu=finance,env=dev,tier=frontend
app-1-tjw2v   1/1     Running   0          9m33s   bu=finance,env=dev,tier=frontend
db-1-bpss4    1/1     Running   0          9m33s   env=dev,tier=db
db-1-dwz7z    1/1     Running   0          9m33s   env=dev,tier=db
db-1-qdh2l    1/1     Running   0          9m33s   env=dev,tier=db
db-1-rvstl    1/1     Running   0          9m33s   env=dev,tier=db

controlplane ~ ✖ k get po --show-labels | grep dev | wc 
        7        42       520
  ```
</details>



<details>
<summary>
Senario -
How many PODs are in the finance business unit (bu)?
</summary>

```bash
controlplane ~ ➜  k get po --show-labels | grep finance | wc 
        6        36       475

controlplane ~ ➜  k get po --show-labels 
NAME          READY   STATUS    RESTARTS   AGE   LABELS
app-1-mtnzm   1/1     Running   0          11m   bu=finance,env=dev,tier=frontend
app-1-rvrpd   1/1     Running   0          11m   bu=finance,env=dev,tier=frontend
app-1-tjw2v   1/1     Running   0          11m   bu=finance,env=dev,tier=frontend
app-1-zzxdf   1/1     Running   0          11m   bu=finance,env=prod,tier=frontend
app-2-7f9zz   1/1     Running   0          11m   env=prod,tier=frontend
auth          1/1     Running   0          11m   bu=finance,env=prod
db-1-bpss4    1/1     Running   0          11m   env=dev,tier=db
db-1-dwz7z    1/1     Running   0          11m   env=dev,tier=db
db-1-qdh2l    1/1     Running   0          11m   env=dev,tier=db
db-1-rvstl    1/1     Running   0          11m   env=dev,tier=db
db-2-s8r94    1/1     Running   0          11m   bu=finance,env=prod,tier=db

  ```
</details>




<details>
<summary>
Senario -
How many objects are in the prod environment including PODs, ReplicaSets and any other objects?
</summary>

```bash

controlplane ~ ➜  k get all  --show-labels | grep prod
pod/app-1-zzxdf   1/1     Running   0          12m   bu=finance,env=prod,tier=frontend
pod/app-2-7f9zz   1/1     Running   0          12m   env=prod,tier=frontend
pod/auth          1/1     Running   0          12m   bu=finance,env=prod
pod/db-2-s8r94    1/1     Running   0          12m   bu=finance,env=prod,tier=db
service/app-1        ClusterIP   10.43.252.19   <none>        3306/TCP   12m   bu=finance,env=prod
replicaset.apps/app-2   1         1         1       12m   env=prod
replicaset.apps/db-2    1         1         1       12m   env=prod

controlplane ~ ➜  k get all  --show-labels | grep prod | wc
        7        43       550

controlplane ~ ➜  kubectl get all --selector env=prod --no-headers | wc -l
7

  ```
</details>




<details>
<summary>
Senario -
Identify the POD which is part of the prod environment, the finance BU and of frontend tier?
</summary>

```bash
controlplane ~ ➜  k get po --selector bu=finance,env=prod,tier=frontend
NAME          READY   STATUS    RESTARTS   AGE
app-1-zzxdf   1/1     Running   0          15m
  ```
</details>




<details>
<summary>
Senario -
A ReplicaSet definition file is given replicaset-definition-1.yaml. Attempt to create the replicaset; you will encounter an issue with the file. Try to fix it.

Once you fix the issue, create the replicaset from the definition file.
```bash
apiVersion: apps/v1
kind: ReplicaSet
metadata:
   name: replicaset-1
spec:
   replicas: 2
   selector:
      matchLabels:
        tier: front-end
   template:
     metadata:
       labels:
        tier: nginx
     spec:
       containers:
       - name: nginx
         image: nginx
  ```

</summary>

```bash

controlplane ~ ➜  k apply -f replicaset-definition-1.yaml 
The ReplicaSet "replicaset-1" is invalid: spec.template.metadata.labels: Invalid value: map[string]string{"tier":"nginx"}: `selector` does not match template `labels`

apiVersion: apps/v1
kind: ReplicaSet
metadata:
   name: replicaset-1
spec:
   replicas: 2
   selector:
      matchLabels:
        tier: nginx
   template:
     metadata:
       labels:
        tier: nginx
     spec:
       containers:
       - name: nginx
         image: nginx
  ```

</details>




<details>
<summary>
Senario -
How many nodes exist on the system?
Including the controlplane node.
</summary>

```bash

controlplane ~ ➜  k get nodes
NAME           STATUS   ROLES           AGE     VERSION
controlplane   Ready    control-plane   10m     v1.31.0
node01         Ready    <none>          9m49s   v1.31.0

controlplane ~ ➜  k get nodes -o wide
NAME           STATUS   ROLES           AGE     VERSION   INTERNAL-IP       EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION    CONTAINER-RUNTIME
controlplane   Ready    control-plane   10m     v1.31.0   192.168.233.141   <none>        Ubuntu 22.04.4 LTS   5.15.0-1072-gcp   containerd://1.6.26
node01         Ready    <none>          9m57s   v1.31.0   192.168.212.149   <none>        Ubuntu 22.04.4 LTS   5.15.0-1070-gcp   containerd://1.6.26
  ```
</details>




<details>
<summary>
Senario -

Do any taints exist on node01 node?
</summary>

```bash

controlplane ~ ✖ k describe nodes node01 | grep Taint
Taints:             <none>

controlplane ~ ➜  k describe nodes node01
Name:               node01
Roles:              <none>
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=node01
                    kubernetes.io/os=linux
Annotations:        flannel.alpha.coreos.com/backend-data: {"VNI":1,"VtepMAC":"b2:94:ef:49:0c:e8"}
                    flannel.alpha.coreos.com/backend-type: vxlan
                    flannel.alpha.coreos.com/kube-subnet-manager: true
                    flannel.alpha.coreos.com/public-ip: 192.168.212.149
                    kubeadm.alpha.kubernetes.io/cri-socket: unix:///var/run/containerd/containerd.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Sun, 05 Jan 2025 07:58:29 +0000
Taints:             <none>
Unschedulable:      false
Lease:
  HolderIdentity:  node01
  AcquireTime:     <unset>
  RenewTime:       Sun, 05 Jan 2025 08:09:11 +0000
Conditions:
  Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----                 ------  -----------------                 ------------------                ------                       -------
  NetworkUnavailable   False   Sun, 05 Jan 2025 07:58:34 +0000   Sun, 05 Jan 2025 07:58:34 +0000   FlannelIsUp                  Flannel is running on this node
  MemoryPressure       False   Sun, 05 Jan 2025 08:09:11 +0000   Sun, 05 Jan 2025 07:58:29 +0000   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure         False   Sun, 05 Jan 2025 08:09:11 +0000   Sun, 05 Jan 2025 07:58:29 +0000   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure          False   Sun, 05 Jan 2025 08:09:11 +0000   Sun, 05 Jan 2025 07:58:29 +0000   KubeletHasSufficientPID      kubelet has sufficient PID available
  Ready                True    Sun, 05 Jan 2025 08:09:11 +0000   Sun, 05 Jan 2025 07:58:32 +0000   KubeletReady                 kubelet is posting ready status
Addresses:
  InternalIP:  192.168.212.149
  Hostname:    node01
Capacity:
  cpu:                16
  ephemeral-storage:  772706776Ki
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             65838240Ki
  pods:               110
Allocatable:
  cpu:                16
  ephemeral-storage:  712126563583
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             65735840Ki
  pods:               110
System Info:
  Machine ID:                 132e3d2451f947fe9214456160254717
  System UUID:                88c76b9b-79b9-2d71-8083-33391e6544cc
  Boot ID:                    008a3584-ebad-4e9b-b115-08f1216fa36a
  Kernel Version:             5.15.0-1070-gcp
  OS Image:                   Ubuntu 22.04.4 LTS
  Operating System:           linux
  Architecture:               amd64
  Container Runtime Version:  containerd://1.6.26
  Kubelet Version:            v1.31.0
  Kube-Proxy Version:         
PodCIDR:                      172.17.1.0/24
PodCIDRs:                     172.17.1.0/24
Non-terminated Pods:          (2 in total)
  Namespace                   Name                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  Age
  ---------                   ----                     ------------  ----------  ---------------  -------------  ---
  kube-flannel                kube-flannel-ds-vtctz    100m (0%)     0 (0%)      50Mi (0%)        0 (0%)         10m
  kube-system                 kube-proxy-dlzsd         0 (0%)        0 (0%)      0 (0%)           0 (0%)         10m
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource           Requests   Limits
  --------           --------   ------
  cpu                100m (0%)  0 (0%)
  memory             50Mi (0%)  0 (0%)
  ephemeral-storage  0 (0%)     0 (0%)
  hugepages-1Gi      0 (0%)     0 (0%)
  hugepages-2Mi      0 (0%)     0 (0%)
Events:
  Type     Reason                   Age                From             Message
  ----     ------                   ----               ----             -------
  Normal   Starting                 10m                kube-proxy       
  Normal   Starting                 10m                kubelet          Starting kubelet.
  Warning  InvalidDiskCapacity      10m                kubelet          invalid capacity 0 on image filesystem
  Warning  CgroupV1                 10m                kubelet          Cgroup v1 support is in maintenance mode, please migrate to Cgroup v2.
  Normal   NodeAllocatableEnforced  10m                kubelet          Updated Node Allocatable limit across pods
  Normal   NodeHasSufficientMemory  10m (x2 over 10m)  kubelet          Node node01 status is now: NodeHasSufficientMemory
  Normal   NodeHasNoDiskPressure    10m (x2 over 10m)  kubelet          Node node01 status is now: NodeHasNoDiskPressure
  Normal   NodeHasSufficientPID     10m (x2 over 10m)  kubelet          Node node01 status is now: NodeHasSufficientPID
  Normal   RegisteredNode           10m                node-controller  Node node01 event: Registered Node node01 in Controller
  Normal   NodeReady                10m                kubelet          Node node01 status is now: NodeReady

  ```
</details>




<details>
<summary>
Senario -
Create a taint on node01 with key of spray, value of mortein and effect of NoSchedule
</summary>

```bash

Examples:
  # Update node 'foo' with a taint with key 'dedicated' and value 'special-user'
and effect 'NoSchedule'
  # If a taint with that key and effect already exists, its value is replaced as
specified
  kubectl taint nodes foo dedicated=special-user:NoSchedule
  
  # Remove from node 'foo' the taint with key 'dedicated' and effect
'NoSchedule' if one exists
  kubectl taint nodes foo dedicated:NoSchedule-
  
  # Remove from node 'foo' all the taints with key 'dedicated'
  kubectl taint nodes foo dedicated-
  
  # Add a taint with key 'dedicated' on nodes having label myLabel=X
  kubectl taint node -l myLabel=X  dedicated=foo:PreferNoSchedule
  
  # Add to node 'foo' a taint with key 'bar' and no value
  kubectl taint nodes foo bar:NoSchedule


controlplane ~ ✖ k taint node node01 spray=mortein:NoSchedule
node/node01 tainted

controlplane ~ ➜  k describe nodes node01 | grep Taint
Taints:             spray=mortein:NoSchedule

  ```
</details>




<details>
<summary>
Senario -
Create  pod named bee with the nginx image, which has a toleration set to the taint mortein.
</summary>

```bash

Solution manifest file to create a pod called bee as follows:

---
apiVersion: v1
kind: Pod
metadata:
  name: bee
spec:
  containers:
  - image: nginx
    name: bee
  tolerations:
  - key: spray
    value: mortein
    effect: NoSchedule
    operator: Equal
then run the kubectl create -f <FILE-NAME>.yaml to create a pod.


controlplane ~ ➜  k get po
NAME   READY   STATUS    RESTARTS   AGE
bee    1/1     Running   0          13s

controlplane ~ ➜  k describe po
Name:             bee
Namespace:        default
Priority:         0
Service Account:  default
Node:             node01/192.168.139.34
Start Time:       Sun, 05 Jan 2025 14:01:31 +0000
Labels:           <none>
Annotations:      <none>
Status:           Running
IP:               172.17.1.2
IPs:
  IP:  172.17.1.2
Containers:
  bee:
    Container ID:   containerd://e5ce828ce7e6d57f5171c1ed32ed67a06777bf91a6955970b6159c5570e1b02b
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:42e917aaa1b5bb40dd0f6f7f4f857490ac7747d7ef73b391c774a41a8b994f15
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Sun, 05 Jan 2025 14:01:37 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-4nlv4 (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kube-api-access-4nlv4:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
                             spray=mortein:NoSchedule
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  19s   default-scheduler  Successfully assigned default/bee to node01
  Normal  Pulling    18s   kubelet            Pulling image "nginx"
  Normal  Pulled     13s   kubelet            Successfully pulled image "nginx" in 4.945s (4.945s including waiting). Image size: 72099410 bytes.
  Normal  Created    13s   kubelet            Created container bee
  Normal  Started    13s   kubelet            Started container bee

  ```
</details>




<details>
<summary>
Senario -
Remove the taint on controlplane, which currently has the taint effect of NoSchedule.
</summary>

```bash

controlplane ~ ➜  k describe nodes controlplane 
Name:               controlplane
Roles:              control-plane
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=controlplane
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/control-plane=
                    node.kubernetes.io/exclude-from-external-load-balancers=
Annotations:        flannel.alpha.coreos.com/backend-data: {"VNI":1,"VtepMAC":"f6:f4:b8:35:95:bc"}
                    flannel.alpha.coreos.com/backend-type: vxlan
                    flannel.alpha.coreos.com/kube-subnet-manager: true
                    flannel.alpha.coreos.com/public-ip: 192.168.231.165
                    kubeadm.alpha.kubernetes.io/cri-socket: unix:///var/run/containerd/containerd.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Sun, 05 Jan 2025 13:45:07 +0000
Taints:             node-role.kubernetes.io/control-plane:NoSchedule
Unschedulable:      false
Lease:
  HolderIdentity:  controlplane
  AcquireTime:     <unset>
  RenewTime:       Sun, 05 Jan 2025 14:02:30 +0000
Conditions:
  Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----                 ------  -----------------                 ------------------                ------                       -------
  NetworkUnavailable   False   Sun, 05 Jan 2025 13:45:19 +0000   Sun, 05 Jan 2025 13:45:19 +0000   FlannelIsUp                  Flannel is running on this node
  MemoryPressure       False   Sun, 05 Jan 2025 14:00:39 +0000   Sun, 05 Jan 2025 13:45:07 +0000   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure         False   Sun, 05 Jan 2025 14:00:39 +0000   Sun, 05 Jan 2025 13:45:07 +0000   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure          False   Sun, 05 Jan 2025 14:00:39 +0000   Sun, 05 Jan 2025 13:45:07 +0000   KubeletHasSufficientPID      kubelet has sufficient PID available
  Ready                True    Sun, 05 Jan 2025 14:00:39 +0000   Sun, 05 Jan 2025 13:45:17 +0000   KubeletReady                 kubelet is posting ready status
Addresses:
  InternalIP:  192.168.231.165
  Hostname:    controlplane
Capacity:
  cpu:                16
  ephemeral-storage:  772706776Ki
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             65838224Ki
  pods:               110
Allocatable:
  cpu:                16
  ephemeral-storage:  712126563583
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             65735824Ki
  pods:               110
System Info:
  Machine ID:                 132e3d2451f947fe9214456160254717
  System UUID:                72d44a52-a6e1-25cf-be5c-7bcca782f11e
  Boot ID:                    7a08d459-bd52-4c58-95c1-486be4478f9f
  Kernel Version:             5.15.0-1071-gcp
  OS Image:                   Ubuntu 22.04.4 LTS
  Operating System:           linux
  Architecture:               amd64
  Container Runtime Version:  containerd://1.6.26
  Kubelet Version:            v1.31.0
  Kube-Proxy Version:         
PodCIDR:                      172.17.0.0/24
PodCIDRs:                     172.17.0.0/24
Non-terminated Pods:          (8 in total)
  Namespace                   Name                                    CPU Requests  CPU Limits  Memory Requests  Memory Limits  Age
  ---------                   ----                                    ------------  ----------  ---------------  -------------  ---
  kube-flannel                kube-flannel-ds-v22vx                   100m (0%)     0 (0%)      50Mi (0%)        0 (0%)         17m
  kube-system                 coredns-77d6fd4654-gz57p                100m (0%)     0 (0%)      70Mi (0%)        170Mi (0%)     17m
  kube-system                 coredns-77d6fd4654-sztbp                100m (0%)     0 (0%)      70Mi (0%)        170Mi (0%)     17m
  kube-system                 etcd-controlplane                       100m (0%)     0 (0%)      100Mi (0%)       0 (0%)         17m
  kube-system                 kube-apiserver-controlplane             250m (1%)     0 (0%)      0 (0%)           0 (0%)         17m
  kube-system                 kube-controller-manager-controlplane    200m (1%)     0 (0%)      0 (0%)           0 (0%)         17m
  kube-system                 kube-proxy-88p9n                        0 (0%)        0 (0%)      0 (0%)           0 (0%)         17m
  kube-system                 kube-scheduler-controlplane             100m (0%)     0 (0%)      0 (0%)           0 (0%)         17m
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
  Normal   Starting                  17m                kube-proxy       
  Normal   NodeHasSufficientMemory   17m (x7 over 17m)  kubelet          Node controlplane status is now: NodeHasSufficientMemory
  Normal   NodeHasNoDiskPressure     17m (x7 over 17m)  kubelet          Node controlplane status is now: NodeHasNoDiskPressure
  Normal   NodeHasSufficientPID      17m (x6 over 17m)  kubelet          Node controlplane status is now: NodeHasSufficientPID
  Warning  CheckLimitsForResolvConf  17m                kubelet          open /run/systemd/resolve/resolv.conf: no such file or directory
  Normal   NodeAllocatableEnforced   17m                kubelet          Updated Node Allocatable limit across pods
  Normal   Starting                  17m                kubelet          Starting kubelet.
  Warning  CgroupV1                  17m                kubelet          Cgroup v1 support is in maintenance mode, please migrate to Cgroup v2.
  Warning  InvalidDiskCapacity       17m                kubelet          invalid capacity 0 on image filesystem
  Normal   NodeAllocatableEnforced   17m                kubelet          Updated Node Allocatable limit across pods
  Normal   NodeHasSufficientMemory   17m                kubelet          Node controlplane status is now: NodeHasSufficientMemory
  Normal   NodeHasNoDiskPressure     17m                kubelet          Node controlplane status is now: NodeHasNoDiskPressure
  Normal   NodeHasSufficientPID      17m                kubelet          Node controlplane status is now: NodeHasSufficientPID
  Normal   RegisteredNode            17m                node-controller  Node controlplane event: Registered Node controlplane in Controller
  Normal   NodeReady                 17m                kubelet          Node controlplane status is now: NodeReady


controlplane ~ ✖ k taint node controlplane node-role.kubernetes.io/control-plane:NoSchedule-
node/controlplane untainted
  ```
</details>


<details>
<summary>
Senario -
How many Labels exist on node node01?

```bash
controlplane ~ ➜  k get nodes --show-labels 
NAME           STATUS   ROLES           AGE     VERSION   LABELS
controlplane   Ready    control-plane   5m14s   v1.31.0   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/arch=amd64,kubernetes.io/hostname=controlplane,kubernetes.io/os=linux,node-role.kubernetes.io/control-plane=,node.kubernetes.io/exclude-from-external-load-balancers=
node01         Ready    <none>          4m47s   v1.31.0   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/arch=amd64,kubernetes.io/hostname=node01,kubernetes.io/os=linux

  ```
</summary>

```bash
controlpl

amples:
  # Update pod 'foo' with the label 'unhealthy' and the value 'true'
  kubectl label pods foo unhealthy=true
  
  # Update pod 'foo' with the label 'status' and the value 'unhealthy',
overwriting any existing value
  kubectl label --overwrite pods foo status=unhealthy
  
  # Update all pods in the namespace
  kubectl label pods --all status=unhealthy
  
  # Update a pod identified by the type and name in "pod.json"
  kubectl label -f pod.json status=unhealthy
  
  # Update pod 'foo' only if the resource is unchanged from version 1
  kubectl label pods foo status=unhealthy --resource-version=1
  
  # Update pod 'foo' by removing a label named 'bar' if it exists
  # Does not require the --overwrite flag
  kubectl label pods foo bar-
  
controlplane ~ ➜  kubectl label nodes node01 color=blue
node/node01 labeled

controlplane ~ ➜  k get nodes --show-labels  | grep node01
node01         Ready    <none>          9m16s   v1.31.0   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,color=blue,kubernetes.io/arch=amd64,kubernetes.io/hostname=node01,kubernetes.io/os=linux


  ```
</details>




<details>
<summary>
Senario -
Create a new deployment named blue with the nginx image and 3 replicas.
</summary>

```bash

controlplane ~ ➜  k create deployment blue --image nginx --dry-run -o yaml
W0105 14:18:06.056121   12275 helpers.go:703] --dry-run is deprecated and can be replaced with --dry-run=client.
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: blue
  name: blue
spec:
  replicas: 1
  selector:
    matchLabels:
      app: blue
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: blue
    spec:
      containers:
      - image: nginx
        name: nginx
        resources: {}
status: {}

  ```
</details>




<details>
<summary>
Senario -
Set Node Affinity to the deployment to place the pods on node01 only.
Name: blue
Replicas: 3
Image: nginx
NodeAffinity: requiredDuringSchedulingIgnoredDuringExecution
Key: color
value: blue
</summary>

```bash
apiVersion: apps/v1
kind: Deployment
metadata: 
  name : blue
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
       containers:
          - image : nginx
            name: nginx
       affinity:
         nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
             - matchExpressions:
                - key: color
                  operator: In
                  values:
                    - blue

controlplane ~ ➜  k apply -f deploy.yaml 
deployment.apps/blue configured

controlplane ~ ➜  k describe po blue-89b5fbf47-
Name:             blue-89b5fbf47-k85x8
Namespace:        default
Priority:         0
Service Account:  default
Node:             node01/192.168.139.39
Start Time:       Sun, 05 Jan 2025 14:29:07 +0000
Labels:           app=nginx
                  pod-template-hash=89b5fbf47
Annotations:      <none>
Status:           Running
IP:               172.17.1.4
IPs:
  IP:           172.17.1.4
Controlled By:  ReplicaSet/blue-89b5fbf47
Containers:
  nginx:
    Container ID:   containerd://d78c256df1dacb91a665323170b0d11cfbccfab3c70642a24fcae2f619047154
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:42e917aaa1b5bb40dd0f6f7f4f857490ac7747d7ef73b391c774a41a8b994f15
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Sun, 05 Jan 2025 14:29:08 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-r484q (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kube-api-access-r484q:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  12s   default-scheduler  Successfully assigned default/blue-89b5fbf47-k85x8 to node01
  Normal  Pulling    11s   kubelet            Pulling image "nginx"
  Normal  Pulled     11s   kubelet            Successfully pulled image "nginx" in 150ms (150ms including waiting). Image size: 72099410 bytes.
  Normal  Created    11s   kubelet            Created container nginx
  Normal  Started    11s   kubelet            Started container nginx

  ```
</details>






<details>
<summary>
Senario -

</summary>

```bash

controlplane ~ ➜  cat new.yaml 

apiVersion: apps/v1
kind: Deployment
metadata:
  name: red
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - image: nginx
          name: nginx
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
             nodeSelectorTerms:
             - matchExpressions:
                - key: node-role.kubernetes.io/control-plane
                  operator: Exists

controlplane ~ ➜  k apply -f new.yaml 
deployment.apps/red configured


  ```
</details>




<details>
<summary>
Senario -
How many DaemonSets are created in the cluster in all namespaces?
</summary>

```bash

controlplane ~ ➜  k get all -A
NAMESPACE      NAME                                       READY   STATUS    RESTARTS   AGE
kube-flannel   pod/kube-flannel-ds-j8z9x                  1/1     Running   0          5m1s
kube-system    pod/coredns-77d6fd4654-skdck               1/1     Running   0          5m1s
kube-system    pod/coredns-77d6fd4654-ts7f8               1/1     Running   0          5m1s
kube-system    pod/etcd-controlplane                      1/1     Running   0          5m8s
kube-system    pod/kube-apiserver-controlplane            1/1     Running   0          5m8s
kube-system    pod/kube-controller-manager-controlplane   1/1     Running   0          5m8s
kube-system    pod/kube-proxy-dzjvq                       1/1     Running   0          5m1s
kube-system    pod/kube-scheduler-controlplane            1/1     Running   0          5m8s

NAMESPACE     NAME                 TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)                  AGE
default       service/kubernetes   ClusterIP   172.20.0.1    <none>        443/TCP                  5m10s
kube-system   service/kube-dns     ClusterIP   172.20.0.10   <none>        53/UDP,53/TCP,9153/TCP   5m9s

NAMESPACE      NAME                             DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
kube-flannel   daemonset.apps/kube-flannel-ds   1         1         1       1            1           <none>                   5m8s
kube-system    daemonset.apps/kube-proxy        1         1         1       1            1           kubernetes.io/os=linux   5m9s

NAMESPACE     NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
kube-system   deployment.apps/coredns   2/2     2            2           5m9s

NAMESPACE     NAME                                 DESIRED   CURRENT   READY   AGE
kube-system   replicaset.apps/coredns-77d6fd4654   2         2         2       5m2s

  ```
</details>




<details>
<summary>
Senario -
Which namespace is the kube-proxy Daemonset created in?
</summary>

```bash

controlplane ~ ➜  k get all -A
NAMESPACE      NAME                                       READY   STATUS    RESTARTS   AGE
kube-flannel   pod/kube-flannel-ds-j8z9x                  1/1     Running   0          8m40s
kube-system    pod/coredns-77d6fd4654-skdck               1/1     Running   0          8m40s
kube-system    pod/coredns-77d6fd4654-ts7f8               1/1     Running   0          8m40s
kube-system    pod/etcd-controlplane                      1/1     Running   0          8m47s
kube-system    pod/kube-apiserver-controlplane            1/1     Running   0          8m47s
kube-system    pod/kube-controller-manager-controlplane   1/1     Running   0          8m47s
kube-system    pod/kube-proxy-dzjvq                       1/1     Running   0          8m40s
kube-system    pod/kube-scheduler-controlplane            1/1     Running   0          8m47s

NAMESPACE     NAME                 TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)                  AGE
default       service/kubernetes   ClusterIP   172.20.0.1    <none>        443/TCP                  8m49s
kube-system   service/kube-dns     ClusterIP   172.20.0.10   <none>        53/UDP,53/TCP,9153/TCP   8m48s

NAMESPACE      NAME                             DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
kube-flannel   daemonset.apps/kube-flannel-ds   1         1         1       1            1           <none>                   8m47s
kube-system    daemonset.apps/kube-proxy        1         1         1       1            1           kubernetes.io/os=linux   8m48s

NAMESPACE     NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
kube-system   deployment.apps/coredns   2/2     2            2           8m48s

NAMESPACE     NAME                                 DESIRED   CURRENT   READY   AGE
kube-system   replicaset.apps/coredns-77d6fd4654   2         2         2       8m41s

  ```
</details>






<details>
<summary>
Senario -
What is the image used by the POD deployed by the kube-flannel-ds DaemonSet?
</summary>

```bash

controlplane ~ ➜  k describe daemonsets.apps kube-flannel-ds -n kube-flannel
Name:           kube-flannel-ds
Selector:       app=flannel,k8s-app=flannel
Node-Selector:  <none>
Labels:         app=flannel
                k8s-app=flannel
                tier=node
Annotations:    deprecated.daemonset.template.generation: 1
Desired Number of Nodes Scheduled: 1
Current Number of Nodes Scheduled: 1
Number of Nodes Scheduled with Up-to-date Pods: 1
Number of Nodes Scheduled with Available Pods: 1
Number of Nodes Misscheduled: 0
Pods Status:  1 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:           app=flannel
                    k8s-app=flannel
                    tier=node
  Service Account:  flannel
  Init Containers:
   install-cni-plugin:
    Image:      docker.io/flannel/flannel-cni-plugin:v1.2.0
    Port:       <none>
    Host Port:  <none>
    Command:
      cp
    Args:
      -f
      /flannel
      /opt/cni/bin/flannel
    Environment:  <none>
    Mounts:
      /opt/cni/bin from cni-plugin (rw)
   install-cni:
    Image:      docker.io/flannel/flannel:v0.23.0
    Port:       <none>
    Host Port:  <none>
    Command:
      cp
    Args:
      -f
      /etc/kube-flannel/cni-conf.json
      /etc/cni/net.d/10-flannel.conflist
    Environment:  <none>
    Mounts:
      /etc/cni/net.d from cni (rw)
      /etc/kube-flannel/ from flannel-cfg (rw)
  Containers:
   kube-flannel:
    Image:      docker.io/flannel/flannel:v0.23.0
    Port:       <none>
    Host Port:  <none>
    Command:
      /opt/bin/flanneld
    Args:
      --ip-masq
      --kube-subnet-mgr
      --iface=eth0
    Requests:
      cpu:     100m
      memory:  50Mi
    Environment:
      POD_NAME:            (v1:metadata.name)
      POD_NAMESPACE:       (v1:metadata.namespace)
      EVENT_QUEUE_DEPTH:  5000
    Mounts:
      /etc/kube-flannel/ from flannel-cfg (rw)
      /run/flannel from run (rw)
      /run/xtables.lock from xtables-lock (rw)
  Volumes:
   run:
    Type:          HostPath (bare host directory volume)
    Path:          /run/flannel
    HostPathType:  
   cni-plugin:
    Type:          HostPath (bare host directory volume)
    Path:          /opt/cni/bin
    HostPathType:  
   cni:
    Type:          HostPath (bare host directory volume)
    Path:          /etc/cni/net.d
    HostPathType:  
   flannel-cfg:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      kube-flannel-cfg
    Optional:  false
   xtables-lock:
    Type:               HostPath (bare host directory volume)
    Path:               /run/xtables.lock
    HostPathType:       FileOrCreate
  Priority Class Name:  system-node-critical
  Node-Selectors:       <none>
  Tolerations:          :NoSchedule op=Exists
Events:
  Type    Reason            Age   From                  Message
  ----    ------            ----  ----                  -------
  Normal  SuccessfulCreate  29m   daemonset-controller  Created pod: kube-flannel-ds-j8z9x

  ```
</details>




<details>
<summary>
Senario -
Deploy a DaemonSet for FluentD Logging.
Use the given specifications.

</summary>

```bash
An easy way to create a DaemonSet is to first generate a YAML file for a Deployment with the command kubectl create deployment elasticsearch --image=registry.k8s.io/fluentd-elasticsearch:1.20 -n kube-system --dry-run=client -o yaml > fluentd.yaml. Next, remove the replicas, strategy and status fields from the YAML file using a text editor. Also, change the kind from Deployment to DaemonSet.
Finally, create the Daemonset by running kubectl create -f fluentd.yaml

controlplane ~ ➜  cat daemon.yaml 
apiVersion: apps/v1
kind: DaemonSet
metadata:
  labels:
    app: elasticsearch
  name: elasticsearch
  namespace: kube-system
spec:
  selector:
    matchLabels:
      app: elasticsearch
  template:
    metadata:
      labels:
        app: elasticsearch
    spec:
      containers:
      - image: registry.k8s.io/fluentd-elasticsearch:1.20
        name: fluentd-elasticsearch



controlplane ~ ➜  k get daemonsets.apps -n kube-system 
NAME            DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
elasticsearch   1         1         1       1            1           <none>                   42s
kube-proxy      1         1         1       1            1           kubernetes.io/os=linux   39m


controlplane ~ ✖ k describe daemonsets.apps ela -n kube-system 
Name:           elasticsearch
Selector:       app=elasticsearch
Node-Selector:  <none>
Labels:         app=elasticsearch
Annotations:    deprecated.daemonset.template.generation: 1
Desired Number of Nodes Scheduled: 1
Current Number of Nodes Scheduled: 1
Number of Nodes Scheduled with Up-to-date Pods: 1
Number of Nodes Scheduled with Available Pods: 1
Number of Nodes Misscheduled: 0
Pods Status:  1 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=elasticsearch
  Containers:
   fluentd-elasticsearch:
    Image:         registry.k8s.io/fluentd-elasticsearch:1.20
    Port:          <none>
    Host Port:     <none>
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Events:
  Type    Reason            Age   From                  Message
  ----    ------            ----  ----                  -------
  Normal  SuccessfulCreate  67s   daemonset-controller  Created pod: elasticsearch-sqp2k

  ```
</details>




<details>
<summary>
Senario -
How many static pods exist in this cluster in all namespaces?
</summary>

```bash
controlplane ~ ➜  ls /etc/kubernetes/manifests/
etcd.yaml  kube-apiserver.yaml  kube-controller-manager.yaml  kube-scheduler.yaml

  ```
</details>



<details>
<summary>
Senario -
What is the path of the directory holding the static pod definition files?
</summary>

```bash
/etc/kubernetes/manifests/
  ```
</details>





<details>
<summary>
Senario -
Create a static pod named static-busybox that uses the busybox image and the command sleep 1000
</summary>

```bash

controlplane ~ ➜  k run static-busybox --image busybox --command sleep 1000 -o yaml --dry-run
 
W0106 07:14:06.943478   10872 helpers.go:703] --dry-run is deprecated and can be replaced with --dry-run=client.
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: static-busybox
  name: static-busybox
spec:
  containers:
  - command:
    - sleep
    - "1000"
    image: busybox
    name: static-busybox
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always


controlplane ~ ➜  k describe po static-busybox-controlplane 
Name:         static-busybox-controlplane
Namespace:    default
Priority:     0
Node:         controlplane/192.168.28.6
Start Time:   Mon, 06 Jan 2025 07:15:32 +0000
Labels:       run=static-busybox
Annotations:  kubernetes.io/config.hash: 52a5e0735b062331e3a56fad930f83b4
              kubernetes.io/config.mirror: 52a5e0735b062331e3a56fad930f83b4
              kubernetes.io/config.seen: 2025-01-06T07:15:32.194815686Z
              kubernetes.io/config.source: file
Status:       Running
IP:           172.17.0.4
IPs:
  IP:           172.17.0.4
Controlled By:  Node/controlplane
Containers:
  static-busybox:
    Container ID:  containerd://1ffe5b01a31449fe39a2cedafea81bd1ea1ee4c5f3592d0d9a3e285264f4107c
    Image:         busybox
    Image ID:      docker.io/library/busybox@sha256:2919d0172f7524b2d8df9e50066a682669e6d170ac0f6a49676d54358fe970b5
    Port:          <none>
    Host Port:     <none>
    Command:
      sleep
      1000
    State:          Running
      Started:      Mon, 06 Jan 2025 07:15:33 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:         <none>
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:                      <none>
QoS Class:                    BestEffort
Node-Selectors:               <none>
Tolerations:                  :NoExecute op=Exists
Events:
  Type    Reason   Age   From     Message
  ----    ------   ----  ----     -------
  Normal  Pulling  35s   kubelet  Pulling image "busybox"
  Normal  Pulled   35s   kubelet  Successfully pulled image "busybox" in 279ms (279ms including waiting). Image size: 2167089 bytes.
  Normal  Created  35s   kubelet  Created container static-busybox
  Normal  Started  34s   kubelet  Started container static-busybox

controlplane ~ ➜  vi /etc/kubernetes/manifests/pod.yaml


  ```
</details>





<details>
<summary>
Senario -
Edit the image on the static pod to use busybox:1.28.4
</summary>

```bash
Simply edit the static pod definition file and save it. If that does not re-create the pod, run: kubectl run --restart=Never --image=busybox:1.28.4 static-busybox --dry-run=client -o yaml --command -- sleep 1000 > /etc/kubernetes/manifests/static-busybox.yaml


controlplane ~ ➜  k describe po static-busybox-controlplane 
Name:         static-busybox-controlplane
Namespace:    default
Priority:     0
Node:         controlplane/192.168.28.6
Start Time:   Mon, 06 Jan 2025 07:15:32 +0000
Labels:       run=static-busybox
Annotations:  kubernetes.io/config.hash: 52a5e0735b062331e3a56fad930f83b4
              kubernetes.io/config.mirror: 52a5e0735b062331e3a56fad930f83b4
              kubernetes.io/config.seen: 2025-01-06T07:15:32.194815686Z
              kubernetes.io/config.source: file
Status:       Running
IP:           172.17.0.5
IPs:
  IP:           172.17.0.5
Controlled By:  Node/controlplane
Containers:
  static-busybox:
    Container ID:  containerd://0a5d09eadf7737b2dd4cc4cf4f221f5d7667f8bc72c0933afc1fbeb1ac2d21cf
    Image:         busybox
    Image ID:      docker.io/library/busybox@sha256:2919d0172f7524b2d8df9e50066a682669e6d170ac0f6a49676d54358fe970b5
    Port:          <none>
    Host Port:     <none>
    Command:
      sleep
      1000
    State:          Running
      Started:      Mon, 06 Jan 2025 07:16:59 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:         <none>
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:                      <none>
QoS Class:                    BestEffort
Node-Selectors:               <none>
Tolerations:                  :NoExecute op=Exists
Events:
  Type    Reason          Age                 From     Message
  ----    ------          ----                ----     -------
  Normal  Pulled          2m4s                kubelet  Successfully pulled image "busybox" in 279ms (279ms including waiting). Image size: 2167089 bytes.
  Normal  Pulling         37s (x2 over 2m4s)  kubelet  Pulling image "busybox"
  Normal  Created         37s (x2 over 2m4s)  kubelet  Created container static-busybox
  Normal  Started         37s (x2 over 2m3s)  kubelet  Started container static-busybox
  Normal  SandboxChanged  37s                 kubelet  Pod sandbox changed, it will be killed and re-created.
  Normal  Pulled          37s                 kubelet  Successfully pulled image "busybox" in 143ms (143ms including waiting). Image size: 2167089 bytes.
  Normal  Killing         15s (x2 over 70s)   kubelet  Stopping container static-busybox

controlplane ~ ➜  k delete po static-busybox-controlplane 
pod "static-busybox-controlplane" deleted
^C

controlplane ~ ✖ k describe po static-busybox-controlplane 
Name:         static-busybox-controlplane
Namespace:    default
Priority:     0
Node:         controlplane/192.168.28.6
Start Time:   Mon, 06 Jan 2025 07:15:32 +0000
Labels:       run=static-busybox
Annotations:  kubernetes.io/config.hash: 7b583aa60fcb47c3951f448336d84b1d
              kubernetes.io/config.mirror: 7b583aa60fcb47c3951f448336d84b1d
              kubernetes.io/config.seen: 2025-01-06T07:17:33.004894164Z
              kubernetes.io/config.source: file
Status:       Running
IP:           172.17.0.6
IPs:
  IP:           172.17.0.6
Controlled By:  Node/controlplane
Containers:
  static-busybox:
    Container ID:  containerd://6286388951bc3da2697a3c305bdf319ad411b9963a70d19d9cb5046ff8628206
    Image:         busybox:1.28.4
    Image ID:      docker.io/library/busybox@sha256:141c253bc4c3fd0a201d32dc1f493bcf3fff003b6df416dea4f41046e0f37d47
    Port:          <none>
    Host Port:     <none>
    Command:
      sleep
      1000
    State:          Running
      Started:      Mon, 06 Jan 2025 07:18:00 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:         <none>
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:                      <none>
QoS Class:                    BestEffort
Node-Selectors:               <none>
Tolerations:                  :NoExecute op=Exists
Events:
  Type    Reason   Age   From     Message
  ----    ------   ----  ----     -------
  Normal  Pulling  81s   kubelet  Pulling image "busybox:1.28.4"
  Normal  Pulled   81s   kubelet  Successfully pulled image "busybox:1.28.4" in 214ms (214ms including waiting). Image size: 727869 bytes.
  Normal  Created  81s   kubelet  Created container static-busybox
  Normal  Started  80s   kubelet  Started container static-busybox

controlplane ~ ➜  
  ```
</details>





<details>
<summary>
Senario -

</summary>

```bash

controlplane /etc/kubernetes/manifests ➜  k get all
NAME                              READY   STATUS    RESTARTS   AGE
pod/static-busybox-controlplane   1/1     Running   0          9m4s
pod/static-greenbox-node01        1/1     Running   0          22s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   172.20.0.1   <none>        443/TCP   23m

controlplane /etc/kubernetes/manifests ➜  ssh node01
Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-1071-gcp x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
Last login: Mon Jan  6 07:22:45 2025 from 192.168.28.6

node01 ~ ➜  ps -ef
UID          PID    PPID  C STIME TTY          TIME CMD
root           1       0  0 07:02 ?        00:00:01 /sbin/init --log-level=err
root         219       1  0 07:02 ?        00:00:00 /lib/systemd/systemd-journald
root         493       1  0 07:02 ?        00:00:00 /lib/systemd/systemd-udevd
systemd+     554       1  0 07:02 ?        00:00:04 /lib/systemd/systemd-resolved
message+     854       1  0 07:02 ?        00:00:00 @dbus-daemon --system --address=systemd: 
root         912       1  0 07:02 ?        00:00:03 /lib/systemd/systemd-logind
root         934       1  0 07:02 ?        00:00:00 /usr/bin/ttyd -p 8080 --ping-interval 30 
root         935       1  0 07:02 ?        00:00:06 /usr/bin/containerd
root         936       1  0 07:02 ?        00:00:00 sshd: /usr/sbin/sshd -D [listener] 0 of 1
root        2748       1  0 07:03 ?        00:00:00 /usr/bin/containerd-shim-runc-v2 -namespa
root        2749       1  0 07:03 ?        00:00:00 /usr/bin/containerd-shim-runc-v2 -namespa
65535       2803    2749  0 07:03 ?        00:00:00 /pause
65535       2810    2748  0 07:03 ?        00:00:00 /pause
root        3001    2749  0 07:03 ?        00:00:00 /usr/local/bin/kube-proxy --config=/var/l
root        3438    2748  0 07:03 ?        00:00:01 /opt/bin/flanneld --ip-masq --kube-subnet
root       12150       1  0 07:21 ?        00:00:03 /usr/bin/kubelet --bootstrap-kubeconfig=/
root       12486       1  0 07:21 ?        00:00:00 /usr/bin/containerd-shim-runc-v2 -namespa
65535      12513   12486  0 07:21 ?        00:00:00 /pause
root       12610   12486  0 07:21 ?        00:00:00 sleep 3000
root       15786     936  0 07:27 ?        00:00:00 sshd: root@pts/0
root       15814       1  0 07:27 ?        00:00:00 /lib/systemd/systemd --user
root       15821   15814  0 07:27 ?        00:00:00 (sd-pam)
root       15859   15786  0 07:27 pts/0    00:00:00 -bash
root       15960   15859  0 07:27 pts/0    00:00:00 ps -ef

node01 ~ ➜  ps -ef | grep kublet
root       16040   15859  0 07:27 pts/0    00:00:00 grep kublet

node01 ~ ➜  ps -ef
UID          PID    PPID  C STIME TTY          TIME CMD
root           1       0  0 07:02 ?        00:00:02 /sbin/init --log-level=err
root         219       1  0 07:02 ?        00:00:00 /lib/systemd/systemd-journald
root         493       1  0 07:02 ?        00:00:00 /lib/systemd/systemd-udevd
systemd+     554       1  0 07:02 ?        00:00:04 /lib/systemd/systemd-resolved
message+     854       1  0 07:02 ?        00:00:00 @dbus-daemon --system --address=systemd: 
root         912       1  0 07:02 ?        00:00:03 /lib/systemd/systemd-logind
root         934       1  0 07:02 ?        00:00:00 /usr/bin/ttyd -p 8080 --ping-interval 30 
root         935       1  0 07:02 ?        00:00:06 /usr/bin/containerd
root         936       1  0 07:02 ?        00:00:00 sshd: /usr/sbin/sshd -D [listener] 0 of 1
root        2748       1  0 07:03 ?        00:00:00 /usr/bin/containerd-shim-runc-v2 -namespa
root        2749       1  0 07:03 ?        00:00:00 /usr/bin/containerd-shim-runc-v2 -namespa
65535       2803    2749  0 07:03 ?        00:00:00 /pause
65535       2810    2748  0 07:03 ?        00:00:00 /pause
root        3001    2749  0 07:03 ?        00:00:00 /usr/local/bin/kube-proxy --config=/var/l
root        3438    2748  0 07:03 ?        00:00:01 /opt/bin/flanneld --ip-masq --kube-subnet
root       12150       1  0 07:21 ?        00:00:03 /usr/bin/kubelet --bootstrap-kubeconfig=/
root       12486       1  0 07:21 ?        00:00:00 /usr/bin/containerd-shim-runc-v2 -namespa
65535      12513   12486  0 07:21 ?        00:00:00 /pause
root       12610   12486  0 07:21 ?        00:00:00 sleep 3000
root       15786     936  0 07:27 ?        00:00:00 sshd: root@pts/0
root       15814       1  0 07:27 ?        00:00:00 /lib/systemd/systemd --user
root       15821   15814  0 07:27 ?        00:00:00 (sd-pam)
root       15859   15786  0 07:27 pts/0    00:00:00 -bash
root       16241   15859  0 07:27 pts/0    00:00:00 ps -ef

node01 ~ ➜  ls /usr/bin/kubelet
/usr/bin/kubelet

node01 ~ ➜  ps -ef | grep /usr/bin/kubelet
root       12150       1  0 07:21 ?        00:00:03 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --container-runtime-endpoint=unix:///var/run/containerd/containerd.sock --pod-infra-container-image=registry.k8s.io/pause:3.10


node01 ~ ➜  ls /var/lib/kubelet/
checkpoints  cpu_manager_state  kubeadm-flags.env     pki      plugins_registry  pods
config.yaml  device-plugins     memory_manager_state  plugins  pod-resources

node01 ~ ➜  cd /var/lib/kubelet/

node01 /var/lib/kubelet ➜  cat config.yaml 
apiVersion: kubelet.config.k8s.io/v1beta1
authentication:
  anonymous:
    enabled: false
  webhook:
    cacheTTL: 0s
    enabled: true
  x509:
    clientCAFile: /etc/kubernetes/pki/ca.crt
authorization:
  mode: Webhook
  webhook:
    cacheAuthorizedTTL: 0s
    cacheUnauthorizedTTL: 0s
cgroupDriver: cgroupfs
clusterDNS:
- 172.20.0.10
clusterDomain: cluster.local
containerRuntimeEndpoint: ""
cpuManagerReconcilePeriod: 0s
evictionPressureTransitionPeriod: 0s
fileCheckFrequency: 0s
healthzBindAddress: 127.0.0.1
healthzPort: 10248
httpCheckFrequency: 0s
imageMaximumGCAge: 0s
imageMinimumGCAge: 0s
kind: KubeletConfiguration
logging:
  flushFrequency: 0
  options:
    json:
      infoBufferSize: "0"
    text:
      infoBufferSize: "0"
  verbosity: 0
memorySwap: {}
nodeStatusReportFrequency: 0s
nodeStatusUpdateFrequency: 0s
resolvConf: /run/systemd/resolve/resolv.conf
rotateCertificates: true
runtimeRequestTimeout: 0s
shutdownGracePeriod: 0s
shutdownGracePeriodCriticalPods: 0s
staticPodPath: /etc/just-to-mess-with-you
streamingConnectionIdleTimeout: 0s
syncFrequency: 0s
volumeStatsAggPeriod: 0s

node01 /var/lib/kubelet ➜  ls
checkpoints  cpu_manager_state  kubeadm-flags.env     pki      plugins_registry  pods
config.yaml  device-plugins     memory_manager_state  plugins  pod-resources

node01 /var/lib/kubelet ➜  cd pods/

node01 lib/kubelet/pods ➜  ls
4888d237-c4d1-4c66-8f4d-6536383908f7  5f4bad94ca4ed380d14b75e5c7fecd18
4ea360cf-a60e-4ea6-b48d-56858c7b936b

node01 lib/kubelet/pods ➜  cd ..

node01 /var/lib/kubelet ➜  /etc/just-to-mess-with-you/
-bash: /etc/just-to-mess-with-you/: Is a directory

node01 /var/lib/kubelet ✖ cd /etc/just-to-mess-with-you/

node01 /etc/just-to-mess-with-you ➜  ls
greenbox.yaml

node01 /etc/just-to-mess-with-you ➜  rm -rf greenbox.yaml 


controlplane /etc/kubernetes/manifests ➜  k get all
NAME                              READY   STATUS    RESTARTS   AGE
pod/static-busybox-controlplane   1/1     Running   0          16m

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   172.20.0.1   <none>        443/TCP   31m




First, let's identify the node in which the pod called static-greenbox is created. To do this, run:
root@controlplane:~# kubectl get pods --all-namespaces -o wide  | grep static-greenbox
default       static-greenbox-node01                 1/1     Running   0          19s     10.244.1.2   node01       <none>           <none>
root@controlplane:~#
From the result of this command, we can see that the pod is running on node01.


Next, SSH to node01 and identify the path configured for static pods in this node.

Important: The path need not be /etc/kubernetes/manifests. Make sure to check the path configured in the kubelet configuration file.
root@controlplane:~# ssh node01 
root@node01:~# ps -ef |  grep /usr/bin/kubelet 
root        4147       1  0 14:05 ?        00:00:00 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --container-runtime-endpoint=unix:///var/run/containerd/containerd.sock --pod-infra-container-image=registry.k8s.io/pause:3.9
root        4773    4733  0 14:05 pts/0    00:00:00 grep /usr/bin/kubelet

root@node01:~# grep -i staticpod /var/lib/kubelet/config.yaml
staticPodPath: /etc/just-to-mess-with-you

root@node01:~# 
Here the staticPodPath is /etc/just-to-mess-with-you


Navigate to this directory and delete the YAML file:
root@node01:/etc/just-to-mess-with-you# ls
greenbox.yaml
root@node01:/etc/just-to-mess-with-you# rm -rf greenbox.yaml 
root@node01:/etc/just-to-mess-with-you#
Exit out of node01 using CTRL + D or type exit. You should return to the controlplane node. Check if the static-greenbox pod has been deleted:
root@controlplane:~# kubectl get pods --all-namespaces -o wide  | grep static-greenbox
root@controlplane:~# 


  ```
</details>





<details>
<summary>
Senario -
what is the default pod name of scheduler
What is the image used to deploy the kubernetes scheduler?
Inspect the kubernetes scheduler pod and identify the image
</summary>

```bash
controlplane ~ ➜  k get po -n kube-system 
NAME                                   READY   STATUS    RESTARTS   AGE
coredns-77d6fd4654-h7pb6               1/1     Running   0          4m9s
coredns-77d6fd4654-ml22x               1/1     Running   0          4m9s
etcd-controlplane                      1/1     Running   0          4m16s
kube-apiserver-controlplane            1/1     Running   0          4m16s
kube-controller-manager-controlplane   1/1     Running   0          4m16s
kube-proxy-tfh2k                       1/1     Running   0          4m10s
kube-scheduler-controlplane            1/1     Running   0          4m16s


controlplane ~ ➜  k describe po kube-scheduler-controlplane -n kube-system 
Name:                 kube-scheduler-controlplane
Namespace:            kube-system
Priority:             2000001000
Priority Class Name:  system-node-critical
Node:                 controlplane/192.168.28.12
Start Time:           Mon, 06 Jan 2025 07:34:58 +0000
Labels:               component=kube-scheduler
                      tier=control-plane
Annotations:          kubernetes.io/config.hash: 3a38305f8aed7021a1cff9b93fc47052
                      kubernetes.io/config.mirror: 3a38305f8aed7021a1cff9b93fc47052
                      kubernetes.io/config.seen: 2025-01-06T07:34:58.515854874Z
                      kubernetes.io/config.source: file
Status:               Running
SeccompProfile:       RuntimeDefault
IP:                   192.168.28.12
IPs:
  IP:           192.168.28.12
Controlled By:  Node/controlplane
Containers:
  kube-scheduler:
    Container ID:  containerd://0e5153a0ed1f3f4750b7c81c01a9815ed2a4df0feb3104017b2d350cbfaebd86
    Image:         registry.k8s.io/kube-scheduler:v1.31.0
    Image ID:      registry.k8s.io/kube-scheduler@sha256:96ddae9c9b2e79342e0551e2d2ec422c0c02629a74d928924aaa069706619808
    Port:          <none>
    Host Port:     <none>
    Command:
      kube-scheduler
      --authentication-kubeconfig=/etc/kubernetes/scheduler.conf
      --authorization-kubeconfig=/etc/kubernetes/scheduler.conf
      --bind-address=127.0.0.1
      --kubeconfig=/etc/kubernetes/scheduler.conf
      --leader-elect=true
    State:          Running
      Started:      Mon, 06 Jan 2025 07:34:52 +0000
    Ready:          True
    Restart Count:  0
    Requests:
      cpu:        100m
    Liveness:     http-get https://127.0.0.1:10259/healthz delay=10s timeout=15s period=10s #success=1 #failure=8
    Startup:      http-get https://127.0.0.1:10259/healthz delay=10s timeout=15s period=10s #success=1 #failure=24
    Environment:  <none>
    Mounts:
      /etc/kubernetes/scheduler.conf from kubeconfig (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kubeconfig:
    Type:          HostPath (bare host directory volume)
    Path:          /etc/kubernetes/scheduler.conf
    HostPathType:  FileOrCreate
QoS Class:         Burstable
Node-Selectors:    <none>
Tolerations:       :NoExecute op=Exists
Events:
  Type     Reason                  Age    From     Message
  ----     ------                  ----   ----     -------
  Warning  FailedCreatePodSandBox  6m20s  kubelet  Failed to create pod sandbox: open /run/systemd/resolve/resolv.conf: no such file or directory
  Normal   Pulled                  6m5s   kubelet  Container image "registry.k8s.io/kube-scheduler:v1.31.0" already present on machine
  Normal   Created                 6m5s   kubelet  Created container kube-scheduler
  Normal   Started                 6m5s   kubelet  Started container kube-scheduler


  ```
</details>




<details>
<summary>
Senario -
We have already created the ServiceAccount and ClusterRoleBinding that our custom scheduler will make use of.
Checkout the following Kubernetes objects:
ServiceAccount: my-scheduler (kube-system namespace)
ClusterRoleBinding: my-scheduler-as-kube-scheduler
ClusterRoleBinding: my-scheduler-as-volume-scheduler
Run the command: kubectl get serviceaccount -n kube-system and kubectl get clusterrolebinding
Note: - Don't worry if you are not familiar with these resources. We will cover it later on.
</summary>

```bash

controlplane ~ ➜   kubectl get serviceaccount -n kube-system 
NAME                                          SECRETS   AGE
attachdetach-controller                       0         7m33s
bootstrap-signer                              0         7m37s
certificate-controller                        0         7m37s
clusterrole-aggregation-controller            0         7m37s
coredns                                       0         7m38s
cronjob-controller                            0         7m33s
daemon-set-controller                         0         7m37s
default                                       0         7m32s
deployment-controller                         0         7m35s
disruption-controller                         0         7m35s
endpoint-controller                           0         7m34s
endpointslice-controller                      0         7m35s
endpointslicemirroring-controller             0         7m33s
ephemeral-volume-controller                   0         7m36s
expand-controller                             0         7m35s
generic-garbage-collector                     0         7m37s
horizontal-pod-autoscaler                     0         7m37s
job-controller                                0         7m36s
kube-proxy                                    0         7m38s
legacy-service-account-token-cleaner          0         7m35s
my-scheduler                                  0         86s
namespace-controller                          0         7m35s
node-controller                               0         7m37s
persistent-volume-binder                      0         7m34s
pod-garbage-collector                         0         7m34s
pv-protection-controller                      0         7m36s
pvc-protection-controller                     0         7m34s
replicaset-controller                         0         7m37s
replication-controller                        0         7m37s
resourcequota-controller                      0         7m33s
root-ca-cert-publisher                        0         7m37s
service-account-controller                    0         7m34s
service-controller                            0         7m37s
statefulset-controller                        0         7m33s
token-cleaner                                 0         7m36s
ttl-after-finished-controller                 0         7m33s
ttl-controller                                0         7m37s
validatingadmissionpolicy-status-controller   0         7m36s

controlplane ~ ➜  kubectl get clusterrolebinding
NAME                                                            ROLE                                                                               AGE
cluster-admin                                                   ClusterRole/cluster-admin                                                          7m50s
flannel                                                         ClusterRole/flannel                                                                7m47s
kubeadm:cluster-admins                                          ClusterRole/cluster-admin                                                          7m49s
kubeadm:get-nodes                                               ClusterRole/kubeadm:get-nodes                                                      7m49s
kubeadm:kubelet-bootstrap                                       ClusterRole/system:node-bootstrapper                                               7m49s
kubeadm:node-autoapprove-bootstrap                              ClusterRole/system:certificates.k8s.io:certificatesigningrequests:nodeclient       7m49s
kubeadm:node-autoapprove-certificate-rotation                   ClusterRole/system:certificates.k8s.io:certificatesigningrequests:selfnodeclient   7m49s
kubeadm:node-proxier                                            ClusterRole/system:node-proxier                                                    7m48s
my-scheduler-as-kube-scheduler                                  ClusterRole/system:kube-scheduler                                                  96s
my-scheduler-as-volume-scheduler                                ClusterRole/system:volume-scheduler                                                96s
system:basic-user                                               ClusterRole/system:basic-user                                                      7m50s
system:controller:attachdetach-controller                       ClusterRole/system:controller:attachdetach-controller                              7m50s
system:controller:certificate-controller                        ClusterRole/system:controller:certificate-controller                               7m49s
system:controller:clusterrole-aggregation-controller            ClusterRole/system:controller:clusterrole-aggregation-controller                   7m50s
system:controller:cronjob-controller                            ClusterRole/system:controller:cronjob-controller                                   7m50s
system:controller:daemon-set-controller                         ClusterRole/system:controller:daemon-set-controller                                7m50s
system:controller:deployment-controller                         ClusterRole/system:controller:deployment-controller                                7m50s
system:controller:disruption-controller                         ClusterRole/system:controller:disruption-controller                                7m50s
system:controller:endpoint-controller                           ClusterRole/system:controller:endpoint-controller                                  7m50s
system:controller:endpointslice-controller                      ClusterRole/system:controller:endpointslice-controller                             7m50s
system:controller:endpointslicemirroring-controller             ClusterRole/system:controller:endpointslicemirroring-controller                    7m50s
system:controller:ephemeral-volume-controller                   ClusterRole/system:controller:ephemeral-volume-controller                          7m50s
system:controller:expand-controller                             ClusterRole/system:controller:expand-controller                                    7m50s
system:controller:generic-garbage-collector                     ClusterRole/system:controller:generic-garbage-collector                            7m50s
system:controller:horizontal-pod-autoscaler                     ClusterRole/system:controller:horizontal-pod-autoscaler                            7m50s
system:controller:job-controller                                ClusterRole/system:controller:job-controller                                       7m50s
system:controller:legacy-service-account-token-cleaner          ClusterRole/system:controller:legacy-service-account-token-cleaner                 7m49s
system:controller:namespace-controller                          ClusterRole/system:controller:namespace-controller                                 7m50s
system:controller:node-controller                               ClusterRole/system:controller:node-controller                                      7m50s
system:controller:persistent-volume-binder                      ClusterRole/system:controller:persistent-volume-binder                             7m50s
system:controller:pod-garbage-collector                         ClusterRole/system:controller:pod-garbage-collector                                7m50s
system:controller:pv-protection-controller                      ClusterRole/system:controller:pv-protection-controller                             7m49s
system:controller:pvc-protection-controller                     ClusterRole/system:controller:pvc-protection-controller                            7m49s
system:controller:replicaset-controller                         ClusterRole/system:controller:replicaset-controller                                7m49s
system:controller:replication-controller                        ClusterRole/system:controller:replication-controller                               7m49s
system:controller:resourcequota-controller                      ClusterRole/system:controller:resourcequota-controller                             7m49s
system:controller:root-ca-cert-publisher                        ClusterRole/system:controller:root-ca-cert-publisher                               7m49s
system:controller:route-controller                              ClusterRole/system:controller:route-controller                                     7m49s
system:controller:service-account-controller                    ClusterRole/system:controller:service-account-controller                           7m49s
system:controller:service-controller                            ClusterRole/system:controller:service-controller                                   7m49s
system:controller:statefulset-controller                        ClusterRole/system:controller:statefulset-controller                               7m49s
system:controller:ttl-after-finished-controller                 ClusterRole/system:controller:ttl-after-finished-controller                        7m49s
system:controller:ttl-controller                                ClusterRole/system:controller:ttl-controller                                       7m49s
system:controller:validatingadmissionpolicy-status-controller   ClusterRole/system:controller:validatingadmissionpolicy-status-controller          7m49s
system:coredns                                                  ClusterRole/system:coredns                                                         7m48s
system:discovery                                                ClusterRole/system:discovery                                                       7m50s
system:kube-controller-manager                                  ClusterRole/system:kube-controller-manager                                         7m50s
system:kube-dns                                                 ClusterRole/system:kube-dns                                                        7m50s
system:kube-scheduler                                           ClusterRole/system:kube-scheduler                                                  7m50s
system:monitoring                                               ClusterRole/system:monitoring                                                      7m50s
system:node                                                     ClusterRole/system:node                                                            7m50s
system:node-proxier                                             ClusterRole/system:node-proxier                                                    7m50s
system:public-info-viewer                                       ClusterRole/system:public-info-viewer                                              7m50s
system:service-account-issuer-discovery                         ClusterRole/system:service-account-issuer-discovery                                7m50s
system:volume-scheduler                                         ClusterRole/system:volume-scheduler                                                7m50s


  ```
</details>



<details>
<summary>
Senario -
Let's create a configmap that the new scheduler will employ using the concept of ConfigMap as a volume.
We have already given a configMap definition file called my-scheduler-configmap.yaml at /root/ path that will create a configmap with name my-scheduler-config using the content of file /root/my-scheduler-config.yaml.

</summary>

```bash

controlplane ~ ➜  ls
my-scheduler-configmap.yaml  my-scheduler-config.yaml  my-scheduler.yaml  nginx-pod.yaml

controlplane ~ ➜  cat my-scheduler-configmap.yaml 
apiVersion: v1
data:
  my-scheduler-config.yaml: |
    apiVersion: kubescheduler.config.k8s.io/v1
    kind: KubeSchedulerConfiguration
    profiles:
      - schedulerName: my-scheduler
    leaderElection:
      leaderElect: false
kind: ConfigMap
metadata:
  creationTimestamp: null
  name: my-scheduler-config
  namespace: kube-system


controlplane ~ ➜  k get cm -n kube-system 
NAME                                                   DATA   AGE
coredns                                                1      10m
extension-apiserver-authentication                     6      10m
kube-apiserver-legacy-service-account-token-tracking   1      10m
kube-proxy                                             2      10m
kube-root-ca.crt                                       1      10m
kubeadm-config                                         1      10m
kubelet-config                                         1      10m

controlplane ~ ➜  k apply -f my-scheduler-config.yaml
error: resource mapping not found for name: "" namespace: "" from "my-scheduler-config.yaml": no matches for kind "KubeSchedulerConfiguration" in version "kubescheduler.config.k8s.io/v1"
ensure CRDs are installed first

controlplane ~ ✖ kubectl create -f /root/my-scheduler-configmap.yaml
configmap/my-scheduler-config created

  ```
</details>



<details>
<summary>
Senario -
Deploy an additional scheduler to the cluster following the given specification.
Use the manifest file provided at /root/my-scheduler.yaml. Use the same image as used by the default kubernetes scheduler.
```bash

controlplane ~ ➜  cat my-scheduler.yaml 
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: my-scheduler
  name: my-scheduler
  namespace: kube-system
spec:
  serviceAccountName: my-scheduler
  containers:
  - command:
    - /usr/local/bin/kube-scheduler
    - --config=/etc/kubernetes/my-scheduler/my-scheduler-config.yaml
    image: <use-correct-image>
    livenessProbe:
      httpGet:
        path: /healthz
        port: 10259
        scheme: HTTPS
      initialDelaySeconds: 15
    name: kube-second-scheduler
    readinessProbe:
      httpGet:
        path: /healthz
        port: 10259
        scheme: HTTPS
    resources:
      requests:
        cpu: '0.1'
    securityContext:
      privileged: false
    volumeMounts:
      - name: config-volume
        mountPath: /etc/kubernetes/my-scheduler
  hostNetwork: false
  hostPID: false
  volumes:
    - name: config-volume
      configMap:
        name: my-scheduler-config

  ```

</summary>

```bash
---
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: my-scheduler
  name: my-scheduler
  namespace: kube-system
spec:
  serviceAccountName: my-scheduler
  containers:
  - command:
    - /usr/local/bin/kube-scheduler
    - --config=/etc/kubernetes/my-scheduler/my-scheduler-config.yaml
    image: registry.k8s.io/kube-scheduler:v1.31.0  # changed
    livenessProbe:
      httpGet:
        path: /healthz
        port: 10259
        scheme: HTTPS
      initialDelaySeconds: 15
    name: kube-second-scheduler
    readinessProbe:
      httpGet:
        path: /healthz
        port: 10259
        scheme: HTTPS
    resources:
      requests:
        cpu: '0.1'
    securityContext:
      privileged: false
    volumeMounts:
      - name: config-volume
        mountPath: /etc/kubernetes/my-scheduler
  hostNetwork: false
  hostPID: false
  volumes:
    - name: config-volume
      configMap:
        name: my-scheduler-config

  ```
</details>



<details>
<summary>
Senario -
A POD definition file is given. Use it to create a POD with the new custom scheduler.
File is located at /root/nginx-pod.yaml
```bash
controlplane ~ ➜  cat nginx-pod.yaml 
apiVersion: v1 
kind: Pod 
metadata:
  name: nginx 
spec:
  containers:
  - image: nginx
    name: nginx
  ```

</summary>

```bash
controlplane ~ ➜  k get po -n kube-system 
NAME                                   READY   STATUS    RESTARTS   AGE
coredns-77d6fd4654-h7pb6               1/1     Running   0          16m
coredns-77d6fd4654-ml22x               1/1     Running   0          16m
etcd-controlplane                      1/1     Running   0          16m
kube-apiserver-controlplane            1/1     Running   0          16m
kube-controller-manager-controlplane   1/1     Running   0          16m
kube-proxy-tfh2k                       1/1     Running   0          16m
kube-scheduler-controlplane            1/1     Running   0          16m
my-scheduler                           1/1     Running   0          6s

controlplane ~ ➜  cat nginx-pod.yaml 
apiVersion: v1 
kind: Pod 
metadata:
  name: nginx 
spec:
  schedulerName: my-scheduler
  containers:
  - image: nginx
    name: nginx

controlplane ~ ➜  k apply -f nginx-pod.yaml 
pod/nginx created

controlplane ~ ✖ k get po
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          9s

  ```
</details>






