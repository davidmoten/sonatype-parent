sonatype-parent
===============
[![Maven Central](https://maven-badges.herokuapp.com/maven-central/com.github.davidmoten/sonatype-parent/badge.svg?style=flat)](https://maven-badges.herokuapp.com/maven-central/com.github.davidmoten/sonatype-parent)

Parent pom.xml to ease deployment to Maven Central

I've been using this since 2014 and have made hundreds of releases to Maven Central with one line commands across some >50 different open source projects. Enjoy!

Update:  June 2025, now using non-legacy publishing, still going strong, I release nearly every week with this.

Getting started
----------------
Add this parent to your pom.xml:

```xml
<parent>
    <groupId>com.github.davidmoten</groupId>
    <artifactId>sonatype-parent</artifactId>
    <version>VERSION_HERE</version>
</parent>
```

Releasing
----------

### Create a Sonatype account and configure Maven to use it
You'll need an account with Sonatype (they manage Maven Central). Create an account or sign in [here](https://central.sonatype.com/) to set one up.

Once you have your account credentials (username and password) you put them in `~/.m2/settings.xml` in a server section:

```xml
<servers>
    <server>
        <id>central</id>
        <username>YOUR_USERNAME</username>
        <password>YOUR_PASSWORD</password>
    </server>
...
</servers>
```
You can encrypt the password field in `settings.xml` by following [this guide](https://maven.apache.org/guides/mini/guide-encryption.html).

### Create a gpg key
Next you need the `gpg` client installed. On Ubuntu:

```bash
sudo apt-get update && sudo apt-get install gnupg
```

Now create your gpg keys:

```bash
gpg --gen-key
```
Follow the prompts and select `RSA and RSA (default)`, 4096 bits and whatever expiry you desire.

```bash
$ gpg --gen-key
gpg (GnuPG) 1.4.20; Copyright (C) 2015 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
Your selection? 1
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 0
Key does not expire at all
Is this correct? (y/N) y

You need a user ID to identify your key; the software constructs the user ID
from the Real Name, Comment and Email Address in this form:
    "Heinrich Heine (Der Dichter) <heinrichh@duesseldorf.de>"

Real name: David Moten <davidmoten@gmail.com>
Invalid character in name
Real name: "David Moten <davidmoten@gmail.com>"
Invalid character in name
Real name: David Moten
Email address: davidmoten@gmail.com
Comment: 
You selected this USER-ID:
    "David Moten <davidmoten@gmail.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
You need a Passphrase to protect your secret key.

We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
+++++
gpg: /home/dxm/.gnupg/trustdb.gpg: trustdb created
gpg: key 93C61F1A marked as ultimately trusted
public and secret key created and signed.

gpg: checking the trustdb
gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
pub   4096R/93C61F1A 2019-05-23
      Key fingerprint = 4327 D3E9 B7CC A1A9 D8F7  6805 1B18 2CFB 93C6 1F1A
uid                  David Moten <davidmoten@gmail.com>
sub   4096R/AB192E0C 2019-05-23
```
List your keys:
```bash
$ gpg --list-keys
/home/dxm/.gnupg/pubring.gpg
----------------------------
pub   4096R/93C61F1A 2019-05-23
uid                  David Moten <davidmoten@gmail.com>
sub   4096R/AB192E0C 2019-05-23
$ gpg --armor --export  93C61F1A >mykey.asc
```
Go to https://pgp.mit.edu/ and submit the contents of `mykey.asc`. Now your public gpg key has been published so the world can use it to verify your artifacts. 

**Note**: you may have to wait up to a day for the world's gpg servers to synchronize with the gpg server you submitted to.

### Release with Maven
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
  mvn release:clean
  mvn --batch-mode release:prepare \
    -Dtag=$RELEASE_VERSION \
    -DreleaseVersion=$RELEASE_VERSION \
    -DdevelopmentVersion=$RELEASE_VERSION.1 \
    -DautoVersionSubmodules=true \
    -Darguments=-Dgpg.passphrase=$GPG_PASSPHRASE && \
  mvn --batch-mode release:perform \
    -Darguments=-Dgpg.passphrase=$GPG_PASSPHRASE
  git push
}
```
 which is called like this:
 ```bash
 release 0.4 <GPG_PASSPHRASE>
 ```

Source
-----------
Some of the above details are from http://central.sonatype.org/pages/apache-maven.html
