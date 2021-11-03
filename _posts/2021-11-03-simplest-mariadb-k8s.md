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
  name: kopenhagen-rdbms
  labels:
    app: kopenhagen
    component: database
data:
  MARIADB_RANDOM_ROOT_PASSWORD: "yes"
  MARIADB_DATABASE: kopenhagen
  MARIADB_USER: kopenhagen
  MARIADB_PASSWORD: admin123

---

apiVersion: v1
kind: ConfigMap
metadata:
  name: kopenhagen-rdbms-config
data:
  my.cnf: |
    [mariadb]
    lower_case_table_names=1

---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: kopenhagen-rdbms
  labels:
    app: kopenhagen
    component: database
spec:
  replicas: 1
  selector:
    matchLabels:
      app: kopenhagen
      component: database
  template:
    metadata:
      labels:
        app: kopenhagen
        component: database
    spec:
      containers:
        - name: kopenhagen-rdbms
          image: mariadb:10.6
          volumeMounts:
            - mountPath: /etc/mysql/conf.d/
              name: config
              readOnly: true
          ports:
            - containerPort: 3306
          envFrom:
            - configMapRef:
                name: kopenhagen-rdbms
      volumes:
        - name: config
          configMap:
            name: kopenhagen-rdbms-config
---

apiVersion: v1
kind: Service
metadata:
  name: kopenhagen-rdbms
  labels:
    app: kopenhagen
    component: database
spec:
  ports:
    - port: 3306
  selector:
    app: kopenhagen
    component: database

```

Once this is created deploy it with:

```
kubectl apply -f rdbms.yaml
```
