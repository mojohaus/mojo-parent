# MojoHaus Parent

[![Maven Central](https://img.shields.io/maven-central/v/org.codehaus.mojo/mojo-parent.svg?label=Maven%20Central)](https://central.sonatype.com/artifact/org.codehaus.mojo/mojo-parent)
[![GitHub CI](https://github.com/mojohaus/mojo-parent/actions/workflows/maven.yml/badge.svg?branch=master)](https://github.com/mojohaus/mojo-parent/actions/workflows/maven.yml)

The parent POM of all [MojoHaus](https://www.mojohaus.org) Maven plugins and components. It sets the Java release
and the Maven API they compile against, the plugin versions and their configuration, the Enforcer rules, Spotless
formatting and the release profile. It has no goals; the build properties, profiles and rules that projects rely on
are described on the [site](https://www.mojohaus.org/mojo-parent/).

It requires Maven 3.9 to build a project, and Java 8 or later (`mojo.java.target`); Spotless needs JDK 17. Use it
as the parent of a project:

```xml
<parent>
  <groupId>org.codehaus.mojo</groupId>
  <artifactId>mojo-parent</artifactId>
  <version><!-- latest release --></version>
</parent>
```

Conventions for contributors and agents are in [AGENTS.md](AGENTS.md).

## Releasing

* Make sure `gpg-agent` is running.
* Execute `mvn -B release:prepare release:perform`

For publishing the site, do the following (assuming mono-module project):

```
cd target/checkout
mvn verify site site:stage scm-publish:publish-scm
```

