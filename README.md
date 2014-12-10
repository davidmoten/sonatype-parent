sonatype-parent
===============
<a href="https://travis-ci.org/davidmoten/geo"><img src="https://travis-ci.org/davidmoten/sonatype-parent.svg"/></a><br/>
[![Maven Central](https://maven-badges.herokuapp.com/maven-central/com.github.davidmoten/sonatype-parent/badge.svg?style=flat)](https://maven-badges.herokuapp.com/maven-central/com.github.davidmoten/sonatype-parent)

Parent pom.xml to ease deployment to Maven Central

Getting started
----------------
Add this parent to your pom.xml:

```xml
<parent>
	<groupId>com.github.davidmoten</groupId>
    <artifactId>sonatype-parent</artifactId>
    <version>0.1</version>
</parent>
```

Releasing
----------

Once you have your settings.xml sorted and your gpg keys as per http://central.sonatype.org/pages/apache-maven.html, you can release in one command line to Maven Central:

```bash
mvn release:prepare && mvn release:perform
```

You will be prompted for versions.
