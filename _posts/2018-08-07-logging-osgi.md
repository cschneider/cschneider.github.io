---
layout: post
title:  "How to set up proper logging in OSGi using logback backend"
date:   2018-08-07
tags: declarative-services OSGi logback logging
---

Logging in OSGi seemed to be an arcane thing for quite some time. On the logback website there is still [this explanation by Ekke](http://ekkes-corner.blogspot.com/2008/10/index-blogseries-logging-in-osgi.html) which was surely good 2008 but in 2018 people do not accept creating their own logging bridges, adding config using fragments and tweaking start levels.

Luckily this all improved quite a lot. Apache Karaf uses pax-logging and there is now also the [felix logback support bundle](http://felix.apache.org/documentation/subprojects/apache-felix-logback.html). In this article I will focus on the later as it is simple to setup and has some nice features.

## Example code

I added the felix logback support to my [OSGi DS hello world example](https://github.com/cschneider/osgi-ds-hello-world) because
logging is a core aspect in any professional development.

See the Readme in the example for instruction how to buil and run it.

## Logging frontends

Logback + Felix logback supports a wide range of logging frontents (slf4j, jul, log4j, logback, commons logging, OSGi Log service).
For your own code I recommend to use the slf4j API. It is very slim in dependencies and provides a lot of features.

At compile time you only need the slf4j API.

```
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-api</artifactId>
  <version>1.7.25</version>
</dependency>
```

You instantiate slf4j exactly like outside OSGi. So it can also be used for
hybrid code that can run inside and outside of OSGi.
```
class MyClass {
    Logger log = LoggerFactory.getLogger(this.getClass());
}
```

### Deployment

At runtime you install the bundles below. These also include the Felix log
service which is used by some OSGi reference implementations.

```
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-api</artifactId>
  <version>1.7.25</version>
</dependency>
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.0</version>
</dependency>
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-core</artifactId>
    <version>1.2.0</version>
</dependency>
<dependency>
    <groupId>org.apache.felix</groupId>
    <artifactId>org.apache.felix.log</artifactId>
    <version>1.2.0</version>
</dependency>
<dependency>
    <groupId>org.apache.felix</groupId>
    <artifactId>org.apache.felix.logback</artifactId>
    <version>1.0.0</version>
</dependency>
```

The simplest way to install these is to use the bndtools bndrun packaging like in the example above.

### Configuration

The logback configuration can be provided by a framework property. Logback will
automatically watch the file for changes and apply new settings.

```
-runproperties: logback.configurationFile=file:${.}/logback.xml
```
You can use plain logback configs but felix logback also provides some special settings to configure OSGi specific logs like bundle events. See the [examples in the felix logback docs](http://felix.apache.org/documentation/subprojects/apache-felix-logback.html).

An example config can be found [here](https://github.com/cschneider/osgi-ds-hello-world/blob/master/starter/logback.xml).
