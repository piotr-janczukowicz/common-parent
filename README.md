# common-parent

Maven parent POM. It binds Checkstyle and SpotBugs with shared config for modules that have Java sources. License: Apache 2.0.

This repository also builds `common-build-config`, a JAR of Checkstyle and SpotBugs XML loaded from the plugin classpath. There is no application Java source here.

## Coordinates

| Field | Value |
| --- | --- |
| groupId | `pl.piotrjanczukowicz` |
| artifactId | `common-parent` |
| version | `1.0.0-SNAPSHOT` |
| packaging | `pom` |

Published to GitHub Packages, not Maven Central:

`https://maven.pkg.github.com/piotr-janczukowicz/common-parent`

## Consume

Inherit it as the Maven parent (empty `relativePath` so Maven does not look for a sibling `pom.xml`):

```xml
<parent>
  <groupId>pl.piotrjanczukowicz</groupId>
  <artifactId>common-parent</artifactId>
  <version>1.0.0-SNAPSHOT</version>
  <relativePath/>
</parent>
```

Checkstyle and SpotBugs run only when `src/main/java` exists (Maven profile `java-sources`). POM-only modules skip them.

Until GitHub Packages has a published build, install from this repository:

```bash
mvn install
```

## GitHub Packages

GitHub Maven packages require authentication even when public. Do not add `<repositories>` to a project POM; the inherited Enforcer rule forbids it. Configure the registry in `~/.m2/settings.xml` (or a CI settings file).

Use a classic personal access token with `read:packages`. Point the server password at an environment variable; do not put the token in the file.

This POM’s parent is `common-dependencies`, so settings must list both GitHub Packages repositories:

```xml
<settings>
  <servers>
    <server>
      <id>github-common-parent</id>
      <username>YOUR_GITHUB_USERNAME</username>
      <password>${env.GH_PACKAGES_TOKEN}</password>
    </server>
    <server>
      <id>github-common-dependencies</id>
      <username>YOUR_GITHUB_USERNAME</username>
      <password>${env.GH_PACKAGES_TOKEN}</password>
    </server>
  </servers>
  <profiles>
    <profile>
      <id>github</id>
      <repositories>
        <repository>
          <id>github-common-parent</id>
          <url>https://maven.pkg.github.com/piotr-janczukowicz/common-parent</url>
          <snapshots>
            <enabled>true</enabled>
          </snapshots>
        </repository>
        <repository>
          <id>github-common-dependencies</id>
          <url>https://maven.pkg.github.com/piotr-janczukowicz/common-dependencies</url>
          <snapshots>
            <enabled>true</enabled>
          </snapshots>
        </repository>
      </repositories>
    </profile>
  </profiles>
  <activeProfiles>
    <activeProfile>github</activeProfile>
  </activeProfiles>
</settings>
```

Pushes to `main` deploy with the workflow `github.token` (`packages: write`). Do not create a repository secret whose name starts with `GITHUB_`.

## Default build

Inheriting this parent binds, when `src/main/java` exists:

- Checkstyle on `validate` (fails on error, production sources only; `checkstyle.xml` from `common-build-config`)
- SpotBugs on `verify` (`Max` effort, `Low` threshold, fails on error, FindSecBugs; `spotbugs-exclude.xml` from `common-build-config`)
