---
title: Java optional to kotlin nullable
categories:
- reminder
- java
- kotlin
- optional
excerpt: |
  When using java frameworks/libraries in kotlin you may find dealing with java `Optional` which equivalent is kotlin nullable
---

From [this](https://stackoverflow.com/questions/38767376/how-to-map-java-util-optionalsomething-to-something-in-kotlin) 
stackoverflow forum, below code extends `Optional` and adds a hande `unwrap()` method that converts the java `Optional` 
into a kotlin nullable object.

```kotlin
fun <T> Optional<T>.unwrap(): T? = orElse(null)
```

and then you can used like below:

```kotlin
val msg: Something? = optional.unwrap()
```
