---
title: The simplest PostgreSQL K8s deployment
categories:
- reminder
excerpt: |
  In some cases I want to deploy a PostgreSQL server inside k8s but not a real server, with many instances, volumes, high performance configuration, on other words, a non prod PostgreSQL for local tests/dev. Here is the solution that I found for this.
---

Below a postgres deployment, no suitable for any prod (even do not have persistence).

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: rdbms
  labels:
    app: rdbms
    component: database
data:
  POSTGRES_DB: database
  POSTGRES_USER: user
  POSTGRES_PASSWORD: admin123

---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: rdbms
  labels:
    app: rdbms
    component: database
spec:
  replicas: 1
  selector:
    matchLabels:
      app: rdbms
      component: database
  template:
    metadata:
      labels:
        app: rdbms
        component: database
    spec:
      containers:
        - name: postgres
          image: postgres:13.5-bullseye
          imagePullPolicy: "IfNotPresent"
          ports:
            - containerPort: 5432
          envFrom:
            - configMapRef:
                name: rdbms
---

apiVersion: v1
kind: Service
metadata:
  name: rdbms
  labels:
    app: rdbms
    component: database
spec:
  ports:
    - port: 5432
  selector:
    app: rdbms
    component: database

```

## Running a postgresql client

``` bash
kubectl run postgresql-client --rm --tty -i --restart='Never' --image postgres:14.1-bullseye --command -- psql  -h rdbms -U user -p 5432 database
```
