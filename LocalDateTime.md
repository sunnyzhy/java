# 将LocalDateTime转为自定义的时间格式的字符串
```java
public static String getDateTimeAsString(LocalDateTime localDateTime, String format) {
    DateTimeFormatter formatter = DateTimeFormatter.ofPattern(format);
    return localDateTime.format(formatter);
}
```

# 将long类型的timestamp转为LocalDateTime
```java
public static LocalDateTime getDateTimeOfTimestamp(long timestamp) {
    Instant instant = Instant.ofEpochMilli(timestamp);
    ZoneId zone = ZoneId.systemDefault();
    return LocalDateTime.ofInstant(instant, zone);
}
```

# 将LocalDateTime转为long类型的timestamp
```java
public static long getTimestampOfDateTime(LocalDateTime localDateTime) {
    ZoneId zone = ZoneId.systemDefault();
    Instant instant = localDateTime.atZone(zone).toInstant();
    return instant.toEpochMilli();
}
```

# 将某时间字符串转为自定义时间格式的LocalDateTime
```java
public static LocalDateTime parseStringToDateTime(String time, String format) {
    DateTimeFormatter df = DateTimeFormatter.ofPattern(format);
    return LocalDateTime.parse(time, df);
}
```
