Parent POM for all MojoHaus hosted Apache Maven plugins and components.

Latest build status: [![GitHub CI](https://github.com/mojohaus/mojo-parent/actions/workflows/maven.yml/badge.svg)](https://github.com/mojohaus/mojo-parent/actions/workflows/maven.yml)

Changelog
=========

The changes of every release are listed on the [GitHub releases page](https://github.com/mojohaus/mojo-parent/releases).

How to use the POM
==================

* for general information about using a parent pom take a look at https://maven.apache.org/guides/introduction/introduction-to-the-pom.html.
* it boils down to define a parent element in your own POM:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>mojo-parent</artifactId>
        <version><!-- latest release --></version>
    </parent>
    <groupId>sample-groupId</groupId>
    <artifactId>sample-project</artifactId>
    <version>1.0.0</version>
    [...]
</project>
```

Versions and requirements
=========================

The parent defines the versions that a MojoHaus build is compiled and checked against. Override them in the
`<properties>` of your POM where a project needs something else.

|          Property          |   Default    |                                                             Meaning                                                             |
|----------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------|
| `mojo.java.target`         | `8`          | The Java release to compile for. Sets `maven.compiler.source`, `target` and `release`, and the Java version the build requires. |
| `minimalMavenBuildVersion` | `3.9`        | The Maven version needed to build the project. The Enforcer plugin fails the build on an older Maven.                           |
| `mavenVersion`             | latest 3.9.x | The version of `maven-plugin-api` that the parent manages, and so the Maven API a plugin compiles against.                      |
| `junit5.version`           |              | The version of the imported `junit-bom`; JUnit 4 is not managed.                                                                |

`minimalMavenBuildVersion` and `mavenVersion` are separate on purpose: raising the version a plugin compiles
against must not raise the Maven version that its users need. A plugin declares the Maven version it needs at run
time as a literal in `<prerequisites>`.

The version of every plugin that the parent manages is in a property named after the plugin, such as
`maven-surefire-plugin.version`, `maven-invoker-plugin.version` or `spotless-maven-plugin.version`; set the
property to use another version. The parent also configures the Invoker plugin to stream the logs of failed tests
and to rerun a failing test once (`invoker.streamLogsOnFailures`, `invoker.rerunFailingTestsCount`), and the
Surefire plugin to redirect test output to files (`surefire.redirectTestOutputToFile`).

Build rules
===========

The Enforcer plugin runs in the `validate` phase and fails the build when:

* the Maven or Java version is below `minimalMavenBuildVersion` or `mojo.java.target`,
* a plugin version is `LATEST` or `RELEASE`, or a plugin has no version,
* the POM declares repositories, other than snapshot repositories and the plugin repositories `sonatype-central-portal`
  and `apache.snapshots`,
* a dependency on `org.codehaus.plexus:plexus-component-api` is present, because it conflicts with the container
  of Maven,
* `project.scm.connection` is not of the form `scm:git:https://github.com/...git`, or `project.scm.url` is not an
  `https://github.com/...` URL.

Profiles
========

|             Profile              |                  Active                   |                                                           Effect                                                           |
|----------------------------------|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------|
| `java17+`                        | on JDK 17 and later                       | Runs Spotless, whose formatters need JDK 17.                                                                               |
| `format-check`                   | when the `CI` environment variable is set | Makes Spotless check the formatting instead of fixing it.                                                                  |
| `mojo-release`                   | on request                                | Used for a release: publishes through Njord, and attaches the source release, the sources, the Javadoc and the signatures. |
| `reporting`                      | unless `-DskipReports=true`               | Adds the Javadoc, plugin and project information reports to the site.                                                      |
| `palantir-javaformat-dependabot` | on request                                | Only lets Dependabot see the version of the Palantir formatter.                                                            |

Spotless - automatic code formatting
====================================

`MojoHaus` Parent POM contains configuration for [spotless-maven-plugin](https://github.com/diffplug/spotless/tree/main/plugin-maven)
with options:

- [palantir](https://github.com/palantir/palantir-java-format) code formatter
- imports order as:
  - javax
  - java
  - other imports
  - static import

`spotless` is active in your project by default on JDK 17 and later, and runs in the `process-sources` phase. It
fixes the formatting of Java, POM and Markdown files (`**/*.md`) in place, and when the `CI` environment variable
is set it fails the build instead. If you don't want to use it:

```xml
<properties>
   <spotless.skip>true</spotless.skip>
</properties>
```

`spotless.apply.skip` and `spotless.check.skip` skip only the fixing or only the check. To fix code, simply run:

```
mvn spotless:apply
```

Keep the metadata of a Markdown page, such as `title:`, without `---` lines: the Markdown formatter turns the
closing line into an underline, and the page renders empty.

Checkstyle
==========

You can use `checkstyle` in order to verify code on each build.

Checkstyle can be enabled by adding to `build/plugins`:

```xml
<plugin>
   <groupId>org.apache.maven.plugins</groupId>
   <artifactId>maven-checkstyle-plugin</artifactId>
</plugin>
```

The parent sets the plugin up to check `src/main/java` and `src/test/java` in the `process-sources` phase, with a
small rule set that leaves code style to Spotless (`checkstyle.config.location`). It does not check file headers by
default.

Big code reformat
=================

After applying bigger reformatting it is recommended to add or update a `.git-blame-ignore-revs` file
in the root of the repository containing a line with the SHA1 of the formatting commit to ignore those changes
when using `git blame` (or equivalent concepts).

This is automatically considered by [GitHub](https://docs.github.com/en/repositories/working-with-files/using-files/viewing-a-file#bypassing-git-blame-ignore-revs-in-the-blame-view)
and can optionally be considered with [local `git blame`](https://git-scm.com/docs/git-blame#Documentation/git-blame.txt---ignore-revs-fileltfilegt)

Latest releases
===============

... may be found at [Maven Central](https://central.sonatype.com/artifact/org.codehaus.mojo/mojo-parent).

Deploy site to github
=====================

To deploy a site to github using [maven-scm-publish-plugin](https://maven.apache.org/plugins/maven-scm-publish-plugin/):

* Add a new branch `gh-pages` [manually](https://help.github.com/articles/creating-project-pages-manually).
* Add `src/site/resources/.nojekyll` [to add hidden files (e.g. for jacoco)](https://illegalstateexception.blogspot.de/2013/01/publishing-maven-site-having-jacoco.html) as well.
* Then after a release do:

```
cd target/checkout
mvn -Preporting verify site-deploy
```

