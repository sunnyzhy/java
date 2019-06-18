# IDEA
## 使用 java11 新特性的语法，需要做以下配置

1. File -> Project Structure -> Platform Settings -> SDKs -> JDK home path ， 设置为 jdk11 的绝对路径

2. File -> Project Structure -> Project Settings -> Project -> Project SDK ， 选择 11(java version "11")

3. File -> Project Structure -> Project Settings -> Modules -> Language level ， 选择 11 - Local variable syntax for lambda parameters

4. File -> Settings -> 搜索 Java Compiler -> Per-module bytecode version -> Target bytecode version ， 选择 11

5. pom.xml

```xml
	<properties>
		<java.version>11</java.version>
	</properties>
```

## 示例
```java
    @Test
    public void testString() {
        var b = " ".isBlank();
        System.out.println(b);
        var s = "Java".repeat(3);
        System.out.println(s);
    }

    @Test
    public void testHTTPClient() throws IOException, InterruptedException {
        var request = HttpRequest.newBuilder()
                .uri(URI.create("https://www.baidu.com"))
                .GET()
                .build();
        var client = HttpClient.newHttpClient();

        // 同步
        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
        System.out.println(response.body());

        // 异步
        client.sendAsync(request, HttpResponse.BodyHandlers.ofString())
                .thenApply(HttpResponse::body)
                .thenAccept(System.out::println);
    }
```
