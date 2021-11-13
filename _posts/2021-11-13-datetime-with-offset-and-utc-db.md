---
title: Transforming OffsetDateTime to LocalDateTime to store in DB
categories:
- reminder
excerpt: |
  At today, MariaDB does not dupport Offset on its DateTime fields and, my approach is to store everything on UTC and don't deal with offset column in the table.
---

At this moment, my preferred approach is handling all dates on the app as `OffsetDataTime`, on this way we 
preserve the instant no matter the Offset and then transform this to UTC when it's going to be stored, 
later, when recovering the value from the persistence mechanism (DB), read it directly as `OffsetDateTime`
or read it as `LocalDateTime` and transform it to `OffsetDateTime` with offset on UTC.

This transformation can be done using the method `withOffsetSameInstant(ZoneOffset.UTC)` from 
`OffsetDateTime` and the method `.atOffset(ZoneOffset.UTC)` from `LocalDateTime`.

```java
@Test
@DisplayName("reviewing offset datetime transformations")
void reviewingOffsetDatetimeTransformations() {
  // The default offset in my location is +01:00
  Clock clock = Clock.fixed(Instant.now(), ZoneId.systemDefault());
  OffsetDateTime offsetDateTime = OffsetDateTime.now(clock);
  
  var utcOffSet = offsetDateTime.withOffsetSameInstant(ZoneOffset.UTC);
  var localFromOffset = utcOffSet.toLocalDateTime();
  var offsetFromLocal = localFromOffset.atOffset(ZoneOffset.UTC);

  System.out.println(offsetDateTime);
  System.out.println(utcOffSet);
  System.out.println(localFromOffset);
  System.out.println(offsetFromLocal);

  assertThat(offsetDateTime)
    .isEqualTo(utcOffSet)
    .isEqualTo(offsetFromLocal);
}
```

With above test, the result will be green no matter the Zone and the output on the consule will show
the same __instant__ on different formats.

```
2021-11-13T22:52:14.974925+01:00
2021-11-13T21:52:14.974925Z
2021-11-13T21:52:14.974925
2021-11-13T21:52:14.974925Z
```
