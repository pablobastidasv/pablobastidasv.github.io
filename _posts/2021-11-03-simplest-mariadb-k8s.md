---
title: The simplest MariaDB K8s deployment
categories:
- reminder
excerpt: |
  In some cases I want to deploy a MariaDB server inside k8s but not a real server, with many instances, volumes, high performance configuration, on other words, a non prod MariaDB for local tests/dev. Here is the solution that I found for this.
---

Here the k8s yaml file with the service, config map and deployment that allows me to run a MariaDB server in my `docker-desktop` k8s.

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-app-rdbms
  labels:
    app: my-app
    component: database
data:
  MARIADB_RANDOM_ROOT_PASSWORD: "yes"
  MARIADB_DATABASE: my-app
  MARIADB_USER: my-app
  MARIADB_PASSWORD: admin123

---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-rdbms
  labels:
    app: my-app
    component: database
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
      component: database
  template:
    metadata:
      labels:
        app: my-app
        component: database
    spec:
      containers:
        - name: my-app-rdbms
          image: mariadb:10.6
          ports:
            - containerPort: 3306
          envFrom:
            - configMapRef:
                name: my-app-rdbms
---

apiVersion: v1
kind: Service
metadata:
  name: my-app-rdbms
  labels:
    app: my-app
    component: database
spec:
  ports:
    - port: 3306
  selector:
    app: my-app
    component: database

```

Once this is created deploy it with:

```
kubectl apply -f rdbms.yaml
```
