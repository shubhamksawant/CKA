
<details>
<summary>
Senario -
What is the targetPort configured on the kubernetes service?
</summary>

```bash
6443
  ```
</details>




<details>
<summary>
Senario -
how many labels are configured on the kubernetes service?
</summary>

```bash
controlplane ~ ➜  k get svc --show-labels
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE     LABELS
kubernetes   ClusterIP   10.43.0.1    <none>        443/TCP   6m46s   component=apiserver,provider=kubernetes
  ```
</details>




<details>
<summary>
Senario -
How many Endpoints are attached on the kubernetes service?
</summary>

```bash

controlplane ~ ➜  k describe svc
Name:                     kubernetes
Namespace:                default
Labels:                   component=apiserver
                          provider=kubernetes
Annotations:              <none>
Selector:                 <none>
Type:                     ClusterIP
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.43.0.1
IPs:                      10.43.0.1
Port:                     https  443/TCP
TargetPort:               6443/TCP
Endpoints:                192.168.20.182:6443
Session Affinity:         None
Internal Traffic Policy:  Cluster
Events:                   <none>

  ```
</details>




<details>
<summary>
Senario -
the service-definition-1.yaml file.

Name: webapp-service
Type: NodePort
targetPort: 8080
port: 8080
nodePort: 30080
selector:
  name: simple-webapp

</summary>

```bash
---
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
  namespace: default
spec:
  ports:
  - nodePort: 30080
    port: 8080
    targetPort: 8080
  selector:
    name: simple-webapp
  type: NodePort
  ```
</details>

