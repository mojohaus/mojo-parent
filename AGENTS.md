# Agent guide for mojo-parent

`org.codehaus.mojo:mojo-parent` (packaging `pom`) is the parent POM inherited by
every MojoHaus Maven plugin and component. It centralises compiler, Checkstyle, Spotless,
animal-sniffer, Enforcer, Surefire, Failsafe, Invoker and site configuration; there are no goals or
application sources here, and the site source is `src/site/markdown/index.md`.

## Build and test

- CI runs `mvn -e -B -V verify site` on JDK 8 and 21 (`.github/workflows/maven.yml`); use that before pushing a POM change.
- The parent sets `mojo.java.target` to 8, sets the Maven API that plugins compile against in `mavenVersion` and the Maven needed to build in `minimalMavenBuildVersion`, and manages JUnit 5 through `junit-bom` (`junit5.version`).
- Spotless applies formatting at `process-sources` locally, but the `format-check` profile switches it to `check` when `env.CI` is set, so run `mvn spotless:apply` after editing POMs or Markdown.

## Documentation updates

A change that users or contributors can notice ships with the documentation it affects, in the same pull
request. A refactor with no visible effect needs none.

- A new or changed property, profile, Enforcer rule or managed plugin behaviour: the matching section of
  `src/site/markdown/index.md` (Versions and requirements, Build rules, Profiles, Spotless, Checkstyle). The page is
  plain Markdown; it names no parent or plugin version except the one in the `<parent>` example, written as
  `<version><!-- latest release --></version>`.
- Metadata at the top of a page (`title:`, `date:`) has no `---` fences: Spotless would turn the closing fence into
  an underline and the page would render empty.
- A page added, removed or renamed: the menu in `src/site/site.xml`. Never rename a published page; its URL is
  linked from elsewhere.
- The Maven or Java floor, or how to release: `README.md`.
- A new convention, build gate or test fixture: this file.

Check the result with `mvn site` and open `target/site/index.html`.
