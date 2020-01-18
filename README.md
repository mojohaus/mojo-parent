# MojoHaus Parent

This is the parent pom for all [MojoHaus](https://www.mojohaus.org) Maven plugins and components.
 
[![Maven Central](https://img.shields.io/maven-central/v/org.codehaus.mojo/mojo-parent.svg?label=Maven%20Central)](https://search.maven.org/search?q=g%3Aorg.codehaus.mojo+AND+a%3Amojo-parent)
[![Build Status](https://travis-ci.org/mojohaus/mojo-parent.svg?branch=master)](https://travis-ci.org/mojohaus/mojo-parent)

## Releasing

* Make sure `gpg-agent` is running.
* Execute `mvn -B release:prepare release:perform`

For publishing the site, do the following (assuming mono-module project):

```
cd target/checkout
mvn verify site site:stage scm-publish:publish-scm
```
