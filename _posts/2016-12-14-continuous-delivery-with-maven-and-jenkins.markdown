---
layout: post
title:  "Continuous Delivery with Maven and Jenkins"
date:   2016-12-14 13:30:00 +0100
categories: maven jenkins
excerpt: How to automatically create unique version numbers with Maven in Jenkins.
---

Motivation: Every jenkins build produces a potential release and as such each build should have a unique version number.
This post shows how to use the Jenkins build number to archive this.

The basic idea is to use a variable as part of the &lt;version>...&lt;/version> declaration in your pom.xml. This variable
is set to 1.0-SNAPSHOT by default for local builds and when executed on jenkins to '1.0-&lt;BUILD_NUMBER>'.

{% highlight xml %}
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>de.torstenwalter.examples</groupId>
    <artifactId>jenkins-versioning</artifactId>
    <version>${revision}</version>

    <properties>
        <baseRevision>1.0</baseRevision>
        <revision>${baseRevision}-SNAPSHOT</revision>
    </properties>

    <profiles>
        <profile>
            <id>jenkins</id>
            <activation>
                <property>
                    <name>env.BUILD_NUMBER</name>
                </property>
            </activation>
            <properties>
                <revision>${baseRevision}-${env.BUILD_NUMBER}</revision>
            </properties>
        </profile>
    </profiles>
</project>
{% endhighlight %}


As variable name for the version `${revision}` is used as it is explicitly allowed since maven 3.2.1  at that location
(see [improvement MNG-5576](https://issues.apache.org/jira/browse/MNG-5576)). 

This is the warning if you try to use a different variable name e.g. ${myVar} instead of ${revision}:

    [WARNING] Some problems were encountered while building the effective model for de.torstenwalter.examples:jenkins-versioning:jar:${myVar}
    [WARNING] 'version' contains an expression but should be a constant. @ line 7, column 14
    [WARNING]
    [WARNING] It is highly recommended to fix these problems because they threaten the stability of your build.
    [WARNING]
    [WARNING] For this reason, future Maven versions might no longer support building such malformed projects.
    [WARNING]

This snippet sets the default value for revision to 1.0-SNAPSHOT:
{% highlight xml %}
    <properties>
        <baseRevision>1.0</baseRevision>
        <revision>${baseRevision}-SNAPSHOT</revision>
    </properties>
{% endhighlight %}
`baseRevision` is meant to provide the base version e.g. 1.0 or 2.3 where the build number is later appended or -LATEST for
local builds.

The build number is included as part of the version by using a maven profile which is automatically enabled when the 
environment variable `BUILD_NUMBER` is present. 
{% highlight xml %}
        <profile>
            <id>jenkins</id>
            <activation>
                <property>
                    <name>env.BUILD_NUMBER</name>
                </property>
            </activation>
            <properties>
                <revision>${baseRevision}-${env.BUILD_NUMBER}</revision>
            </properties>
        </profile>
{% endhighlight %}

