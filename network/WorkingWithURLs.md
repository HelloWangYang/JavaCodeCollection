#1.基本概念

* URI(uniform resource identifier) 统一资源标识符。URI是个纯粹的语法结构，用于指定标识Web资源的字符串的各个不同部分。在Java类库中，URI类不包含任何用于访问资源的方法，他唯一的作用就是解析。

* URL(uniform resource locator) 统一资源定位符。URL是URI的一个特例，它包含了用于定位Web资源的足够信息。

* URN(uniform resource name) 统一资源名称。其他的URI，例如`mailto:horstman.com`则不属于定位符，因为根据该标识符我们无法定位任何数据。这样的URI就称为URN。

#2.创建URL

可以查看[java.net.URL](http://docs.oracle.com/javase/8/docs/api/java/net/URL.html)。有不同的构造方法，也有从[java.net.URI](http://docs.oracle.com/javase/8/docs/api/java/net/URI.html)创建URL的方法。

需要注意的是创建URL需要捕捉`MalformedURLException`异常：

```java
try {
    URL myURL = new URL(...);
} 
catch (MalformedURLException e) {
    // exception handler code here
    // ...
}
```

此外，URLs是一个**`"write-once" objects`**。当创建一个URL对象后，就不能修改这个URL对象的任何属性(protocol, host name, filename, or port number)。

#3.从URLConnection读取数据

```java
import java.net.*;
import java.io.*;

public class URLConnectionReader {
    public static void main(String[] args) throws Exception {
        URL oracle = new URL("http://www.oracle.com/");
        URLConnection yc = oracle.openConnection();
        BufferedReader in = new BufferedReader(new InputStreamReader(
                                    yc.getInputStream()));
        String inputLine;
        while ((inputLine = in.readLine()) != null) 
            System.out.println(inputLine);
        in.close();
    }
}
```

这里使用了`URLConnection`对象来读取数据。也可以使用`URL`直接读取数据，但使用`URLConnection`会更有效，因为可以在同一时间使用`URLConnection`对象来进行其他操作（例如向`URL`写入数据）。

#4.向URLConnection写入数据

步骤如下：

1. Create a URL.
2. Retrieve the URLConnection object.
3. Set output capability on the URLConnection.
4. Open a connection to the resource.
5. Get an output stream from the connection.
6. Write to the output stream.
7. Close the output stream.

```java
import java.io.*;
import java.net.*;

public class Reverse {
    public static void main(String[] args) throws Exception {

        if (args.length != 2) {
            System.err.println("Usage:  java Reverse "
                + "http://<location of your servlet/script>"
                + " string_to_reverse");
            System.exit(1);
        }

        String stringToReverse = URLEncoder.encode(args[1], "UTF-8");

        URL url = new URL(args[0]);
        URLConnection connection = url.openConnection();
        connection.setDoOutput(true);

        OutputStreamWriter out = new OutputStreamWriter(
                                         connection.getOutputStream());
        out.write("string=" + stringToReverse);
        out.close();

        BufferedReader in = new BufferedReader(
                                    new InputStreamReader(
                                    connection.getInputStream()));
        String decodedString;
        while ((decodedString = in.readLine()) != null) {
            System.out.println(decodedString);
        }
        in.close();
    }
}
```

#5.设置代理

UNIX

	java -Dhttp.proxyHost=proxyhost
	[-Dhttp.proxyPort=portNumber] URLReader

DOS shell (Windows 95/NT)

	java -Dhttp.proxyHost=proxyhost
	[-Dhttp.proxyPort=portNumber] URLReader