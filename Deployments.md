
<details>
<summary>
Senario -
Create a new Deployment with the below attributes using your own deployment definition file.


Name: httpd-frontend;
Replicas: 3;
Image: httpd:2.4-alpine
</summary>

```bash

controlplane ~ ✖ k create deployment  httpd-frontend --image=httpd:2.4-alpine --replicas=3
deployment.apps/httpd-frontend created

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd-frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      name: httpd-frontend
  template:
    metadata:
      labels:
        name: httpd-frontend
    spec:
      containers:
      - name: httpd-frontend
        image: httpd:2.4-alpine
  ```
</details>


