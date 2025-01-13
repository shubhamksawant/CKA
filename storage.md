 Persistent Volume Claims

<details>
<summary>
Senario -
We have deployed a POD. Inspect the POD and wait for it to start running.

In the current(default) namespace.
```bash

controlplane ~ ➜  k get pod webapp 
NAME     READY   STATUS    RESTARTS   AGE
webapp   1/1     Running   0          2m36s

controlplane ~ ➜  k describe po webapp 
Name:             webapp
Namespace:        default
Priority:         0
Service Account:  default
Node:             controlplane/192.168.233.154
Start Time:       Thu, 09 Jan 2025 13:07:39 +0000
Labels:           <none>
Annotations:      <none>
Status:           Running
IP:               172.17.0.4
IPs:
  IP:  172.17.0.4
Containers:
  event-simulator:
    Container ID:   containerd://bb78cda29fc524b48ab0f0a988dd7b0cd068a53bb6bbe0af1de7dc85b55e021e
    Image:          kodekloud/event-simulator
    Image ID:       docker.io/kodekloud/event-simulator@sha256:1e3e9c72136bbc76c96dd98f29c04f298c3ae241c7d44e2bf70bcc209b030bf9
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Thu, 09 Jan 2025 13:07:43 +0000
    Ready:          True
    Restart Count:  0
    Environment:
      LOG_HANDLERS:  file
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-l8wm6 (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kube-api-access-l8wm6:
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
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  2m55s  default-scheduler  Successfully assigned default/webapp to controlplane
  Normal  Pulling    2m54s  kubelet            Pulling image "kodekloud/event-simulator"
  Normal  Pulled     2m52s  kubelet            Successfully pulled image "kodekloud/event-simulator" in 2.131s (2.131s including waiting). Image size: 28855042 bytes.
  Normal  Created    2m52s  kubelet            Created container event-simulator
  Normal  Started    2m51s  kubelet            Started container event-simulator

 
  ```
The application stores logs at location /log/app.log. View the logs.


You can exec in to the container and open the file:

kubectl exec webapp -- cat /log/app.log

</summary>

```bash

controlplane ~ ➜  k exec webapp -- cat /log/app.log
[2025-01-09 13:07:43,205] INFO in event-simulator: USER1 logged out
[2025-01-09 13:07:44,206] INFO in event-simulator: USER4 logged in
[2025-01-09 13:07:45,207] INFO in event-simulator: USER4 is viewing page3
[2025-01-09 13:07:46,208] INFO in event-simulator: USER1 is viewing page1
[2025-01-09 13:07:47,209] INFO in event-simulator: USER3 logged in
[2025-01-09 13:07:48,210] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2025-01-09 13:07:48,210] INFO in event-simulator: USER2 logged out
[2025-01-09 13:07:49,211] INFO in event-simulator: USER2 is viewing page1
[2025-01-09 13:07:50,212] INFO in event-simulator: USER4 is viewing page1
[2025-01-09 13:07:51,214] WARNING in event-simulator: USER7 Order failed as the item is OUT OF STOCK.
[2025-01-09 13:07:51,214] INFO in event-simulator: USER2 logged in
[2025-01-09 13:07:52,215] INFO in event-simulator: USER3 logged out
[2025-01-09 13:07:53,216] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2025-01-09 13:07:53,216] INFO in event-simulator: USER3 is viewing page2
[2025-01-09 13:07:54,217] INFO in event-simulator: USER2 logged out
[2025-01-09 13:07:55,219] INFO in event-simulator: USER4 is viewing page1
[2025-01-09 13:07:56,219] INFO in event-simulator: USER1 is viewing page2
[2025-01-09 13:07:57,220] INFO in event-simulator: USER2 is viewing page1
[2025-01-09 13:07:58,221] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2025-01-09 13:07:58,222] INFO in event-simulator: USER2 is viewing page3
[2025-01-09 13:07:59,222] WARNING in event-simulator: USER7 Order failed as the item is OUT OF STOCK.
[2025-01-09 13:07:59,222] INFO in event-simulator: USER3 logged in
[2025-01-09 13:08:00,223] INFO in event-simulator: USER1 logged in
[2025-01-09 13:08:01,225] INFO in event-simulator: USER1 is viewing page2
[2025-01-09 13:08:02,226] INFO in event-simulator: USER4 logged in
[2025-01-09 13:08:03,227] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2025-01-09 13:08:03,227] INFO in event-simulator: USER1 is viewing page2
[2025-01-09 13:08:04,228] INFO in event-simulator: USER2 logged in
[2025-01-09 13:08:05,230] INFO in event-simulator: USER4 is viewing page1
[2025-01-09 13:08:06,230] INFO in event-simulator: USER2 is viewing page3
[2025-01-09 13:08:07,231] WARNING in event-simulator: USER7 Order failed as the item is OUT OF STOCK.
[2025-01-09 13:08:07,231] INFO in event-simulator: USER4 is viewing page1
[2025-01-09 13:08:08,232] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2025-01-09 13:08:08,233] INFO in event-simulator: USER1 is viewing page1
[2025-01-09 13:08:09,234] INFO in event-simulator: USER1 is viewing page1
[2025-01-09 13:08:10,234] INFO in event-simulator: USER2 is viewing page1
[2025-01-09 13:08:11,235] INFO in event-simulator: USER3 is viewing page2
[2025-01-09 13:08:12,236] INFO in event-simulator: USER2 is viewing page1
[2025-01-09 13:08:13,237] WARNING in event-simulator: USER5 Failed to Login as the account is locked due to MANY FAILED ATTEMPTS.
[2025-01-09 13:08:13,238] INFO in event-simulator: USER4 is viewing page1
[2025-01-09 13:08:14,239] INFO in event-simulator: USER2 logged in
[2025-01-09 13:08:15,240] WARNING in event-simulator: USER7 Order failed as the item is OUT OF 
  ```
</details>


<details>
<summary>
Senario -
Configure a volume to store these logs at /var/log/webapp on the host.
Use the spec provided below.
Name: webapp
Image Name: kodekloud/event-simulator
Volume HostPath: /var/log/webapp
Volume Mount: /log

</summary>

```bash

controlplane ~ ➜  cat pod.yaml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: webapp
  name: webapp
spec:
  containers:
  - image: kodekloud/event-simulator
    name: webapp
    volumeMounts:
      - name: log
        mountPath: /log
  volumes:
    - hostPath: 
        path: /var/log/webapp
        type: Directory
      name:  log


controlplane ~ ✖ k replace --force -f pod.yaml 
pod "webapp" deleted
pod/webapp replaced

controlplane ~ ➜  ls /var/log/webapp
app.log

controlplane ~ ➜  k describe po webapp 
Name:             webapp
Namespace:        default
Priority:         0
Service Account:  default
Node:             controlplane/192.168.28.34
Start Time:       Thu, 09 Jan 2025 13:55:33 +0000
Labels:           run=webapp
Annotations:      <none>
Status:           Running
IP:               172.17.0.5
IPs:
  IP:  172.17.0.5
Containers:
  webapp:
    Container ID:   containerd://d7947fa8a0b5cef50632a6355cf83887d229fa374227a061ac0e0a4190b76a39
    Image:          kodekloud/event-simulator
    Image ID:       docker.io/kodekloud/event-simulator@sha256:1e3e9c72136bbc76c96dd98f29c04f298c3ae241c7d44e2bf70bcc209b030bf9
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Thu, 09 Jan 2025 13:55:34 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /log from log (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-bl4t2 (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  log:
    Type:          HostPath (bare host directory volume)
    Path:          /var/log/webapp
    HostPathType:  Directory
  kube-api-access-bl4t2:
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
  Normal  Scheduled  27s   default-scheduler  Successfully assigned default/webapp to controlplane
  Normal  Pulling    26s   kubelet            Pulling image "kodekloud/event-simulator"
  Normal  Pulled     26s   kubelet            Successfully pulled image "kodekloud/event-simulator" in 140ms (140ms including waiting). Image size: 28855042 bytes.
  Normal  Created    26s   kubelet            Created container webapp
  Normal  Started    26s   kubelet            Started container webapp

  ```
</details>
<details>
<summary>
Senario -
Create a Persistent Volume with the given specification.

Volume Name: pv-log
Storage: 100Mi
Access Modes: ReadWriteMany
Host Path: /pv/log
Reclaim Policy: Retain

</summary>

```bash

controlplane ~ ➜  cat pv.yaml 
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-log
spec:
  capacity:
    storage: 100Mi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: "/pv/log"

controlplane ~ ➜  k apply -f pv.yaml 
persistentvolume/pv-log created

controlplane ~ ➜  k describe pv pv-log 
Name:            pv-log
Labels:          <none>
Annotations:     <none>
Finalizers:      [kubernetes.io/pv-protection]
StorageClass:    
Status:          Available
Claim:           
Reclaim Policy:  Retain
Access Modes:    RWX
VolumeMode:      Filesystem
Capacity:        100Mi
Node Affinity:   <none>
Message:         
Source:
    Type:          HostPath (bare host directory volume)
    Path:          /pv/log
    HostPathType:  
Events:            <none>
  ```
</details>


<details>
<summary>
Senario -
Let us claim some of that storage for our application. Create a Persistent Volume Claim with the given specification.

Persistent Volume Claim: claim-log-1
Storage Request: 50Mi
Access Modes: ReadWriteOnce

</summary>

```bash


controlplane ~ ➜  cat pvc.yaml 
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: claim-log-1
spec:
  storageClassName: manual 
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 50Mi


controlplane ~ ➜  k apply -f pvc.yaml 
persistentvolumeclaim/claim-log-1 created

controlplane ~ ➜  k describe pvc claim-log-1 
Name:          claim-log-1
Namespace:     default
StorageClass:  manual
Status:        Pending
Volume:        
Labels:        <none>
Annotations:   <none>
Finalizers:    [kubernetes.io/pvc-protection]
Capacity:      
Access Modes:  
VolumeMode:    Filesystem
Used By:       <none>

  ```
</details>


<details>
<summary>
Senario -
What is the state of the Persistent Volume Claim?
What is the state of the Persistent Volume?
Why is the claim not bound to the available Persistent Volume?

```bash

controlplane ~ ➜  k describe pv pv-log 
Name:            pv-log
Labels:          <none>
Annotations:     <none>
Finalizers:      [kubernetes.io/pv-protection]
StorageClass:    
Status:          Available
Claim:           
Reclaim Policy:  Retain
Access Modes:    RWX
VolumeMode:      Filesystem
Capacity:        100Mi
Node Affinity:   <none>
Message:         
Source:
    Type:          HostPath (bare host directory volume)
    Path:          /pv/log
    HostPathType:  
Events:            <none>

controlplane ~ ➜  k describe pvc claim-log-1 
Name:          claim-log-1
Namespace:     default
StorageClass:  
Status:        Pending
Volume:        
Labels:        <none>
Annotations:   <none>
Finalizers:    [kubernetes.io/pvc-protection]
Capacity:      
Access Modes:  
VolumeMode:    Filesystem
Used By:       <none>


  ```

</summary>

```bash
access mode not matching

  ```
</details>


<details>
<summary>
Senario -
Update the Access Mode on the claim to bind it to the PV.
Delete and recreate the claim-log-1.

Persistent Volume Claim: claim-log-1
Storage Request: 50Mi
PVol: pv-log
Status: Bound

</summary>

```bash

controlplane ~ ➜  k describe pv pv-log 
Name:            pv-log
Labels:          <none>
Annotations:     pv.kubernetes.io/bound-by-controller: yes
Finalizers:      [kubernetes.io/pv-protection]
StorageClass:    manual
Status:          Bound
Claim:           default/claim-log-1
Reclaim Policy:  Retain
Access Modes:    RWX
VolumeMode:      Filesystem
Capacity:        100Mi
Node Affinity:   <none>
Message:         
Source:
    Type:          HostPath (bare host directory volume)
    Path:          /pv/log
    HostPathType:  
Events:            <none>

controlplane ~ ➜  k describe pvc claim-log-1 
Name:          claim-log-1
Namespace:     default
StorageClass:  manual
Status:        Bound
Volume:        pv-log
Labels:        <none>
Annotations:   pv.kubernetes.io/bind-completed: yes
               pv.kubernetes.io/bound-by-controller: yes
Finalizers:    [kubernetes.io/pvc-protection]
Capacity:      100Mi
Access Modes:  RWX
VolumeMode:    Filesystem
Used By:       <none>
Events:
  Type     Reason              Age                  From                         Message
  ----     ------              ----                 ----                         -------
  Warning  ProvisioningFailed  52s (x7 over 2m11s)  persistentvolume-controller  storageclass.storage.k8s.io "manual" not found

  ```
</details>


<details>
<summary>
Senario -
You requested for 50Mi, how much capacity is now available to the PVC?

</summary>

```bash
Capacity:      100Mi
  ```
</details>


<details>
<summary>
Senario -
Update the webapp pod to use the persistent volume claim as its storage.
Replace hostPath configured earlier with the newly created PersistentVolumeClaim.

Name: webapp
Image Name: kodekloud/event-simulator
Volume: PersistentVolumeClaim=claim-log-1
Volume Mount: /log

</summary>

```bash

controlplane ~ ➜  k replace --force -f pod.yaml 
pod/webapp replaced

controlplane ~ ➜  k describe pod webapp 
Name:             webapp
Namespace:        default
Priority:         0
Service Account:  default
Node:             controlplane/192.168.28.34
Start Time:       Thu, 09 Jan 2025 14:29:01 +0000
Labels:           run=webapp
Annotations:      <none>
Status:           Running
IP:               172.17.0.6
IPs:
  IP:  172.17.0.6
Containers:
  webapp:
    Container ID:   containerd://eb0c38a142b8f6c69b361e24a2e5478e84d6f1a0b7f975f5e9aca4a9d42cbe17
    Image:          kodekloud/event-simulator
    Image ID:       docker.io/kodekloud/event-simulator@sha256:1e3e9c72136bbc76c96dd98f29c04f298c3ae241c7d44e2bf70bcc209b030bf9
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Thu, 09 Jan 2025 14:29:02 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /log from claim-log (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-qg4ct (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  claim-log:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  claim-log-1
    ReadOnly:   false
  kube-api-access-qg4ct:
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
  Normal  Scheduled  8s    default-scheduler  Successfully assigned default/webapp to controlplane
  Normal  Pulling    7s    kubelet            Pulling image "kodekloud/event-simulator"
  Normal  Pulled     7s    kubelet            Successfully pulled image "kodekloud/event-simulator" in 146ms (146ms including waiting). Image size: 28855042 bytes.
  Normal  Created    7s    kubelet            Created container webapp
  Normal  Started    7s    kubelet            Started container webapp

controlplane ~ ➜  cat pod.yaml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: webapp
  name: webapp
spec:
  containers:
  - image: kodekloud/event-simulator
    name: webapp
    volumeMounts:
      - name: claim-log
        mountPath: /log
  volumes:
    - name: claim-log
      persistentVolumeClaim:
        claimName: claim-log-1

controlplane ~ ➜  
  ```
</details>




<details>
<summary>
Senario -
What would happen to the PV if the PVC was destroyed?
Try deleting the PVC and notice what happens.

If the command hangs, you can use CTRL + C to get back to the bash prompt OR check the status of the pvc from another terminal

stuck in terminating state

why it is not deleting 
</summary>
attched it to pod
</details>




<details>
<summary>
Senario -
delete pod and check status of pvc ? what is the curent status

</summary>

```bash

controlplane ~ ✖ k delete po webapp 
pod "webapp" deleted

controlplane ~ ➜  k describe pvc 
No resources found in default namespace.

controlplane ~ ➜  k get pvc
No resources found in default namespace.

  ```
</details>




<details>
<summary>
Senario -
What is the state of the Persistent Volume now?
</summary>

```bash

controlplane ~ ➜  k describe pv pv-log 
Name:            pv-log
Labels:          <none>
Annotations:     pv.kubernetes.io/bound-by-controller: yes
Finalizers:      [kubernetes.io/pv-protection]
StorageClass:    manual
Status:          Released
Claim:           default/claim-log-1
Reclaim Policy:  Retain
Access Modes:    RWX
VolumeMode:      Filesystem
Capacity:        100Mi
Node Affinity:   <none>
Message:         
Source:
    Type:          HostPath (bare host directory volume)
    Path:          /pv/log
    HostPathType:  
Events:            <none>

  ```
</details>


 Storage Class

<details>
<summary>
Senario -
How many StorageClasses exist in the cluster right now?

</summary>

```bash


controlplane ~ ➜  k get sc
NAME                        PROVISIONER                     RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
local-path (default)        rancher.io/local-path           Delete          WaitForFirstConsumer   false                  11m
local-storage               kubernetes.io/no-provisioner    Delete          WaitForFirstConsumer   false                  69s
portworx-io-priority-high   kubernetes.io/portworx-volume   Delete          Immediate              false                  69s


  ```
</details>




<details>
<summary>
Senario -
What is the name of the Storage Class that does not support dynamic volume provisioning?

</summary>

```bash
local
  ```
</details>




<details>
<summary>
Senario -
What is the Provisioner used for the storage class called portworx-io-priority-high?
Let's fix that. Create a new PersistentVolumeClaim by the name of local-pvc that should bind to the volume local-pv.


Inspect the pv local-pv for the specs.
</summary>

```bash

controlplane ~ ➜  cat pvc.yaml 
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name:  local-pvc
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 500Mi
  storageClassName: local-storage

  
controlplane ~ ➜  k describe pvc
Name:          local-pvc
Namespace:     default
StorageClass:  local-storage
Status:        Pending
Volume:        
Labels:        <none>
Annotations:   <none>
Finalizers:    [kubernetes.io/pvc-protection]
Capacity:      
Access Modes:  
VolumeMode:    Filesystem
Used By:       <none>
Events:
  Type    Reason                Age                   From                         Message
  ----    ------                ----                  ----                         -------
  Normal  WaitForFirstConsumer  10s (x14 over 3m25s)  persistentvolume-controller  waiting for first consumer to be created before binding


  ```
</details>




<details>
<summary>
Senario -
What is the status of the newly created Persistent Volume Claim?
</summary>

```bash
pending 


controlplane ~ ➜  k describe pvc
Name:          local-pvc
Namespace:     default
StorageClass:  local-storage
Status:        Pending
Volume:        
Labels:        <none>
Annotations:   <none>
Finalizers:    [kubernetes.io/pvc-protection]
Capacity:      
Access Modes:  
VolumeMode:    Filesystem
Used By:       <none>
  ```
</details>




<details>
<summary>
Senario -
Why is the PVC in a pending state despite making a valid request to claim the volume called local-pv?

Inspect the PVC events.

```bash

controlplane ~ ➜  k describe pvc
Name:          local-pvc
Namespace:     default
StorageClass:  local-storage
Status:        Pending
Volume:        
Labels:        <none>
Annotations:   <none>
Finalizers:    [kubernetes.io/pvc-protection]
Capacity:      
Access Modes:  
VolumeMode:    Filesystem
Used By:       <none>
Events:
  Type    Reason                Age                   From                         Message
  ----    ------                ----                  ----                         -------
  Normal  WaitForFirstConsumer  13s (x21 over 5m13s)  persistentvolume-controller  waiting for first consumer to be created before binding

  ```


</summary>

```bash
pod is not deployed yet
  ```
</details>

The Storage Class called local-storage makes use of VolumeBindingMode set to WaitForFirstConsumer. This will delay the binding and provisioning of a PersistentVolume until a Pod using the PersistentVolumeClaim is created.


<details>
<summary>
Senario -
Create a new pod called nginx with the image nginx:alpine. The Pod should make use of the PVC local-pvc and mount the volume at the path /var/www/html.
The PV local-pv should be in a bound state.

Pod created with the correct Image?
Pod uses PVC called local-pvc?
local-pv bound?
nginx pod running?
Volume mounted at the correct path?
</summary>

```bash
controlplane ~ ➜  k get pvc
NAME        STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS    VOLUMEATTRIBUTESCLASS   AGE
local-pvc   Pending                                      local-storage   <unset>                 10m


controlplane ~ ➜  cat pod.yaml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx:alpine
    name: nginx
    volumeMounts:
      - mountPath: "/var/www/html"
        name: data
  volumes:
    - name: data
      persistentVolumeClaim:
        claimName: local-pvc

controlplane ~ ➜  k apply -f pod.yaml 
pod/nginx created

controlplane ~ ➜  k describe pod nginx 
Name:             nginx
Namespace:        default
Priority:         0
Service Account:  default
Node:             controlplane/192.168.251.196
Start Time:       Thu, 09 Jan 2025 15:09:16 +0000
Labels:           run=nginx
Annotations:      <none>
Status:           Running
IP:               10.42.0.9
IPs:
  IP:  10.42.0.9
Containers:
  nginx:
    Container ID:   containerd://5d4347a1f717d254bd9fa52af12d83638648f70eba12f5d287cc0837db31793c
    Image:          nginx:alpine
    Image ID:       docker.io/library/nginx@sha256:9b460c5ee96333b43a92df16351e150af717ad6522101e52d05d255cd7241064
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Thu, 09 Jan 2025 15:09:18 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-9f68g (ro)
      /var/www/html from data (rw)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  data:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  local-pvc
    ReadOnly:   false
  kube-api-access-9f68g:
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
  Normal  Scheduled  5s    default-scheduler  Successfully assigned default/nginx to controlplane
  Normal  Pulling    5s    kubelet            Pulling image "nginx:alpine"
  Normal  Pulled     4s    kubelet            Successfully pulled image "nginx:alpine" in 1.183s (1.183s including waiting). Image size: 20534112 bytes.
  Normal  Created    4s    kubelet            Created container nginx
  Normal  Started    4s    kubelet            Started container nginx

controlplane ~ ➜  k get pvc
NAME        STATUS   VOLUME     CAPACITY   ACCESS MODES   STORAGECLASS    VOLUMEATTRIBUTESCLASS   AGE
local-pvc   Bound    local-pv   500Mi      RWO            local-storage   <unset>                 12m

controlplane ~ ➜  k get pv
NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM               STORAGECLASS    VOLUMEATTRIBUTESCLASS   REASON   AGE
local-pv   500Mi      RWO            Retain           Bound    default/local-pvc   local-storage   <unset>                          24m


  ```
</details>




<details>
<summary>
Senario -
What is the status of the local-pvc Persistent Volume Claim now?

</summary>

```bash
Bound

controlplane ~ ➜  k get pvc
NAME        STATUS   VOLUME     CAPACITY   ACCESS MODES   STORAGECLASS    VOLUMEATTRIBUTESCLASS   AGE
local-pvc   Bound    local-pv   500Mi      RWO            local-storage   <unset>                 12m

controlplane ~ ➜  k get pv
NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM               STORAGECLASS    VOLUMEATTRIBUTESCLASS   REASON   AGE
local-pv   500Mi      RWO            Retain           Bound    default/local-pvc   local-storage   <unset>                          24m

  ```
</details>





<details>
<summary>
Senario -
create sc 
</summary>

```bash


controlplane ~ ➜  cat sc.yaml 
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: delayed-volume-sc
provisioner:  kubernetes.io/no-provisioner
volumeBindingMode: WaitForFirstConsumer


Ctrolplane ~ ➜  k apply -f sc.yaml 
storageclass.storage.k8s.io/delayed-volume-sc created

controlplane ~ ➜  k get sc
NAME                        PROVISIONER                     RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
delayed-volume-sc           kubernetes.io/no-provisioner    Delete          WaitForFirstConsumer   false                  5s
local-path (default)        rancher.io/local-path           Delete          WaitForFirstConsumer   false                  40m
local-storage               kubernetes.io/no-provisioner    Delete          WaitForFirstConsumer   false                  30m
portworx-io-priority-high   kubernetes.io/portworx-volume   Delete          Immediate              false                  30m

controlplane ~ ➜  k describe  sc delayed-volume-sc 
Name:            delayed-volume-sc
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"storage.k8s.io/v1","kind":"StorageClass","metadata":{"annotations":{},"name":"delayed-volume-sc"},"provisioner":"kubernetes.io/no-provisioner","volumeBindingMode":"WaitForFirstConsumer"}

Provisioner:           kubernetes.io/no-provisioner
Parameters:            <none>
AllowVolumeExpansion:  <unset>
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     WaitForFirstConsumer
Events:                <none>

controlplane ~ ➜  
  ```
</details>



