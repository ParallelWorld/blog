---
title: tomcat源码分析---server.xml文件的加载和解析
date: 2017-09-13 17:04:18
tags:
categories:
---

server.xml 对于 tomcat 来讲就是配置文件。搞清楚 server.xml 是如何加载的很有必要。

// 此处缺个 UML 时序图

Bootstrap 类

```java
private void load(String[] arguments) throws Exception {
    // Call the load() method
    String methodName = "load";
    Object param[];
    Class<?> paramTypes[];
    if (arguments == null || arguments.length == 0) {
        paramTypes = null;
        param = null;
    } else {
        paramTypes = new Class[1];
        paramTypes[0] = arguments.getClass();
        param = new Object[1];
        param[0] = arguments;
    }
    // catalinaDaemon是org.apache.catalina.startup.Catalina实例
    Method method = catalinaDaemon.getClass().getMethod(methodName, paramTypes);
    if (log.isDebugEnabled())
        log.debug("Calling startup class " + method);
    method.invoke(catalinaDaemon, param);
}
```

通过 java 反射，实际调用的是 Catalina 实例的 load 方法。

Catalina 类

```java
public void load() {

    //···

    // 检查catalina.home和catalina.base路径
    initDirs();

    // TODO 不知道这个是干嘛的
    initNaming();

    // Create and execute our Digester
    Digester digester = createStartDigester();

    InputSource inputSource = null;
    InputStream inputStream = null;
    File file = null;
    try {
        // 配置文件conf/server.xml路径在此，$(catalina.base)/conf/server.xml
        file = configFile();
        inputStream = new FileInputStream(file);
        inputSource = new InputSource(file.toURI().toURL().toString());
    } catch (Exception e) {
        if (log.isDebugEnabled()) {
            log.debug(sm.getString("catalina.configFail", file), e);
        }
    }
    if (inputStream == null) {
        try {
            inputStream = getClass().getClassLoader()
                    .getResourceAsStream(getConfigFile());
            inputSource = new InputSource
                    (getClass().getClassLoader()
                            .getResource(getConfigFile()).toString());
        } catch (Exception e) {
            if (log.isDebugEnabled()) {
                log.debug(sm.getString("catalina.configFail",
                        getConfigFile()), e);
            }
        }
    }

    // This should be included in catalina.jar
    // Alternative: don't bother with xml, just create it manually.
    if (inputStream == null) {
        try {
            inputStream = getClass().getClassLoader()
                    .getResourceAsStream("server-embed.xml");
            inputSource = new InputSource
                    (getClass().getClassLoader()
                            .getResource("server-embed.xml").toString());
        } catch (Exception e) {
            if (log.isDebugEnabled()) {
                log.debug(sm.getString("catalina.configFail",
                        "server-embed.xml"), e);
            }
        }
    }


    if (inputStream == null || inputSource == null) {
        if (file == null) {
            log.warn(sm.getString("catalina.configFail",
                    getConfigFile() + "] or [server-embed.xml]"));
        } else {
            log.warn(sm.getString("catalina.configFail",
                    file.getAbsolutePath()));
            if (file.exists() && !file.canRead()) {
                log.warn("Permissions incorrect, read permission is not allowed on the file.");
            }
        }
        return;
    }

    try {
        inputSource.setByteStream(inputStream);
        digester.push(this);

        // server.xml读取出来后，进行映射
        digester.parse(inputSource);


    } catch (SAXParseException spe) {
        log.warn("Catalina.start using " + getConfigFile() + ": " +
                spe.getMessage());
        return;
    } catch (Exception e) {
        log.warn("Catalina.start using " + getConfigFile() + ": ", e);
        return;
    } finally {
        try {
            inputStream.close();
        } catch (IOException e) {
            // Ignore
        }
    }

    getServer().setCatalina(this);

    // Stream redirection
    initStreams();

    // Start the new server
    try {
        getServer().init();
    } catch (LifecycleException e) {
        if (Boolean.getBoolean("org.apache.catalina.startup.EXIT_ON_INIT_FAILURE")) {
            throw new java.lang.Error(e);
        } else {
            log.error("Catalina.start", e);
        }

    }

    long t2 = System.nanoTime();
    if (log.isInfoEnabled()) {
        log.info("Initialization processed in " + ((t2 - t1) / 1000000) + " ms");
    }

}
```
