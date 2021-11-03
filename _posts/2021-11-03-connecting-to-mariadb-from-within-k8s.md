---
title: Connecting to MariaDB from withing K8s
categories:
- reminder
excerpt: |
  Connecting to a MariaDB service that is deployed on K8s but not expose becomes a challenge but it's pretty easy to solve.
---

Execute a new pod with MariaDB client and connect from this one to the MariaDB server.

```
kubectl run maria-client --rm --tty -i --restart='Never' --image mariadb:10.6 --command -- mariadb -u<username> -h<hostname> -p
```

Type the password (This one won't be shown on the console) and press enter, now you are logged into MariaDB console.
