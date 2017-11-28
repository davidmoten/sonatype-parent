sonatype-parent
===============
<a href="https://travis-ci.org/davidmoten/geo"><img src="https://travis-ci.org/davidmoten/sonatype-parent.svg"/></a><br/>
[![Maven Central](https://maven-badges.herokuapp.com/maven-central/com.github.davidmoten/sonatype-parent/badge.svg?style=flat)](https://maven-badges.herokuapp.com/maven-central/com.github.davidmoten/sonatype-parent)

Parent pom.xml to ease deployment to Maven Central

I've been using this for years now and have made hundreds of releases to Maven Central with one line commands. Enjoy!

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
You'll need an account with Sonatype (they manage Maven Central). Go [here](http://central.sonatype.org/pages/ossrh-guide.html) to set one up.

Once you have your account credentials (username and password) you put them in `~/.m2/settings.xml` in a server section:

```xml
<servers>
    <server>
        <id>ossrh</id>
        <username>YOUR_USERNAME</username>
        <password>YOUR_PASSWORD</password>
    </server>
...
</servers>
```
You can encrypt the password field in `settings.xml` by following [this guide](https://maven.apache.org/guides/mini/guide-encryption.html).

Next you need the `gpg` client installed. On Ubuntu:

```bash
sudo apt-get update && sudo apt-get install gpg
```

Now create your gpg keys:

TODO

Now publish your gpg keys so the world can use them to verify your artifacts:

TODO

http://central.sonatype.org/pages/apache-maven.html

Now you can release in one command line to Maven Central:

```bash
mvn release:prepare && mvn release:perform
```

You will be prompted for versions.

My preference is to use this bash function in my `.bashrc`:

```bash
function release() {
  RELEASE_VERSION=$1
  GPG_PASSPHRASE=$2
  mvn --batch-mode release:prepare \
    -Dtag=$RELEASE_VERSION \
    -DreleaseVersion=$RELEASE_VERSION \
    -DdevelopmentVersion=$RELEASE_VERSION.1 \
    -DautoVersionSubmodules=true \
    -Darguments=-Dgpg.passphrase=$GPG_PASSPHRASE && \
  mvn --batch-mode release:perform \
    -Darguments=-Dgpg.passphrase=$GPG_PASSPHRASE
}
```
 which is called like this:
 ```bash
 release 0.4 <GPG_PASSPHRASE>
 ```
