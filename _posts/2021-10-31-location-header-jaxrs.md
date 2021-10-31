---
title: JaxRS location header response
categories:
- reminder
excerpt: |
  Adding a location header to the POST response is something easy on JaxRS but I always forget how to do it.
---

Add `@Context UriInfo uriInfo` to your method:

```java
import javax.ws.rs.core.UriInfo;
import javax.ws.rs.core.Context;

  ...
  public Response create(
    @NotNull RequestHttpModel request,
    @Context UriInfo uriInfo
  ){
  ...
  }
```

Use method `getRequestUriBuilder()` from `uriInfo` to add to the `path` the `id` of the new resource.

```java
...
var locationHeader = uriInfo.getRequestUriBuilder().path(id).build()
...

```

Add this location header to the Response

```java
...
return Response.create(locationHEader).build();
...
```
