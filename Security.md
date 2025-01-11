View Certificate Details

<details>
<summary>
Senario -
1. Identify the Certificate file of kube-apiserver
2. Identify the Certificate file used to authenticate kube-apiserver as a client to ETCD Server.
3. Identify the key used to authenticate kubeapi-server to the kubelet server.

```bash

controlplane ~ ➜  k describe po kube-apiserver-controlplane -n kube-system 
Name:                 kube-apiserver-controlplane
Namespace:            kube-system
Priority:             2000001000
Priority Class Name:  system-node-critical
Node:                 controlplane/192.168.63.150
Start Time:           Thu, 09 Jan 2025 05:03:45 +0000
Labels:               component=kube-apiserver
                      tier=control-plane
Annotations:          kubeadm.kubernetes.io/kube-apiserver.advertise-address.endpoint: 192.168.63.150:6443
                      kubernetes.io/config.hash: 6905487ec500c01b93fe29c336e78034
                      kubernetes.io/config.mirror: 6905487ec500c01b93fe29c336e78034
                      kubernetes.io/config.seen: 2025-01-09T05:03:45.533223902Z
                      kubernetes.io/config.source: file
Status:               Running
SeccompProfile:       RuntimeDefault
IP:                   192.168.63.150
IPs:
  IP:           192.168.63.150
Controlled By:  Node/controlplane
Containers:
  kube-apiserver:
    Container ID:  containerd://45b8aebe9e6a2a99fa137faeee220889a147f022490c13ad68738e1d7eb30dac
    Image:         registry.k8s.io/kube-apiserver:v1.31.0
    Image ID:      registry.k8s.io/kube-apiserver@sha256:470179274deb9dc3a81df55cfc24823ce153147d4ebf2ed649a4f271f51eaddf
    Port:          <none>
    Host Port:     <none>
    Command:
      kube-apiserver
      --advertise-address=192.168.63.150
      --allow-privileged=true
      --authorization-mode=Node,RBAC
      --client-ca-file=/etc/kubernetes/pki/ca.crt
      --enable-admission-plugins=NodeRestriction
      --enable-bootstrap-token-auth=true
      --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt
      --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
      --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key
      --etcd-servers=https://127.0.0.1:2379
      --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt
      --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key
      --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
      --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt
      --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key
      --requestheader-allowed-names=front-proxy-client
      --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt
      --requestheader-extra-headers-prefix=X-Remote-Extra-
      --requestheader-group-headers=X-Remote-Group
      --requestheader-username-headers=X-Remote-User
      --secure-port=6443
      --service-account-issuer=https://kubernetes.default.svc.cluster.local
      --service-account-key-file=/etc/kubernetes/pki/sa.pub
      --service-account-signing-key-file=/etc/kubernetes/pki/sa.key
      --service-cluster-ip-range=172.20.0.0/16
      --tls-cert-file=/etc/kubernetes/pki/apiserver.crt
      --tls-private-key-file=/etc/kubernetes/pki/apiserver.key
    State:          Running
      Started:      Thu, 09 Jan 2025 05:03:40 +0000
    Ready:          True
    Restart Count:  0
    Requests:
      cpu:        250m
    Liveness:     http-get https://192.168.63.150:6443/livez delay=10s timeout=15s period=10s #success=1 #failure=8
    Readiness:    http-get https://192.168.63.150:6443/readyz delay=0s timeout=15s period=1s #success=1 #failure=3
    Startup:      http-get https://192.168.63.150:6443/livez delay=10s timeout=15s period=10s #success=1 #failure=24
    Environment:  <none>
    Mounts:
      /etc/ca-certificates from etc-ca-certificates (ro)
      /etc/kubernetes/pki from k8s-certs (ro)
      /etc/ssl/certs from ca-certs (ro)
      /usr/local/share/ca-certificates from usr-local-share-ca-certificates (ro)
      /usr/share/ca-certificates from usr-share-ca-certificates (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  ca-certs:
    Type:          HostPath (bare host directory volume)
    Path:          /etc/ssl/certs
    HostPathType:  DirectoryOrCreate
  etc-ca-certificates:
    Type:          HostPath (bare host directory volume)
    Path:          /etc/ca-certificates
    HostPathType:  DirectoryOrCreate
  k8s-certs:
    Type:          HostPath (bare host directory volume)
    Path:          /etc/kubernetes/pki
    HostPathType:  DirectoryOrCreate
  usr-local-share-ca-certificates:
    Type:          HostPath (bare host directory volume)
    Path:          /usr/local/share/ca-certificates
    HostPathType:  DirectoryOrCreate
  usr-share-ca-certificates:
    Type:          HostPath (bare host directory volume)
    Path:          /usr/share/ca-certificates
    HostPathType:  DirectoryOrCreate
QoS Class:         Burstable
Node-Selectors:    <none>
Tolerations:       :NoExecute op=Exists
Events:            <none>

  ```

</summary>

```bash
1.  --tls-cert-file=/etc/kubernetes/pki/apiserver.crt
2.  --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
3.  --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key


  ```
</details>


<details>
<summary>
Senario -
 1. Identify the ETCD Server Certificate used to host ETCD server.
2. Identify the ETCD Server CA Root Certificate used to serve ETCD Server.
ETCD can have its own CA. So this may be a different CA certificate than the one used by kube-api server.

```bash

controlplane ~ ➜  k describe po etcd-controlplane -n kube-system 
Name:                 etcd-controlplane
Namespace:            kube-system
Priority:             2000001000
Priority Class Name:  system-node-critical
Node:                 controlplane/192.168.63.150
Start Time:           Thu, 09 Jan 2025 05:03:45 +0000
Labels:               component=etcd
                      tier=control-plane
Annotations:          kubeadm.kubernetes.io/etcd.advertise-client-urls: https://192.168.63.150:2379
                      kubernetes.io/config.hash: a846b9c04df3fe5594a853fe11307d12
                      kubernetes.io/config.mirror: a846b9c04df3fe5594a853fe11307d12
                      kubernetes.io/config.seen: 2025-01-09T05:03:45.533219799Z
                      kubernetes.io/config.source: file
Status:               Running
SeccompProfile:       RuntimeDefault
IP:                   192.168.63.150
IPs:
  IP:           192.168.63.150
Controlled By:  Node/controlplane
Containers:
  etcd:
    Container ID:  containerd://c126855f2f90066365a8b3f63befc9574eb4416375d662e5628930f1d6cebdec
    Image:         registry.k8s.io/etcd:3.5.15-0
    Image ID:      registry.k8s.io/etcd@sha256:a6dc63e6e8cfa0307d7851762fa6b629afb18f28d8aa3fab5a6e91b4af60026a
    Port:          <none>
    Host Port:     <none>
    Command:
      etcd
      --advertise-client-urls=https://192.168.63.150:2379
      --cert-file=/etc/kubernetes/pki/etcd/server.crt
      --client-cert-auth=true
      --data-dir=/var/lib/etcd
      --experimental-initial-corrupt-check=true
      --experimental-watch-progress-notify-interval=5s
      --initial-advertise-peer-urls=https://192.168.63.150:2380
      --initial-cluster=controlplane=https://192.168.63.150:2380
      --key-file=/etc/kubernetes/pki/etcd/server.key
      --listen-client-urls=https://127.0.0.1:2379,https://192.168.63.150:2379
      --listen-metrics-urls=http://127.0.0.1:2381
      --listen-peer-urls=https://192.168.63.150:2380
      --name=controlplane
      --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
      --peer-client-cert-auth=true
      --peer-key-file=/etc/kubernetes/pki/etcd/peer.key
      --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
      --snapshot-count=10000
      --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
    State:          Running
      Started:      Thu, 09 Jan 2025 05:03:40 +0000
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
Events:            <none>

  ```

</summary>

```bash
1.     --cert-file=/etc/kubernetes/pki/etcd/server.crt
2.       --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt



  ```
</details>


<details>
<summary>
Senario -
1. What is the Common Name (CN) configured on the Kube API Server Certificate?

OpenSSL Syntax: openssl x509 -in file-path.crt -text -noout

2. What is the name of the CA who issued the Kube API Server Certificate?
3. What is the  Alternative Name configured on the ETCD Server certificate?
4. How long, from the issued date, is the Kube-API Server Certificate valid for?
File: /etc/kubernetes/pki/apiserver.crt


</summary>

```bash

controlplane ~ ✖ openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 7941048620600766719 (0x6e3445a59f03b0ff)
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = kubernetes
        Validity
            Not Before: Jan  9 04:58:34 2025 GMT
            Not After : Jan  9 05:03:34 2026 GMT
        Subject: CN = kube-apiserver
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:ad:80:54:00:83:48:02:a0:7e:77:fe:eb:79:d5:
                    5f:0d:ec:26:ba:2c:a1:24:37:10:a6:16:04:83:0a:
                    8d:03:b1:83:bd:5c:3f:57:94:ee:68:e5:91:10:0a:
                    92:54:9e:a1:13:56:e6:b6:75:9a:4a:99:5a:ea:22:
                    62:1b:70:f4:be:16:9a:8f:95:6d:8a:d5:47:28:de:
                    66:b5:d8:1d:e9:c9:f2:55:43:20:35:af:55:91:42:
                    d1:9d:92:22:4f:4b:23:0c:7e:f6:3a:46:02:eb:1e:
                    20:50:bf:a6:9a:50:25:50:a7:eb:d2:20:d5:a7:64:
                    53:dd:fd:e6:65:b1:97:5d:d7:d9:d6:c6:9b:2c:bc:
                    22:ce:62:01:c5:a5:0d:73:bc:0c:0b:8a:6d:40:0b:
                    d8:b5:9f:c7:2c:a7:ed:08:cb:ec:83:01:0f:50:e5:
                    78:ac:cb:cc:a5:82:d8:47:f2:d4:26:de:73:9a:3d:
                    65:23:45:d8:a6:91:4d:39:c7:3d:a4:f2:86:b1:a1:
                    12:89:4a:4e:84:b1:2b:5b:2c:73:ae:0a:52:ad:15:
                    c4:be:05:ae:ce:5d:3c:0c:78:c9:70:d6:4e:76:72:
                    36:55:12:dd:42:c4:80:97:43:2e:f4:ee:e2:3d:66:
                    46:5c:9d:9e:8b:fa:3d:9c:e4:9b:51:79:f0:6a:41:
                    e6:05
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            X509v3 Extended Key Usage: 
                TLS Web Server Authentication
            X509v3 Basic Constraints: critical
                CA:FALSE
            X509v3 Authority Key Identifier: 
                EE:8D:C2:16:5D:55:A2:49:A3:9B:EC:8F:79:50:67:8B:87:55:E6:F0
            X509v3 Subject Alternative Name: 
                DNS:controlplane, DNS:kubernetes, DNS:kubernetes.default, DNS:kubernetes.default.svc, DNS:kubernetes.default.svc.cluster.local, IP Address:172.20.0.1, IP Address:192.168.63.150
    Signature Algorithm: sha256WithRSAEncryption
    Signature Value:
        25:f7:9f:10:dd:da:58:16:54:f0:d6:02:19:4e:c0:64:f2:60:
        7c:58:eb:bd:33:ed:ec:25:3e:72:c4:3f:9c:a8:b6:dc:dc:31:
        b1:f9:4c:59:75:10:2a:39:73:1c:68:1e:9b:60:19:10:45:67:
        a2:62:b6:f2:b9:dd:06:a6:fc:c9:03:22:46:3f:c6:f2:98:4f:
        d5:f3:35:c5:03:c6:0f:2c:a9:a3:9c:e5:a8:9a:8b:a8:a1:12:
        a8:cf:1b:c0:75:47:6a:4b:b4:27:5e:d3:b7:9e:52:f3:2c:ec:
        b9:5f:9b:93:89:4f:04:e3:f7:26:84:45:e7:7a:bc:67:5f:68:
        40:04:4e:c5:66:b1:39:a3:7b:1f:94:2f:27:dc:5d:04:a0:98:
        d2:83:a4:fe:6c:16:a3:a8:16:3f:8a:7f:85:1c:f3:d8:9e:7d:
        d5:75:a7:42:87:8a:8c:54:ba:08:73:15:2f:3f:a7:bf:95:e9:
        b4:81:82:bb:92:63:8e:2b:5d:75:a4:d4:7e:31:1e:b2:95:a7:
        ca:34:39:03:34:25:13:32:10:99:44:e7:be:23:79:16:31:06:
        9b:d4:3b:ba:fe:12:28:db:18:25:87:9d:b5:3a:5a:99:7d:f6:
        da:8e:8a:ac:ac:a2:26:69:55:cb:87:fe:66:45:78:14:c8:49:
        48:c4:f0:41

  ```
</details>



<details>
<summary>
Senario -
1. What is the Common Name (CN) configured on the ETCD Server certificate?

OpenSSL Syntax: openssl x509 -in file-path.crt -text -noout

</summary>

```bash

controlplane ~ ➜  openssl x509 -in /etc/kubernetes/pki/etcd/server.crt  -text -noout
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 6136176725576157592 (0x5528143896fc8998)
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = etcd-ca
        Validity
            Not Before: Jan  9 04:58:34 2025 GMT
            Not After : Jan  9 05:03:34 2026 GMT
        Subject: CN = controlplane
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:a4:01:71:91:92:14:ed:fc:3c:20:97:01:73:aa:
                    fc:ca:48:0c:67:5e:0d:4d:04:6f:c9:e1:23:8f:66:
                    a1:a5:35:6a:54:a9:6b:71:bf:65:4f:91:c1:d0:15:
                    b9:bb:74:24:db:2b:82:eb:04:da:fd:88:9e:4b:29:
                    e4:d6:cd:f5:cc:74:ba:de:cf:33:a4:42:2f:e0:97:
                    b4:09:35:c5:ae:c1:ac:64:c4:7e:42:76:cd:7c:91:
                    d6:2c:5e:06:be:1e:63:36:4e:f5:44:71:71:52:89:
                    3b:42:47:ea:8a:1a:60:0e:df:b8:53:62:2d:37:b9:
                    ff:03:ee:11:4a:1d:87:40:bf:60:ca:f3:cb:5d:89:
                    b0:f2:ef:ea:74:f9:3f:90:af:f8:51:e8:de:cd:9b:
                    e6:49:58:0f:1d:ca:0f:de:56:ed:18:19:da:a8:18:
                    63:8f:04:f9:d7:7c:61:87:94:3f:2a:bf:f5:07:a8:
                    d7:a7:b9:0a:69:f5:d6:46:a4:2d:9e:78:75:aa:c5:
                    29:ed:db:38:01:d6:f5:2d:bc:8a:37:6e:82:05:25:
                    b7:4b:b4:dc:3b:58:33:83:3f:f1:e4:ec:0a:c8:75:
                    3d:f8:f2:5a:7f:b2:63:30:12:0e:98:f5:84:89:90:
                    12:8b:c1:32:bc:86:6b:91:7e:d5:48:a0:04:cb:06:
                    e4:2f
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            X509v3 Extended Key Usage: 
                TLS Web Server Authentication, TLS Web Client Authentication
            X509v3 Basic Constraints: critical
                CA:FALSE
            X509v3 Authority Key Identifier: 
                1B:ED:18:F2:7F:4C:18:90:29:02:19:E3:2E:A3:D8:1F:DD:8B:13:6D
            X509v3 Subject Alternative Name: 
                DNS:controlplane, DNS:localhost, IP Address:192.168.63.150, IP Address:127.0.0.1, IP Address:0:0:0:0:0:0:0:1
    Signature Algorithm: sha256WithRSAEncryption
    Signature Value:
        78:8f:2f:c7:93:6a:3f:06:98:08:fc:8b:0c:73:cd:47:4d:9f:
        4e:10:23:47:e5:8d:8c:1b:c9:ca:83:0b:70:58:e3:45:48:86:
        9b:90:ba:c9:29:24:3f:24:7e:43:f5:8e:db:00:0a:80:57:f3:
        f9:a9:3c:f7:70:1c:f5:5f:10:8c:e1:82:fc:fb:9c:a0:c6:a9:
        b1:64:79:00:4c:af:e1:0d:d6:26:d8:ef:83:c6:a2:c3:02:3b:
        0c:cc:9f:79:12:5e:80:42:ed:d7:8b:e8:1d:8b:b8:93:ef:ca:
        ac:de:39:7a:ec:57:ce:6b:a7:6e:77:e3:68:ee:ea:38:17:bc:
        8e:c5:0f:79:9e:8e:b4:84:1d:69:8b:46:97:37:7c:f0:18:4e:
        61:3d:92:83:cb:05:c2:4e:66:21:a4:64:05:9a:8a:b2:27:62:
        3a:79:1f:f9:58:23:ca:be:02:01:6c:d5:24:8d:c1:67:58:d0:
        93:8b:08:1c:26:0f:d8:65:53:9b:20:c6:73:12:d4:08:90:a6:
        cc:c5:eb:13:a0:34:85:82:f2:95:a4:f1:59:cb:e0:e8:3d:91:
        79:6c:f9:ea:47:a3:a2:15:0a:fd:cc:c9:c5:1b:b5:5b:32:63:
        ca:62:8d:a9:b8:79:7a:67:3b:1a:df:46:a9:50:44:f3:02:d1:
        04:0a:a4:bf

  ```
</details>
<details>
<summary>
Senario -

1. How long, from the issued date, is the Root CA Certificate valid for?
File: /etc/kubernetes/pki/ca.crt

</summary>

```bash

controlplane ~ ➜  openssl x509 -in /etc/kubernetes/pki/ca.crt  -text -noout
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 3014547662820683033 (0x29d5d320a8e92d19)
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = kubernetes
        Validity
            Not Before: Jan  9 04:58:34 2025 GMT
            Not After : Jan  7 05:03:34 2035 GMT
        Subject: CN = kubernetes
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:ab:e9:04:ba:9a:7f:04:22:c6:c2:d7:8d:40:93:
                    bf:55:16:0a:6e:e7:70:25:ac:c5:30:7d:7f:22:58:
                    da:80:3b:75:5a:82:30:94:1a:b4:4f:5c:0b:a6:16:
                    8a:bc:54:b0:c4:d3:a2:34:a7:ca:e0:b0:d0:96:a5:
                    e9:66:77:ff:85:9b:2f:b2:83:7c:f4:c7:1d:09:e4:
                    e4:2e:89:18:de:b4:63:ec:ac:19:46:91:68:7b:15:
                    cf:42:4e:a0:d7:d6:23:82:0a:5b:2c:1f:c0:7f:b6:
                    d7:14:1f:c8:e9:7a:bb:01:ae:3c:7b:bf:6c:38:2f:
                    ae:98:65:da:79:41:10:cf:05:86:14:7a:de:51:35:
                    99:7b:c9:20:c6:47:bd:d1:5e:0a:18:a1:6c:20:be:
                    4c:8b:c6:e0:b6:49:cf:98:11:ff:a6:1d:75:30:74:
                    87:39:e0:2c:35:f9:f7:28:b0:f5:e5:6f:40:e3:1d:
                    f9:43:cf:cd:4b:49:24:5a:7b:9a:17:0d:0a:86:03:
                    db:29:4d:44:b5:ad:63:d2:86:66:f0:f0:ca:50:13:
                    c9:61:53:b4:99:d8:c9:ec:65:72:35:05:2f:44:14:
                    af:7f:5d:d4:fb:59:c3:07:0c:87:0b:25:39:23:fc:
                    a3:5e:d6:f6:47:cb:ad:d7:75:0e:dd:07:ef:42:24:
                    6e:e9
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment, Certificate Sign
            X509v3 Basic Constraints: critical
                CA:TRUE
            X509v3 Subject Key Identifier: 
                EE:8D:C2:16:5D:55:A2:49:A3:9B:EC:8F:79:50:67:8B:87:55:E6:F0
            X509v3 Subject Alternative Name: 
                DNS:kubernetes
    Signature Algorithm: sha256WithRSAEncryption
    Signature Value:
        0a:14:9c:05:46:cb:74:a6:85:c8:91:8e:ab:b7:48:dd:18:4f:
        8b:04:75:2f:96:78:8f:26:16:5a:19:51:5b:de:77:06:f6:fa:
        67:d2:e3:5c:2f:e9:0d:63:ba:cb:5c:62:17:ef:f7:9e:8e:60:
        95:25:cf:88:53:ab:53:7d:8b:61:4e:09:ba:9a:43:4b:e1:33:
        79:af:54:75:d7:78:27:8d:86:8f:07:86:84:bc:c9:8c:0b:27:
        c2:35:01:e3:d2:e2:2a:35:ff:26:0c:67:d3:5f:5b:3c:6a:3e:
        66:99:8b:47:7f:c8:1c:8e:e9:35:da:df:ba:2d:46:64:3c:cd:
        41:66:c3:95:0c:03:11:ae:62:ab:e0:f7:cf:f3:f1:c3:a0:56:
        26:c9:c1:56:b4:54:86:49:24:4e:8b:43:57:8c:cc:01:c9:02:
        29:33:f2:48:cb:d0:c0:0f:8c:dc:2a:ed:8b:72:e9:a0:31:c7:
        87:70:34:5c:a8:d1:14:f7:87:b2:d1:f7:13:9c:95:85:2d:e6:
        f0:93:b6:cc:0c:46:7f:f2:b0:f9:75:54:d5:70:f1:36:86:38:
        73:0a:09:80:b0:3f:b7:e3:13:9b:c5:86:56:f7:62:4f:4b:03:
        8a:d9:fa:e0:a5:9d:5b:6c:17:01:8f:01:38:3f:b2:1b:7b:30:
        27:90:b7:72
  ```
</details>

<details>
<summary>
Senario -
Kubectl suddenly stops responding to your commands. Check it out! Someone recently modified the /etc/kubernetes/manifests/etcd.yaml file
You are asked to investigate and fix the issue. Once you fix the issue wait for sometime for kubectl to respond. Check the logs of the ETCD container.

```bash

controlplane ~ ➜  cat /etc/kubernetes/manifests/etcd.yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    kubeadm.kubernetes.io/etcd.advertise-client-urls: https://192.168.63.150:2379
  creationTimestamp: null
  labels:
    component: etcd
    tier: control-plane
  name: etcd
  namespace: kube-system
spec:
  containers:
  - command:
    - etcd
    - --advertise-client-urls=https://192.168.63.150:2379
    - --cert-file=/etc/kubernetes/pki/etcd/server-certificate.crt
    - --client-cert-auth=true
    - --data-dir=/var/lib/etcd
    - --experimental-initial-corrupt-check=true
    - --experimental-watch-progress-notify-interval=5s
    - --initial-advertise-peer-urls=https://192.168.63.150:2380
    - --initial-cluster=controlplane=https://192.168.63.150:2380
    - --key-file=/etc/kubernetes/pki/etcd/server.key
    - --listen-client-urls=https://127.0.0.1:2379,https://192.168.63.150:2379
    - --listen-metrics-urls=http://127.0.0.1:2381
    - --listen-peer-urls=https://192.168.63.150:2380
    - --name=controlplane
    - --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
    - --peer-client-cert-auth=true
    - --peer-key-file=/etc/kubernetes/pki/etcd/peer.key
    - --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
    - --snapshot-count=10000
    - --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
    image: registry.k8s.io/etcd:3.5.15-0
    imagePullPolicy: IfNotPresent
    livenessProbe:
      failureThreshold: 8
      httpGet:
        host: 127.0.0.1
        path: /livez
        port: 2381
        scheme: HTTP
      initialDelaySeconds: 10
      periodSeconds: 10
      timeoutSeconds: 15
    name: etcd
    readinessProbe:
      failureThreshold: 3
      httpGet:
        host: 127.0.0.1
        path: /readyz
        port: 2381
        scheme: HTTP
      periodSeconds: 1
      timeoutSeconds: 15
    resources:
      requests:
        cpu: 100m
        memory: 100Mi
    startupProbe:
      failureThreshold: 24
      httpGet:
        host: 127.0.0.1
        path: /readyz
        port: 2381
        scheme: HTTP
      initialDelaySeconds: 10
      periodSeconds: 10
      timeoutSeconds: 15
    volumeMounts:
    - mountPath: /var/lib/etcd
      name: etcd-data
    - mountPath: /etc/kubernetes/pki/etcd
      name: etcd-certs
  hostNetwork: true
  priority: 2000001000
  priorityClassName: system-node-critical
  securityContext:
    seccompProfile:
      type: RuntimeDefault
  volumes:
  - hostPath:
      path: /etc/kubernetes/pki/etcd
      type: DirectoryOrCreate
    name: etcd-certs
  - hostPath:
      path: /var/lib/etcd
      type: DirectoryOrCreate
    name: etcd-data
status: {}
  ```

</summary>

```bash

controlplane ~ ✖ crictl ps 
CONTAINER           IMAGE               CREATED             STATE               NAME                      ATTEMPT             POD ID              POD
6ac8c4b19d0be       1766f54c897f0       4 minutes ago       Running             kube-scheduler            1                   43f1984cf8e57       kube-scheduler-controlplane
6aeb510c7d996       045733566833c       4 minutes ago       Running             kube-controller-manager   1                   6334217d10bc7       kube-controller-manager-controlplane
3ff3bfba28de4       ead0a4a53df89       8 minutes ago       Running             coredns                   0                   4339468662830       coredns-77d6fd4654-wwglk
8dc07047635fb       ead0a4a53df89       9 minutes ago       Running             coredns                   0                   27cf30355e57f       coredns-77d6fd4654-ct8q4
72cd277ffb7a0       01cdfa8dd262f       9 minutes ago       Running             kube-flannel              0                   482ae5be9e269       kube-flannel-ds-8rdfg
b8a1426bc6354       ad83b2ca7b09e       9 minutes ago       Running             kube-proxy                0                   72c641a8de02a       kube-proxy-sssf8



controlplane ~ ➜  crictl ps -a
CONTAINER           IMAGE               CREATED             STATE               NAME                      ATTEMPT             POD ID              POD
2a53f76693216       2e96e5913fc06       9 seconds ago       Exited              etcd                      5                   23f01fb945c77       etcd-controlplane
27da99e0c14c6       604f5db92eaa8       53 seconds ago      Exited              kube-apiserver            5                   5556c2ce18f30       kube-apiserver-controlplane
6ac8c4b19d0be       1766f54c897f0       5 minutes ago       Running             kube-scheduler            1                   43f1984cf8e57       kube-scheduler-controlplane
6aeb510c7d996       045733566833c       5 minutes ago       Running             kube-controller-manager   1                   6334217d10bc7       kube-controller-manager-controlplane
3ff3bfba28de4       ead0a4a53df89       10 minutes ago      Running             coredns                   0                   4339468662830       coredns-77d6fd4654-wwglk
8dc07047635fb       ead0a4a53df89       10 minutes ago      Running             coredns                   0                   27cf30355e57f       coredns-77d6fd4654-ct8q4
72cd277ffb7a0       01cdfa8dd262f       10 minutes ago      Running             kube-flannel              0                   482ae5be9e269       kube-flannel-ds-8rdfg
c2088c0ffcf8c       01cdfa8dd262f       10 minutes ago      Exited              install-cni               0                   482ae5be9e269       kube-flannel-ds-8rdfg
e30e1d8255358       a55d1bad692b7       10 minutes ago      Exited              install-cni-plugin        0                   482ae5be9e269       kube-flannel-ds-8rdfg
b8a1426bc6354       ad83b2ca7b09e       10 minutes ago      Running             kube-proxy                0                   72c641a8de02a       kube-proxy-sssf8
ef5416cf1626f       045733566833c       10 minutes ago      Exited              kube-controller-manager   0                   6334217d10bc7       kube-controller-manager-controlplane
b9d66c0076504       1766f54c897f0       10 minutes ago      Exited              kube-scheduler            0                   43f1984cf8e57       kube-scheduler-controlplane


controlplane ~ ➜  crictl logs --tail 5 27da99e0c14c6
W0109 06:15:40.595807       1 logging.go:55] [core] [Channel #1 SubChannel #3]grpc: addrConn.createTransport failed to connect to {Addr: "127.0.0.1:2379", ServerName: "127.0.0.1:2379", }. Err: connection error: desc = "transport: Error while dialing: dial tcp 127.0.0.1:2379: connect: connection refused"
W0109 06:15:45.192662       1 logging.go:55] [core] [Channel #5 SubChannel #6]grpc: addrConn.createTransport failed to connect to {Addr: "127.0.0.1:2379", ServerName: "127.0.0.1:2379", }. Err: connection error: desc = "transport: Error while dialing: dial tcp 127.0.0.1:2379: connect: connection refused"
W0109 06:15:47.178609       1 logging.go:55] [core] [Channel #2 SubChannel #4]grpc: addrConn.createTransport failed to connect to {Addr: "127.0.0.1:2379", ServerName: "127.0.0.1:2379", }. Err: connection error: desc = "transport: Error while dialing: dial tcp 127.0.0.1:2379: connect: connection refused"
W0109 06:15:48.449550       1 logging.go:55] [core] [Channel #1 SubChannel #3]grpc: addrConn.createTransport failed to connect to {Addr: "127.0.0.1:2379", ServerName: "127.0.0.1:2379", }. Err: connection error: desc = "transport: Error while dialing: dial tcp 127.0.0.1:2379: connect: connection refused"
F0109 06:15:50.732347       1 instance.go:225] Error creating leases: error creating storage factory: context deadline exceeded


controlplane ~ ➜  ls /etc/kubernetes/pki/etcd/
ca.crt  healthcheck-client.crt  peer.crt  server.crt
ca.key  healthcheck-client.key  peer.key  server.key


controlplane ~ ✖ cat /etc/kubernetes/manifests/etcd.yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    kubeadm.kubernetes.io/etcd.advertise-client-urls: https://192.168.63.150:2379
  creationTimestamp: null
  labels:
    component: etcd
    tier: control-plane
  name: etcd
  namespace: kube-system
spec:
  containers:
  - command:
    - etcd
    - --advertise-client-urls=https://192.168.63.150:2379
    - --cert-file=/etc/kubernetes/pki/etcd/server.crt
    - --client-cert-auth=true
    - --data-dir=/var/lib/etcd
    - --experimental-initial-corrupt-check=true
    - --experimental-watch-progress-notify-interval=5s
    - --initial-advertise-peer-urls=https://192.168.63.150:2380
    - --initial-cluster=controlplane=https://192.168.63.150:2380
    - --key-file=/etc/kubernetes/pki/etcd/server.key
    - --listen-client-urls=https://127.0.0.1:2379,https://192.168.63.150:2379
    - --listen-metrics-urls=http://127.0.0.1:2381
    - --listen-peer-urls=https://192.168.63.150:2380
    - --name=controlplane
    - --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
    - --peer-client-cert-auth=true
    - --peer-key-file=/etc/kubernetes/pki/etcd/peer.key
    - --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
    - --snapshot-count=10000
    - --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
    image: registry.k8s.io/etcd:3.5.15-0
    imagePullPolicy: IfNotPresent
    livenessProbe:
      failureThreshold: 8
      httpGet:
        host: 127.0.0.1
        path: /livez
        port: 2381
        scheme: HTTP
      initialDelaySeconds: 10
      periodSeconds: 10
      timeoutSeconds: 15
    name: etcd
    readinessProbe:
      failureThreshold: 3
      httpGet:
        host: 127.0.0.1
        path: /readyz
        port: 2381
        scheme: HTTP
      periodSeconds: 1
      timeoutSeconds: 15
    resources:
      requests:
        cpu: 100m
        memory: 100Mi
    startupProbe:
      failureThreshold: 24
      httpGet:
        host: 127.0.0.1
        path: /readyz
        port: 2381
        scheme: HTTP
      initialDelaySeconds: 10
      periodSeconds: 10
      timeoutSeconds: 15
    volumeMounts:
    - mountPath: /var/lib/etcd
      name: etcd-data
    - mountPath: /etc/kubernetes/pki/etcd
      name: etcd-certs
  hostNetwork: true
  priority: 2000001000
  priorityClassName: system-node-critical
  securityContext:
    seccompProfile:
      type: RuntimeDefault
  volumes:
  - hostPath:
      path: /etc/kubernetes/pki/etcd
      type: DirectoryOrCreate
    name: etcd-certs
  - hostPath:
      path: /var/lib/etcd
      type: DirectoryOrCreate
    name: etcd-data
status: {}



The certificate file used here is incorrect. It is set to /etc/kubernetes/pki/etcd/server-certificate.crt which does not exist. As we saw in the previous questions the correct path should be /etc/kubernetes/pki/etcd/server.crt.

root@controlplane:~# ls -l /etc/kubernetes/pki/etcd/server* | grep .crt
-rw-r--r-- 1 root root 1188 May 20 00:41 /etc/kubernetes/pki/etcd/server.crt

controlplane ~ ➜  ls /etc/kubernetes/pki/etcd/
ca.crt  healthcheck-client.crt  peer.crt  server.crt
ca.key  healthcheck-client.key  peer.key  server.key


Update the YAML file with the correct certificate path and wait for the ETCD pod to be recreated. wait for the kube-apiserver to get to a Ready state.


NOTE: It may take a few minutes for the kubectl commands to work again so please be patient.


  ```
</details>
<details>
<summary>
Senario -
The kube-api server stopped again! Check it out. Inspect the kube-api server logs and identify the root cause and fix the issue.


Run crictl ps -a command to identify the kube-api server container. Run crictl logs container-id command to view the logs.

```bash

controlplane ~ ✖ cat /etc/kubernetes/manifests/kube-apiserver.yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    kubeadm.kubernetes.io/kube-apiserver.advertise-address.endpoint: 192.168.63.150:6443
  creationTimestamp: null
  labels:
    component: kube-apiserver
    tier: control-plane
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
  - command:
    - kube-apiserver
    - --advertise-address=192.168.63.150
    - --allow-privileged=true
    - --authorization-mode=Node,RBAC
    - --client-ca-file=/etc/kubernetes/pki/ca.crt
    - --enable-admission-plugins=NodeRestriction
    - --enable-bootstrap-token-auth=true
    - --etcd-cafile=/etc/kubernetes/pki/ca.crt
    - --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
    - --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key
    - --etcd-servers=https://127.0.0.1:2379
    - --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt
    - --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key
    - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
    - --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt
    - --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key
    - --requestheader-allowed-names=front-proxy-client
    - --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt
    - --requestheader-extra-headers-prefix=X-Remote-Extra-
    - --requestheader-group-headers=X-Remote-Group
    - --requestheader-username-headers=X-Remote-User
    - --secure-port=6443
    - --service-account-issuer=https://kubernetes.default.svc.cluster.local
    - --service-account-key-file=/etc/kubernetes/pki/sa.pub
    - --service-account-signing-key-file=/etc/kubernetes/pki/sa.key
    - --service-cluster-ip-range=172.20.0.0/16
    - --tls-cert-file=/etc/kubernetes/pki/apiserver.crt
    - --tls-private-key-file=/etc/kubernetes/pki/apiserver.key
    image: registry.k8s.io/kube-apiserver:v1.31.0
    imagePullPolicy: IfNotPresent
    livenessProbe:
      failureThreshold: 8
      httpGet:
        host: 192.168.63.150
        path: /livez
        port: 6443
        scheme: HTTPS
      initialDelaySeconds: 10
      periodSeconds: 10
      timeoutSeconds: 15
    name: kube-apiserver
    readinessProbe:
      failureThreshold: 3
      httpGet:
        host: 192.168.63.150
        path: /readyz
        port: 6443
        scheme: HTTPS
      periodSeconds: 1
      timeoutSeconds: 15
    resources:
      requests:
        cpu: 250m
    startupProbe:
      failureThreshold: 24
      httpGet:
        host: 192.168.63.150
        path: /livez
        port: 6443
        scheme: HTTPS
      initialDelaySeconds: 10
      periodSeconds: 10
      timeoutSeconds: 15
    volumeMounts:
    - mountPath: /etc/ssl/certs
      name: ca-certs
      readOnly: true
    - mountPath: /etc/ca-certificates
      name: etc-ca-certificates
      readOnly: true
    - mountPath: /etc/kubernetes/pki
      name: k8s-certs
      readOnly: true
    - mountPath: /usr/local/share/ca-certificates
      name: usr-local-share-ca-certificates
      readOnly: true
    - mountPath: /usr/share/ca-certificates
      name: usr-share-ca-certificates
      readOnly: true
  hostNetwork: true
  priority: 2000001000
  priorityClassName: system-node-critical
  securityContext:
    seccompProfile:
      type: RuntimeDefault
  volumes:
  - hostPath:
      path: /etc/ssl/certs
      type: DirectoryOrCreate
    name: ca-certs
  - hostPath:
      path: /etc/ca-certificates
      type: DirectoryOrCreate
    name: etc-ca-certificates
  - hostPath:
      path: /etc/kubernetes/pki
      type: DirectoryOrCreate
    name: k8s-certs
  - hostPath:
      path: /usr/local/share/ca-certificates
      type: DirectoryOrCreate
    name: usr-local-share-ca-certificates
  - hostPath:
      path: /usr/share/ca-certificates
      type: DirectoryOrCreate
    name: usr-share-ca-certificates
status: {}

  ```

</summary>

```bash

controlplane ~ ✖ crictl ps -a
CONTAINER           IMAGE               CREATED             STATE               NAME                      ATTEMPT             POD ID              POD
d3bf5d5b730a8       604f5db92eaa8       55 seconds ago      Exited              kube-apiserver            5                   de0f08a15ccde       kube-apiserver-controlplane
7f475f17d7d44       1766f54c897f0       5 minutes ago       Running             kube-scheduler            2                   7e0527b8ebeb0       kube-scheduler-controlplane
2f806b47ffe44       045733566833c       5 minutes ago       Running             kube-controller-manager   2                   ec7e3e9ccda73       kube-controller-manager-controlplane
09befb5f7c4ab       2e96e5913fc06       7 minutes ago       Running             etcd                      0                   823476b87cdc6       etcd-controlplane
10592ad80df2f       1766f54c897f0       10 minutes ago      Exited              kube-scheduler            1                   7e0527b8ebeb0       kube-scheduler-controlplane
a509de83e019d       045733566833c       10 minutes ago      Exited              kube-controller-manager   1                   ec7e3e9ccda73       kube-controller-manager-controlplane
71c5db6ecd879       ead0a4a53df89       59 minutes ago      Running             coredns                   0                   1f7071dd3275e       coredns-77d6fd4654-wk848
776a8a2d30030       ead0a4a53df89       59 minutes ago      Running             coredns                   0                   c9aa38d5bad4f       coredns-77d6fd4654-g5zrb
0af7d7133ea1c       01cdfa8dd262f       59 minutes ago      Running             kube-flannel              0                   2083b986445e8       kube-flannel-ds-tr45c
b3a4d96eacbf7       01cdfa8dd262f       59 minutes ago      Exited              install-cni               0                   2083b986445e8       kube-flannel-ds-tr45c
b11d343af54bb       a55d1bad692b7       59 minutes ago      Exited              install-cni-plugin        0                   2083b986445e8       kube-flannel-ds-tr45c
5feb7b11932f2       ad83b2ca7b09e       59 minutes ago      Running             kube-proxy                0                   8c4983115bf53       kube-proxy-nfmh2

controlplane ~ ➜  crictl ps -a | grep kube-apiserver
d3bf5d5b730a8       604f5db92eaa8       About a minute ago   Exited              kube-apiserver            5                   de0f08a15ccde       kube-apiserver-controlplane

controlplane ~ ➜   crictl logs --tail=10 d3bf5d5b730a8
W0109 06:02:57.447710       1 logging.go:55] [core] [Channel #5 SubChannel #6]grpc: addrConn.createTransport failed to connect to {Addr: "127.0.0.1:2379", ServerName: "127.0.0.1:2379", }. Err: connection error: desc = "transport: authentication handshake failed: tls: failed to verify certificate: x509: certificate signed by unknown authority"
W0109 06:02:57.566040       1 logging.go:55] [core] [Channel #1 SubChannel #3]grpc: addrConn.createTransport failed to connect to {Addr: "127.0.0.1:2379", ServerName: "127.0.0.1:2379", }. Err: connection error: desc = "transport: authentication handshake failed: tls: failed to verify certificate: x509: certificate signed by unknown authority"
W0109 06:02:58.289660       1 logging.go:55] [core] [Channel #2 SubChannel #4]grpc: addrConn.createTransport failed to connect to {Addr: "127.0.0.1:2379", ServerName: "127.0.0.1:2379", }. Err: connection error: desc = "transport: authentication handshake failed: tls: failed to verify certificate: x509: certificate signed by unknown authority"
W0109 06:03:01.926332       1 logging.go:55] [core] [Channel #5 SubChannel #6]grpc: addrConn.createTransport failed to connect to {Addr: "127.0.0.1:2379", ServerName: "127.0.0.1:2379", }. Err: connection error: desc = "transport: authentication handshake failed: tls: failed to verify certificate: x509: certificate signed by unknown authority"
W0109 06:03:02.418014       1 logging.go:55] [core] [Channel #1 SubChannel #3]grpc: addrConn.createTransport failed to connect to {Addr: "127.0.0.1:2379", ServerName: "127.0.0.1:2379", }. Err: connection error: desc = "transport: authentication handshake failed: tls: failed to verify certificate: x509: certificate signed by unknown authority"
W0109 06:03:02.521408       1 logging.go:55] [core] [Channel #2 SubChannel #4]grpc: addrConn.createTransport failed to connect to {Addr: "127.0.0.1:2379", ServerName: "127.0.0.1:2379", }. Err: connection error: desc = "transport: authentication handshake failed: tls: failed to verify certificate: x509: certificate signed by unknown authority"
W0109 06:03:08.696001       1 logging.go:55] [core] [Channel #1 SubChannel #3]grpc: addrConn.createTransport failed to connect to {Addr: "127.0.0.1:2379", ServerName: "127.0.0.1:2379", }. Err: connection error: desc = "transport: authentication handshake failed: tls: failed to verify certificate: x509: certificate signed by unknown authority"
W0109 06:03:09.183504       1 logging.go:55] [core] [Channel #5 SubChannel #6]grpc: addrConn.createTransport failed to connect to {Addr: "127.0.0.1:2379", ServerName: "127.0.0.1:2379", }. Err: connection error: desc = "transport: authentication handshake failed: tls: failed to verify certificate: x509: certificate signed by unknown authority"
W0109 06:03:10.171117       1 logging.go:55] [core] [Channel #2 SubChannel #4]grpc: addrConn.createTransport failed to connect to {Addr: "127.0.0.1:2379", ServerName: "127.0.0.1:2379", }. Err: connection error: desc = "transport: authentication handshake failed: tls: failed to verify certificate: x509: certificate signed by unknown authority"
F0109 06:03:12.424273       1 instance.go:225] Error creating leases: error creating storage factory: context deadline exceeded


If we now inspect the logs of this exited container, we would see the following errors:

root@controlplane:~# crictl logs --tail=2 1fb242055cff8  
W0916 14:19:44.771920       1 clientconn.go:1331] [core] grpc: addrConn.createTransport failed to connect to {127.0.0.1:2379 127.0.0.1 <nil> 0 <nil>}. Err: connection error: desc = "transport: authentication handshake failed: x509: certificate signed by unknown authority". Reconnecting...
E0916 14:19:48.689303       1 run.go:74] "command failed" err="context deadline exceeded"
This indicates an issue with the ETCD CA certificate used by the kube-apiserver. Correct it to use the file /etc/kubernetes/pki/etcd/ca.crt.

Once the YAML file has been saved, wait for the kube-apiserver pod to be Ready. This can take a couple of minutes.


controlplane ~ ➜  cat  /etc/kubernetes/manifests/etcd.yaml 
apiVersion: v1
kind: Pod
metadata:
  annotations:
    kubeadm.kubernetes.io/etcd.advertise-client-urls: https://192.168.233.139:2379
  creationTimestamp: null
  labels:
    component: etcd
    tier: control-plane
  name: etcd
  namespace: kube-system
spec:
  containers:
  - command:
    - etcd
    - --advertise-client-urls=https://192.168.233.139:2379
    - --cert-file=/etc/kubernetes/pki/etcd/server.crt
    - --client-cert-auth=true
    - --data-dir=/var/lib/etcd
    - --experimental-initial-corrupt-check=true
    - --experimental-watch-progress-notify-interval=5s
    - --initial-advertise-peer-urls=https://192.168.233.139:2380
    - --initial-cluster=controlplane=https://192.168.233.139:2380
    - --key-file=/etc/kubernetes/pki/etcd/server.key
    - --listen-client-urls=https://127.0.0.1:2379,https://192.168.233.139:2379
    - --listen-metrics-urls=http://127.0.0.1:2381
    - --listen-peer-urls=https://192.168.233.139:2380
    - --name=controlplane
    - --peer-cert-file=/etc/kubernetes/pki/etcd/peer.crt
    - --peer-client-cert-auth=true
    - --peer-key-file=/etc/kubernetes/pki/etcd/peer.key
    - --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
    - --snapshot-count=10000
    - --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
    image: registry.k8s.io/etcd:3.5.15-0


controlplane ~ ➜  cat  /etc/kubernetes/manifests/kube-apiserver.yaml 
apiVersion: v1
kind: Pod
metadata:
  annotations:
    kubeadm.kubernetes.io/kube-apiserver.advertise-address.endpoint: 192.168.233.139:6443
  creationTimestamp: null
  labels:
    component: kube-apiserver
    tier: control-plane
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
  - command:
    - kube-apiserver
    - --advertise-address=192.168.233.139
    - --allow-privileged=true
    - --authorization-mode=Node,RBAC
    - --client-ca-file=/etc/kubernetes/pki/ca.crt
    - --enable-admission-plugins=NodeRestriction
    - --enable-bootstrap-token-auth=true
    - --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt
    - --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt
    - --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key
    - --etcd-servers=https://127.0.0.1:2379
    - --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt
    - --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key
    - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
    - --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt
    - --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key
    - --requestheader-allowed-names=front-proxy-client
    - --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt
    - --requestheader-extra-headers-prefix=X-Remote-Extra-
    - --requestheader-group-headers=X-Remote-Group
    - --requestheader-username-headers=X-Remote-User
    - --secure-port=6443
    - --service-account-issuer=https://kubernetes.default.svc.cluster.local
    - --service-account-key-file=/etc/kubernetes/pki/sa.pub
    - --service-account-signing-key-file=/etc/kubernetes/pki/sa.key
    - --service-cluster-ip-range=172.20.0.0/16
    - --tls-cert-file=/etc/kubernetes/pki/apiserver.crt
    - --tls-private-key-file=/etc/kubernetes/pki/apiserver.key
    image: registry.k8s.io/kube-apiserver:v1.31.0
    imagePullPolicy: IfNotPresent
    livenessProbe:
      failureThreshold: 8
      httpGet:
        host: 192.168.233.139
        path: /livez
        port: 6443
        scheme: HTTPS
      initialDelaySeconds: 10
      periodSeconds: 10
      timeoutSeconds: 15
    name: kube-apiserver
    readinessProbe:
      failureThreshold: 3
      httpGet:
        host: 192.168.233.139
        path: /readyz
        port: 6443
        scheme: HTTPS
      periodSeconds: 1
      timeoutSeconds: 15
    resources:
      requests:
        cpu: 250m
    startupProbe:
      failureThreshold: 24
      httpGet:
        host: 192.168.233.139
        path: /livez
        port: 6443
        scheme: HTTPS
      initialDelaySeconds: 10
      periodSeconds: 10
      timeoutSeconds: 15
    volumeMounts:
    - mountPath: /etc/ssl/certs
      name: ca-certs
      readOnly: true
    - mountPath: /etc/ca-certificates
      name: etc-ca-certificates
      readOnly: true
    - mountPath: /etc/kubernetes/pki
      name: k8s-certs
      readOnly: true
    - mountPath: /usr/local/share/ca-certificates
      name: usr-local-share-ca-certificates
      readOnly: true
    - mountPath: /usr/share/ca-certificates
      name: usr-share-ca-certificates
      readOnly: true
  hostNetwork: true
  priority: 2000001000
  priorityClassName: system-node-critical
  securityContext:
    seccompProfile:
      type: RuntimeDefault
  volumes:
  - hostPath:
      path: /etc/ssl/certs
      type: DirectoryOrCreate
    name: ca-certs
  - hostPath:
      path: /etc/ca-certificates
      type: DirectoryOrCreate
    name: etc-ca-certificates
  - hostPath:
      path: /etc/kubernetes/pki
      type: DirectoryOrCreate
    name: k8s-certs
  - hostPath:
      path: /usr/local/share/ca-certificates
      type: DirectoryOrCreate
    name: usr-local-share-ca-certificates
  - hostPath:
      path: /usr/share/ca-certificates
      type: DirectoryOrCreate
    name: usr-share-ca-certificates
status: {}

Every 2.0s: crictl logs --tail 5 3a0a207701592         controlplane: Thu Jan  9 06:57:01 2025

I0109 06:46:01.645083       1 cache.go:32] Waiting for caches to sync for autoregister contro
ller
I0109 06:46:01.645088       1 cache.go:39] Caches are synced for autoregister controller
I0109 06:46:01.646358       1 controller.go:615] quota admission added evaluator for: leases.
coordination.k8s.io
I0109 06:46:01.647436       1 handler_discovery.go:450] Starting ResourceDiscoveryManager
I0109 06:46:02.390065       1 storage_scheduling.go:111] all system priority classes are crea
ted successfully or already exist.


Every 2.0s: crictl ps -a                                                     controlplane: Thu Jan  9 06:57:20 2025

CONTAINER           IMAGE               CREATED             STATE               NAME                      ATTEMPT
           POD ID              POD
3a0a207701592       604f5db92eaa8       11 minutes ago      Running             kube-apiserver            0
           16b95d1c87edd       kube-apiserver-controlplane
e3



  ```
</details>


KubeConfig

<details>
<summary>
Senario -
default kubeconfig file location?

</summary>

```bash
controlplane ~ ✖ cat  .kube/config 
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCVENDQWUyZ0F3SUJBZ0lJYmxJVHlEZ0pXVFV3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TlRBeE1Ea3hNVEUwTXpSYUZ3MHpOVEF4TURjeE1URTVNelJhTUJVeApFekFSQmdOVkJBTVRDbXQxWW1WeWJtVjBaWE13Z2d
  ```
</details>


<details>
<summary>
Senario -
How many clusters are defined in the default kubeconfig file?

How many Users are defined in the default kubeconfig file?

How many contexts are defined in the default kubeconfig file?

What is the user configured in the current context?

What is the name of the cluster configured in the default kubeconfig file?

```bash
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCVENDQWUyZ0F3SUJBZ0lJYmxJVHlEZ0pXVFV3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TlRBeE1Ea3hNVEUwTXpSYUZ3MHpOVEF4TURjeE1URTVNelJhTUJVeApFekFSQmdOVkJBTVRDbXQxWW1WeWJtVjBaWE13Z2dFaU1BMEdDU3FHU0liM0RRRUJBUVVBQTRJQkR3QXdnZ0VLCkFvSUJBUURueXVzTFdleGZZcHVKdWRSWUQ0VUlwWGlCZzVFVHJZOGV0bU9GbVdxdGIvQjRDR2FXSkpRdERmb1oKTm8zSUdrVlM3SmlGcDdGSU9vSStabXVaRzh3L01TT3lxV1ozTXQ0cG5CRG9aZnlMQSs5TVQ3dENRc2Z1REtONApWZ2JwQlQ5ZVhJWjNUSVVqRGh4L3FybDBORDNSMWVjdVl1dExjMDBLclFIRUtwT01BU3A0Si91dWFLVk1nZWZMCmd1V0F6Y1BCaW5zaS9kVzdWUTUrN2lFaFVaVUQyQlpBRXB0VUN4ajdESFVmenF0RFZHSFZzUk96SUM0RzEyVDUKUzlmUFZqN1h2OTVUVGZwenRMYmdGL1EzS3o3MHNaajZKZGtLU0lzS0lRT3JDRnFtTVFzY0ZvdVVLenl4V0MyZApRc3RyWVZvVG9URXBjejliN0Qvb3pzWTdxcHNGQWdNQkFBR2pXVEJYTUE0R0ExVWREd0VCL3dRRUF3SUNwREFQCkJnTlZIUk1CQWY4RUJUQURBUUgvTUIwR0ExVWREZ1FXQkJSWW8yV0syeGYxZFZEdFB2MFlBSGtYdUtIQjJ6QVYKQmdOVkhSRUVEakFNZ2dwcmRXSmxjbTVsZEdWek1BMEdDU3FHU0liM0RRRUJDd1VBQTRJQkFRQ05TcEJvYlRUSQppWlp1cHZDL1hDWHQ0emIwMjB5SVpSTDF2SFl3ZHJGamVBcXlHN2oyNFhWaVVHUDBhRFhyQldxdURaRjBuY0IzCnhscGgyQjByMzhNaWluQ2EzRnBqTTZoNDVDam9xU1J1MXZKNC9aaFFMWHZqSHRQS2t6VmdRTVBRY3ltdkZ4ZUgKb2NEVk8wMkRuN2hJb0hnQnJ4b1VZeExNZTF4WUtIN2U1WnovdDE2ZmJWMzI0anVsd0pscG1kNmRYRlpUTUIzRApSU216c3VENEFnRXM3RDd2d1RMN2NvbThvUzZFbm9SbmRhTFgvN1ZkMkFGaE93V1o2aVE1cTQ2Q2MrQ0FrQ1hICjlQWkNiSnAxVlYxcmo1bVdPaVd0cGNrZjc1Mi9sVTJIeDhZZDFpdHhLNnBTcC9rZUx2VG45YS8yU2s4TmZsZDIKNWoyOCtzMGxnYXVVCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    server: https://controlplane:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURLVENDQWhHZ0F3SUJBZ0lJV1ltRUNPcXQxL1F3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TlRBeE1Ea3hNVEUwTXpSYUZ3MHlOakF4TURreE1URTVNelJhTUR3eApIekFkQmdOVkJBb1RGbXQxWW1WaFpHMDZZMngxYzNSbGNpMWhaRzFwYm5NeEdUQVhCZ05WQkFNVEVHdDFZbVZ5CmJtVjBaWE10WVdSdGFXNHdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUtBb0lCQVFEWkhoZU8KNEwvRVdVaEsyNGNFcktwUnBSMnA5SVIzeG5VTDRmb3FVMWNNY05wVzdFdmd1cEtlRVA1bWZyTy9zWStYK1BrMApyVTc1TDdnZFhJMWYxVW9RcmQ3ZXc3MXJhNDNWL2VPZ0ZrNlh5V0JPbFpJTTZjcmM4eFJPMzJwWWxEUjFUT0I4CnVUL3ZaY1pGYnowS1ZsbjVoeXZuMVFGT1Z0VzJaY3J1NWNBdW9GczROQVliczdPdVFRWVRMU2prNlk5QXRtdjAKQ0IvTkZOMWIyTjVZUVByclVZT1hOM2JUUnpQdHVSSEcyYzJtMjFrazkwUU1UK3hNNFZWVURRUGZod2EwdnAxRwp0S3BaSlM3a2xMUzljb3RRZ0o2T3BDQk9GdHF0eEVBNlYzL1VWcEtuWDBnOGNDdHNCWEsrMkpzVzF1d0txT0hiCkFQSXU3WE02SDhZd1lOd3hBZ01CQUFHalZqQlVNQTRHQTFVZER3RUIvd1FFQXdJRm9EQVRCZ05WSFNVRUREQUsKQmdnckJnRUZCUWNEQWpBTUJnTlZIUk1CQWY4RUFqQUFNQjhHQTFVZEl3UVlNQmFBRkZpalpZcmJGL1YxVU8wKwovUmdBZVJlNG9jSGJNQTBHQ1NxR1NJYjNEUUVCQ3dVQUE0SUJBUUNOYXBJazlKZ3hmL21zNHVianQ2N1d6WXdPCmRjY25lQWxDU2x5RTd0Yytud0V3QnF1RUZKWElsUWFMYTNLZmlRM2pmbFhiTnRkOG96WDFvTTRxQVFDcU54UmgKUENqZWh2MDB2MldlS0pHSmxJZmlqeGZpcHJGV1c5eEFIU05McEpsbTQ3aWtkSzFQVk53RzIzcXcySmZYdFc1OApEbGhwY2V5RE1WeDc1S2VGME1GZ0JPWFZjQjBCWFlYTlh6LzBDeWdnaGVuZERxdW5LbGVwdHZtSzRaRkgxRFNJCkxQdXE2aVdqellmSWkxWFVvVzlYU2hLNzJpLzE4RVdjZXVpYVVidFlEcTFsSE9HR2JZSGVMbGgrdEE2NjJvUzcKVmdteW9TbFFwc3BJUnVBbGlMVmh6Si9SMVhPRWZaajVKcm0wWDUrdlJLc05tZG5VZkptZHNaTDR2Y3hUCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb2dJQkFBS0NBUUVBMlI0WGp1Qy94RmxJU3R1SEJLeXFVYVVkcWZTRWQ4WjFDK0g2S2xOWERIRGFWdXhMCjRMcVNuaEQrWm42enY3R1BsL2o1TksxTytTKzRIVnlOWDlWS0VLM2Uzc085YTJ1TjFmM2pvQlpPbDhsZ1RwV1MKRE9uSzNQTVVUdDlxV0pRMGRVemdmTGsvNzJYR1JXODlDbFpaK1ljcjU5VUJUbGJWdG1YSzd1WEFMcUJiT0RRRwpHN096cmtFR0V5MG81T21QUUxacjlBZ2Z6UlRkVzlqZVdFRDY2MUdEbHpkMjAwY3o3YmtSeHRuTnB0dFpKUGRFCkRFL3NUT0ZWVkEwRDM0Y0d0TDZkUnJTcVdTVXU1SlMwdlhLTFVJQ2VqcVFnVGhiYXJjUkFPbGQvMUZhU3AxOUkKUEhBcmJBVnl2dGliRnRic0Nxamgyd0R5THUxek9oL0dNR0RjTVFJREFRQUJBb0lCQUhXY2ZHeDBTTTBuQkNjdwp4Zkg5U29IUnJNRDg2S2t0UXdnOTBGTVBOQzlVRHFKWmZEenZOQzhWeTRsZ0J5VEhIWjdZU3I2RFJ5MXE4UlJPCnAzS0xTb1RBZXVNM0t5dStabVhPRTNrTHZHVXl1ODIxN2hpNnFKNkp1cTJERHN1TzlOKzUxajBaNkkxWkYwOVQKazhBK3lKeUg0WHZEQWVNYTNNZmlXa3lRL2tzQ3FSUU5vVFZHaERSNFhlSnk3RFFIUlkvdUx2Qng4VFVvTWZHNwp5cHB1dmtvbm5QcnlGZTdKK0xTL2VqeFhERVdLNmwxWjI5VDFGdFBkRE1aMTFac08yLzFpM2tFUGJvd0JGRll1CnhyNVV6L3c0bWEyMURtTFR4T1JkcUNPbGtrb0J2Yk0ya0Q4aGljc0Q0RVV5R083SmljbGgxS0VjMENmNEtOWDAKVVdwb2VyRUNnWUVBNzBveHJHTHVQajZOU1dzSVdFdlFDdzd4OTFPcGZFY0hhVnlQOHBsdEovZ0ovTEZxaVFyNwo3QWw5OWVtUHExY3RGVDYreDd0MTdaZHAxVFRybDNKSEdyanRHbGkxbEVkT3RhOGVtMnBtNEs2UzhvYlhiTE81Ckl2YzZvTFhsalRQaHVNODArcXJZYUN1UXRtdTFLaG45UnQzT1o4cWtNUFdCTXhwL2lIMUNwVzBDZ1lFQTZFZUYKc09BWVlleGM1RHVDc09RdllFWk9iUUhNbWxCOFlWUUoxQzlzKzFqeEVwQ1NqSWNWeVJ2T0pIOVR6bzdFMjdDMgpneGFkbFF1ajBLaUdtZnl1ZnZVekhhVEJnNFgxMHlFQXNqU1UvN3k5L0djY00vQnIrNjc5cVBDbU14akVBSEEzClFncFowZmtJWlFGbCsvQkRjdk5pVGVrMTA5aEh3NDhNMmZWTlMxVUNnWUFKdXdHa0dlM0lPREQwb0ZhWmlKT3cKTDI1bGVWQjJEa2xkM25hNERjY2pqZkdTQmdEb0M3dE0wT0gvWXYwRm9hamlxVWVHeGJlNDdxUk9HRFhRVmNSUQpjTjZyYUpvUnFvaW4yTFFzNjdtOVB6d1lncVUxOVdUODZBTEpVRHhDYnE2cUhqcndTb3c4U0RFOVNjbjNLVkZrClZWYTBYOGduZVlSRWVMcGc2WDhSVFFLQmdBVUNEY0xiZ2dzU2NvcEZNak1vS0ZGV0Vyak04aUZkbk9Eb1UvSHMKTjloUGVac2gzTmNFenBad2NBaGZhbW9vcjl2Z3VuNm5EU3k2ZklTeUxYekNjdXl2MHJOUDZ4QXY0algwSHpTNApaNElBQ3NTWFlJZEtKcm5kY3dDaWQ2TGdaTkZ4bWU5bGs3d3dOMXNlSlpuQisySk9GdytBK0xVaGs2S0d3Z3NzCis4YWxBb0dBZkNjRjJsT2t0cHRQZEIyUmRMVlhNVmJIMEVtUFhPaVJubWJWUlRYU1dEdzZSbDdDVThNR0ExanUKNGo1cnMyR2NDcXNGRkpoRWoycVNhakVQcFBMZy9PbERQNVNvbXdSekE5MU1KSkVIWDBReDBIbFdMNDcraFhaSwpCMGtsUWxZd01USXhxbGJJRDVQWW9VamN0dWJlWGFyc2JmT2RsbUlhVDl2Unl3MzZpdlU9Ci0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==

controlplane ~ ➜  
  ```

</summary>

```bash
1
1
1
kubernetes-admin
kubernetes

  ```
</details>


<details>
<summary>
Senario -
A new kubeconfig file named my-kube-config is created. It is placed in the /root directory. How many clusters are defined in that kubeconfig file?

How many contexts are configured in the my-kube-config file?

What user is configured in the research context?

What is the name of the client-certificate file configured for the aws-user?

What is the current context set to in the my-kube-config file?


```bash

controlplane ~ ➜  cat my-kube-config 
apiVersion: v1
kind: Config

clusters:
- name: production
  cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://controlplane:6443

- name: development
  cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://controlplane:6443

- name: kubernetes-on-aws
  cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://controlplane:6443

- name: test-cluster-1
  cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://controlplane:6443

contexts:
- name: test-user@development
  context:
    cluster: development
    user: test-user

- name: aws-user@kubernetes-on-aws
  context:
    cluster: kubernetes-on-aws
    user: aws-user

- name: test-user@production
  context:
    cluster: production
    user: test-user

- name: research
  context:
    cluster: test-cluster-1
    user: dev-user

users:
- name: test-user
  user:
    client-certificate: /etc/kubernetes/pki/users/test-user/test-user.crt
    client-key: /etc/kubernetes/pki/users/test-user/test-user.key
- name: dev-user
  user:
    client-certificate: /etc/kubernetes/pki/users/dev-user/developer-user.crt
    client-key: /etc/kubernetes/pki/users/dev-user/dev-user.key
- name: aws-user
  user:
    client-certificate: /etc/kubernetes/pki/users/aws-user/aws-user.crt
    client-key: /etc/kubernetes/pki/users/aws-user/aws-user.key

current-context: test-user@development
preferences: {}


  ```

</summary>

```bash
4
4
user: dev-user
client-certificate: /etc/kubernetes/pki/users/aws-user/aws-user.crt
current-context: test-user@development


  ```
</details>


<details>
<summary>
Senario -
12 / 14
I would like to use the dev-user to access test-cluster-1. Set the current context to the right one so I can do that.

Once the right context is identified, use the kubectl config use-context command.

</summary>

```bash


controlplane ~ ➜  k config use-context research --kubeconfig /root/my-kube-config
Switched to context "research".

controlplane ~ ➜  cat my-kube-config 
apiVersion: v1
clusters:
- cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://controlplane:6443
  name: development
- cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://controlplane:6443
  name: kubernetes-on-aws
- cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://controlplane:6443
  name: production
- cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://controlplane:6443
  name: test-cluster-1
contexts:
- context:
    cluster: kubernetes-on-aws
    user: aws-user
  name: aws-user@kubernetes-on-aws
- context:
    cluster: test-cluster-1
    user: dev-user
  name: research
- context:
    cluster: development
    user: test-user
  name: test-user@development
- context:
    cluster: production
    user: test-user
  name: test-user@production
current-context: research
kind: Config
preferences: {}
users:
- name: aws-user
  user:
    client-certificate: /etc/kubernetes/pki/users/aws-user/aws-user.crt
    client-key: /etc/kubernetes/pki/users/aws-user/aws-user.key
- name: dev-user
  user:
    client-certificate: /etc/kubernetes/pki/users/dev-user/developer-user.crt
    client-key: /etc/kubernetes/pki/users/dev-user/dev-user.key
- name: test-user
  user:
    client-certificate: /etc/kubernetes/pki/users/test-user/test-user.crt
    client-key: /etc/kubernetes/pki/users/test-user/test-user.key



  ```
</details>


<details>
<summary>
Senario -
We don't want to specify the kubeconfig file option on each kubectl command.
Set the my-kube-config file as the default kubeconfig file and make it persistent across all sessions without overwriting the existing ~/.kube/config. Ensure any configuration changes persist across reboots and new shell sessions.
Note: Don't forget to source the configuration file to take effect in the existing session. Example:

source ~/.bashrc


</summary>

```bash

controlplane ~ ➜  export KUBECONFIG=/root/my-kube-config


controlplane ~ ➜  cat ~/.bashrc 
# ~/.bashrc: executed by bash(1) for non-login shells.
# see /usr/share/doc/bash/examples/startup-files (in the package bash-doc)
# for examples

# If not running interactively, don't do anything
[ -z "$PS1" ] && return

# don't put duplicate lines in the history. See bash(1) for more options
# ... or force ignoredups and ignorespace
HISTCONTROL=ignoredups:ignorespace

# append to the history file, don't overwrite it
shopt -s histappend

# for setting history length see HISTSIZE and HISTFILESIZE in bash(1)
HISTSIZE=1000
HISTFILESIZE=2000

# check the window size after each command and, if necessary,
# update the values of LINES and COLUMNS.
shopt -s checkwinsize

# make less more friendly for non-text input files, see lesspipe(1)
[ -x /usr/bin/lesspipe ] && eval "$(SHELL=/bin/sh lesspipe)"

# set variable identifying the chroot you work in (used in the prompt below)
if [ -z "$debian_chroot" ] && [ -r /etc/debian_chroot ]; then
    debian_chroot=$(cat /etc/debian_chroot)
fi

# set a fancy prompt (non-color, unless we know we "want" color)
case "$TERM" in
    xterm-color) color_prompt=yes;;
esac

# uncomment for a colored prompt, if the terminal has the capability; turned
# off by default to not distract the user: the focus in a terminal window
# should be on the output of commands, not on the prompt
#force_color_prompt=yes

if [ -n "$force_color_prompt" ]; then
    if [ -x /usr/bin/tput ] && tput setaf 1 >&/dev/null; then
        # We have color support; assume it's compliant with Ecma-48
        # (ISO/IEC-6429). (Lack of such support is extremely rare, and such
        # a case would tend to support setf rather than setaf.)
        color_prompt=yes
    else
        color_prompt=
    fi
fi

if [ "$color_prompt" = yes ]; then
    PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
else
    PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
fi
unset color_prompt force_color_prompt

# If this is an xterm set the title to user@host:dir
case "$TERM" in
xterm*|rxvt*)
    PS1="\[\e]0;${debian_chroot:+($debian_chroot)}\u@\h: \w\a\]$PS1"
    ;;
*)
    ;;
esac

# enable color support of ls and also add handy aliases
if [ -x /usr/bin/dircolors ]; then
    test -r ~/.dircolors && eval "$(dircolors -b ~/.dircolors)" || eval "$(dircolors -b)"
    alias ls='ls --color=auto'
    #alias dir='dir --color=auto'
    #alias vdir='vdir --color=auto'

    alias grep='grep --color=auto'
    alias fgrep='fgrep --color=auto'
    alias egrep='egrep --color=auto'
fi

# some more ls aliases
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'

# Alias definitions.
# You may want to put all your additions into a separate file like
# ~/.bash_aliases, instead of adding them here directly.
# See /usr/share/doc/bash-doc/examples in the bash-doc package.

if [ -f ~/.bash_aliases ]; then
    . ~/.bash_aliases
fi

# enable programmable completion features (you don't need to enable
# this, if it's already enabled in /etc/bash.bashrc and /etc/profile
# sources /etc/bash.bashrc).
#if [ -f /etc/bash_completion ] && ! shopt -oq posix; then
#    . /etc/bash_completion
#fi
eval "$(starship init bash)"
cd /root
source /etc/profile.d/bash_completion.sh
source <(kubectl completion bash)
alias k=kubectl
alias KUBECONFIG=/root/my-kube-config
complete -F __start_kubectl k


controlplane ~ ➜  source ~/.bashrc

  ```
</details>


<details>
<summary>
Senario -
With the current-context set to research, we are trying to access the cluster. However something seems to be wrong. Identify and fix the issue.
Try running the kubectl get pods command and look for the error. All users certificates are stored at /etc/kubernetes/pki/users.

</summary>

```bash

controlplane ~ ➜  k get all
error: unable to read client-cert /etc/kubernetes/pki/users/dev-user/developer-user.crt for dev-user due to open /etc/kubernetes/pki/users/dev-user/developer-user.crt: no such file or directory

controlplane ~ ✖ ls /etc/kubernetes/pki/users/
aws-user  dev-user  test-user

controlplane ~ ➜  ls /etc/kubernetes/pki/users/dev-user/
dev-user.crt  dev-user.csr  dev-user.key

controlplane ~ ➜  vi my-kube-config 

controlplane ~ ➜  cat my-kube-config 
apiVersion: v1
clusters:
- cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://controlplane:6443
  name: development
- cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://controlplane:6443
  name: kubernetes-on-aws
- cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://controlplane:6443
  name: production
- cluster:
    certificate-authority: /etc/kubernetes/pki/ca.crt
    server: https://controlplane:6443
  name: test-cluster-1
contexts:
- context:
    cluster: kubernetes-on-aws
    user: aws-user
  name: aws-user@kubernetes-on-aws
- context:
    cluster: test-cluster-1
    user: dev-user
  name: research
- context:
    cluster: development
    user: test-user
  name: test-user@development
- context:
    cluster: production
    user: test-user
  name: test-user@production
current-context: research
kind: Config
preferences: {}
users:
- name: aws-user
  user:
    client-certificate: /etc/kubernetes/pki/users/aws-user/aws-user.crt
    client-key: /etc/kubernetes/pki/users/aws-user/aws-user.key
- name: dev-user
  user:
    client-certificate: /etc/kubernetes/pki/users/dev-user/dev-user.crt
    client-key: /etc/kubernetes/pki/users/dev-user/dev-user.key
- name: test-user
  user:
    client-certificate: /etc/kubernetes/pki/users/test-user/test-user.crt
    client-key: /etc/kubernetes/pki/users/test-user/test-user.key

controlplane ~ ➜  k get all
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   172.20.0.1   <none>        443/TCP   38m


  ```
</details>

 Certificates API

<details>
<summary>
Senario -
A new member akshay joined our team. He requires access to our cluster. The Certificate Signing Request is at the /root location.

Inspect it

```bash

controlplane ~ ➜  ls
akshay.csr  akshay.key

  ```

</summary>

```bash
cat akshay.csr | base64 -w 0 
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZqQ0NBVDRDQVFBd0VURVBNQTBHQTFVRUF3d0dZV3R6YUdGNU1JSUJJakFOQmdrcWhraUc5dzBCQVFFRgpBQU9DQVE4QU1JSUJDZ0tDQVFFQTZZWGNmbTNPeGpCak1RNjByNDQzRG03cncvN0hGOW9rY3NGTE0ybEorQm50CjVyNEk0eVl1ZVF2TFM0bU1Vc1ZmbGdHUVVWNXhOL3F0RGEzWUhqNWhVY090SmVrVVNyS0pLQm5mbVV6d0l3dUwKM3VMc1hNTllHRzRHUyswejJXY1JIQ3JxSHFEVmJWMDc4TmtDQndLVjRZR1JCb09LVWdwdjFvQzJYQk9iaTV2dgp1aWlBWkZEQUJmNll4czB1VVhiaTYrTC9QMnJrZFB4NDJITklWczJsRjBHRkFadVdoR2U2RmRSZlRDMFRUeTZGCi83Z0VwNmpOUS9sVVB0elkwcE5MZUMwdGdLeFhmcm9DdXBWc3U3ZUFub1ExU2ZaY1NNNEtlTXhzaThYWkQ5K2YKdllzQmJYTFc0Y09jQ01CN0pZYnZndUp5OWFyd01rMEpHRGViWGRtTTVRSURBUUFCb0FBd0RRWUpLb1pJaHZjTgpBUUVMQlFBRGdnRUJBTEZSbHg5RlV4QVM1REtGa2xDcWRtQW9nNHlmTzRnb21iQ0p1SnZSc3FYK2Fkbk5NZFRLCnpPNkhLZ1p0T1ozUHdUZldxRzRrc3NpNTg4QmpCcXlzOGVNa1dHckVCdDJJU2NaMEREQnlxclBnb2hQVU91M24KZmlyNzNaZURqd2hvUzF4NmZpT1NRdnJ5N2w1Nk4vV3hwK1pCUlorNWhpZEIzaUVla2lOT3JXcHhINDdPdWNqWQpWNHhnNnV0bmlTSzluYkJVeE9UM3JnVDcweU9SYkg2YnZaakJVbXpJclVqZXJieVpOalYrcFZtOTVRZmhnZElJCit0Z1ZuSzR5VUpqcEIycWF0QXRLdERQaWdYMlAxNDVhVHg3QVkzV2VrQ0VBVGw2NytYK2gxbXdpaGhxRlZIYWUKei9uU0FVTTNmVnVPTk50SGxVQlBoWEpTSEhZVVA4ODkzMzA9Ci0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=


controlplane ~ ➜  cat csr.yaml 
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
   name: akshaycsr
spec:
  signerName: kubernetes.io/kube-apiserver-client
  groups: 
  - system:authenticated
  usages: 
  - client auth
  request: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZqQ0NBVDRDQVFBd0VURVBNQTBHQTFVRUF3d0dZV3R6YUdGNU1JSUJJakFOQmdrcWhraUc5dzBCQVFFRgpBQU9DQVE4QU1JSUJDZ0tDQVFFQTZZWGNmbTNPeGpCak1RNjByNDQzRG03cncvN0hGOW9rY3NGTE0ybEorQm50CjVyNEk0eVl1ZVF2TFM0bU1Vc1ZmbGdHUVVWNXhOL3F0RGEzWUhqNWhVY090SmVrVVNyS0pLQm5mbVV6d0l3dUwKM3VMc1hNTllHRzRHUyswejJXY1JIQ3JxSHFEVmJWMDc4TmtDQndLVjRZR1JCb09LVWdwdjFvQzJYQk9iaTV2dgp1aWlBWkZEQUJmNll4czB1VVhiaTYrTC9QMnJrZFB4NDJITklWczJsRjBHRkFadVdoR2U2RmRSZlRDMFRUeTZGCi83Z0VwNmpOUS9sVVB0elkwcE5MZUMwdGdLeFhmcm9DdXBWc3U3ZUFub1ExU2ZaY1NNNEtlTXhzaThYWkQ5K2YKdllzQmJYTFc0Y09jQ01CN0pZYnZndUp5OWFyd01rMEpHRGViWGRtTTVRSURBUUFCb0FBd0RRWUpLb1pJaHZjTgpBUUVMQlFBRGdnRUJBTEZSbHg5RlV4QVM1REtGa2xDcWRtQW9nNHlmTzRnb21iQ0p1SnZSc3FYK2Fkbk5NZFRLCnpPNkhLZ1p0T1ozUHdUZldxRzRrc3NpNTg4QmpCcXlzOGVNa1dHckVCdDJJU2NaMEREQnlxclBnb2hQVU91M24KZmlyNzNaZURqd2hvUzF4NmZpT1NRdnJ5N2w1Nk4vV3hwK1pCUlorNWhpZEIzaUVla2lOT3JXcHhINDdPdWNqWQpWNHhnNnV0bmlTSzluYkJVeE9UM3JnVDcweU9SYkg2YnZaakJVbXpJclVqZXJieVpOalYrcFZtOTVRZmhnZElJCit0Z1ZuSzR5VUpqcEIycWF0QXRLdERQaWdYMlAxNDVhVHg3QVkzV2VrQ0VBVGw2NytYK2gxbXdpaGhxRlZIYWUKei9uU0FVTTNmVnVPTk50SGxVQlBoWEpTSEhZVVA4ODkzMzA9Ci0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=


controlplane ~ ➜  k apply -f csr.yaml 
certificatesigningrequest.certificates.k8s.io/akshaycsr created

controlplane ~ ➜  k get csr
NAME        AGE   SIGNERNAME                            REQUESTOR          REQUESTEDDURATION   CONDITION
akshaycsr   16s   kubernetes.io/kube-apiserver-client   kubernetes-admin   <none>              Pending

controlplane ~ ➜  k describe csr
Name:         akshaycsr
Labels:       <none>
Annotations:  kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"certificates.k8s.io/v1","kind":"CertificateSigningRequest","metadata":{"annotations":{},"name":"akshaycsr"},"spec":{"groups":["system:authenticated"],"request":"LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZqQ0NBVDRDQVFBd0VURVBNQTBHQTFVRUF3d0dZV3R6YUdGNU1JSUJJakFOQmdrcWhraUc5dzBCQVFFRgpBQU9DQVE4QU1JSUJDZ0tDQVFFQTZZWGNmbTNPeGpCak1RNjByNDQzRG03cncvN0hGOW9rY3NGTE0ybEorQm50CjVyNEk0eVl1ZVF2TFM0bU1Vc1ZmbGdHUVVWNXhOL3F0RGEzWUhqNWhVY090SmVrVVNyS0pLQm5mbVV6d0l3dUwKM3VMc1hNTllHRzRHUyswejJXY1JIQ3JxSHFEVmJWMDc4TmtDQndLVjRZR1JCb09LVWdwdjFvQzJYQk9iaTV2dgp1aWlBWkZEQUJmNll4czB1VVhiaTYrTC9QMnJrZFB4NDJITklWczJsRjBHRkFadVdoR2U2RmRSZlRDMFRUeTZGCi83Z0VwNmpOUS9sVVB0elkwcE5MZUMwdGdLeFhmcm9DdXBWc3U3ZUFub1ExU2ZaY1NNNEtlTXhzaThYWkQ5K2YKdllzQmJYTFc0Y09jQ01CN0pZYnZndUp5OWFyd01rMEpHRGViWGRtTTVRSURBUUFCb0FBd0RRWUpLb1pJaHZjTgpBUUVMQlFBRGdnRUJBTEZSbHg5RlV4QVM1REtGa2xDcWRtQW9nNHlmTzRnb21iQ0p1SnZSc3FYK2Fkbk5NZFRLCnpPNkhLZ1p0T1ozUHdUZldxRzRrc3NpNTg4QmpCcXlzOGVNa1dHckVCdDJJU2NaMEREQnlxclBnb2hQVU91M24KZmlyNzNaZURqd2hvUzF4NmZpT1NRdnJ5N2w1Nk4vV3hwK1pCUlorNWhpZEIzaUVla2lOT3JXcHhINDdPdWNqWQpWNHhnNnV0bmlTSzluYkJVeE9UM3JnVDcweU9SYkg2YnZaakJVbXpJclVqZXJieVpOalYrcFZtOTVRZmhnZElJCit0Z1ZuSzR5VUpqcEIycWF0QXRLdERQaWdYMlAxNDVhVHg3QVkzV2VrQ0VBVGw2NytYK2gxbXdpaGhxRlZIYWUKei9uU0FVTTNmVnVPTk50SGxVQlBoWEpTSEhZVVA4ODkzMzA9Ci0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=","signerName":"kubernetes.io/kube-apiserver-client","usages":["client auth"]}}

CreationTimestamp:  Thu, 09 Jan 2025 12:18:19 +0000
Requesting User:    kubernetes-admin
Signer:             kubernetes.io/kube-apiserver-client
Status:             Pending
Subject:
         Common Name:    akshay
         Serial Number:  
Events:  <none>

controlplane ~ ➜  k certificate approve 
error: one or more CSRs must be specified as <name> or -f <filename>

controlplane ~ ✖ k certificate approve akshaycsr
certificatesigningrequest.certificates.k8s.io/akshaycsr approved

controlplane ~ ➜  k describe csr
Name:         akshaycsr
Labels:       <none>
Annotations:  kubectl.kubernetes.io/last-applied-configuration={"apiVersion":"certificates.k8s.io/v1","kind":"CertificateSigningRequest","metadata":{"annotations":{},"name":"akshaycsr"},"spec":{"groups":["system:authenticated"],"request":"LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZqQ0NBVDRDQVFBd0VURVBNQTBHQTFVRUF3d0dZV3R6YUdGNU1JSUJJakFOQmdrcWhraUc5dzBCQVFFRgpBQU9DQVE4QU1JSUJDZ0tDQVFFQTZZWGNmbTNPeGpCak1RNjByNDQzRG03cncvN0hGOW9rY3NGTE0ybEorQm50CjVyNEk0eVl1ZVF2TFM0bU1Vc1ZmbGdHUVVWNXhOL3F0RGEzWUhqNWhVY090SmVrVVNyS0pLQm5mbVV6d0l3dUwKM3VMc1hNTllHRzRHUyswejJXY1JIQ3JxSHFEVmJWMDc4TmtDQndLVjRZR1JCb09LVWdwdjFvQzJYQk9iaTV2dgp1aWlBWkZEQUJmNll4czB1VVhiaTYrTC9QMnJrZFB4NDJITklWczJsRjBHRkFadVdoR2U2RmRSZlRDMFRUeTZGCi83Z0VwNmpOUS9sVVB0elkwcE5MZUMwdGdLeFhmcm9DdXBWc3U3ZUFub1ExU2ZaY1NNNEtlTXhzaThYWkQ5K2YKdllzQmJYTFc0Y09jQ01CN0pZYnZndUp5OWFyd01rMEpHRGViWGRtTTVRSURBUUFCb0FBd0RRWUpLb1pJaHZjTgpBUUVMQlFBRGdnRUJBTEZSbHg5RlV4QVM1REtGa2xDcWRtQW9nNHlmTzRnb21iQ0p1SnZSc3FYK2Fkbk5NZFRLCnpPNkhLZ1p0T1ozUHdUZldxRzRrc3NpNTg4QmpCcXlzOGVNa1dHckVCdDJJU2NaMEREQnlxclBnb2hQVU91M24KZmlyNzNaZURqd2hvUzF4NmZpT1NRdnJ5N2w1Nk4vV3hwK1pCUlorNWhpZEIzaUVla2lOT3JXcHhINDdPdWNqWQpWNHhnNnV0bmlTSzluYkJVeE9UM3JnVDcweU9SYkg2YnZaakJVbXpJclVqZXJieVpOalYrcFZtOTVRZmhnZElJCit0Z1ZuSzR5VUpqcEIycWF0QXRLdERQaWdYMlAxNDVhVHg3QVkzV2VrQ0VBVGw2NytYK2gxbXdpaGhxRlZIYWUKei9uU0FVTTNmVnVPTk50SGxVQlBoWEpTSEhZVVA4ODkzMzA9Ci0tLS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=","signerName":"kubernetes.io/kube-apiserver-client","usages":["client auth"]}}

CreationTimestamp:  Thu, 09 Jan 2025 12:18:19 +0000
Requesting User:    kubernetes-admin
Signer:             kubernetes.io/kube-apiserver-client
Status:             Approved,Issued
Subject:
         Common Name:    akshay
         Serial Number:  
Events:  <none>

  ```
</details>


<details>
<summary>
Senario -

```bash

  ```

</summary>

```bash

  ```
</details>
<details>
<summary>
Senario -

```bash

  ```

</summary>

```bash

  ```
</details>


<details>
<summary>
Senario -

```bash

  ```

</summary>

```bash

  ```
</details>
<details>
<summary>
Senario -

```bash

  ```

</summary>

```bash

  ```
</details>


<details>
<summary>
Senario -

```bash

  ```

</summary>

```bash

  ```
</details>
<details>
<summary>
Senario -

```bash

  ```

</summary>

```bash

  ```
</details>


<details>
<summary>
Senario -

```bash

  ```

</summary>

```bash

  ```
</details>
<details>
<summary>
Senario -

```bash

  ```

</summary>

```bash

  ```
</details>


<details>
<summary>
Senario -

```bash

  ```

</summary>

```bash

  ```
</details>
<details>
<summary>
Senario -

```bash

  ```

</summary>

```bash

  ```
</details>


<details>
<summary>
Senario -

```bash

  ```

</summary>

```bash

  ```
</details>
<details>
<summary>
Senario -

```bash

  ```

</summary>

```bash

  ```
</details>


<details>
<summary>
Senario -

```bash

  ```

</summary>

```bash

  ```
</details>
<details>
<summary>
Senario -

```bash

  ```

</summary>

```bash

  ```
</details>


<details>
<summary>
Senario -

```bash

  ```

</summary>

```bash

  ```
</details>
<details>
<summary>
Senario -

```bash

  ```

</summary>

```bash

  ```
</details>


<details>
<summary>
Senario -

```bash

  ```

</summary>

```bash

  ```
</details>
<details>
<summary>
Senario -

```bash

  ```

</summary>

```bash

  ```
</details>

