---
title: Creating Maven wrapper files
categories:
- reminder
- maven
excerpt: |
  Something a Maven wrapper is pretty useful, let's see how to create it.
---

With maven installed, run:

```bash
mvn -N io.takari:maven:wrapper
```

Specific maven version:

```bash
mvn -N io.takari:maven:wrapper -Dmaven=3.3.9 
```
