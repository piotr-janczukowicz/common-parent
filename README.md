[![Codacy Badge](https://app.codacy.com/project/badge/Grade/66957501585041d8a742c01c3d00591b)](https://app.codacy.com/gh/piotr-janczukowicz/common-parent/dashboard?utm_source=gh&utm_medium=referral&utm_content=&utm_campaign=Badge_grade)
[![Build](https://github.com/piotr-janczukowicz/common-parent/actions/workflows/main.yml/badge.svg)](https://github.com/piotr-janczukowicz/common-parent/actions/workflows/main.yml)
[![Maven Central](https://img.shields.io/maven-central/v/io.github.piotr-janczukowicz/common-parent)](https://central.sonatype.com/artifact/io.github.piotr-janczukowicz/common-parent)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
![Java 21](https://img.shields.io/badge/Java-21-orange.svg)

# common-parent

The Maven parent POM of the `io.github.piotr-janczukowicz` libraries. It pins the dependency and plugin versions, runs the
quality checks on every module that has Java sources, and holds the profiles and the reusable GitHub Actions workflows that
release a library to Maven Central. Set it as the parent of a project and the same build, checks and release steps apply.

**Requirements:** Java 21 and Maven 3.9 or newer (the build fails on other versions). It is published on
[Maven Central](https://central.sonatype.com/artifact/io.github.piotr-janczukowicz/common-parent) as a `pom`:

```xml
<parent>
    <groupId>io.github.piotr-janczukowicz</groupId>
    <artifactId>common-parent</artifactId>
    <version>13</version>
    <relativePath/>
</parent>
```

Versions are whole numbers (`13`); a library that has its own release line keeps the number as the last part, like
`2026.3.10.0`.

## What a project gets

| | |
|---|---|
| **Compiler** | Java 21 (`maven.compiler.release`), `-parameters`, and the [record-builder](https://github.com/Randgalt/record-builder) annotation processor |
| **Dependency versions** | Managed for Jackson 3 and JUnit 6 (both by BOM), SLF4J, JSpecify, OkHttp, AssertJ, Mockito, WireMock, ArchUnit, ByteBuddy, LogCaptor, json-schema-validator and record-builder. List only the artifact, never the version |
| **Default dependencies** | `jspecify` (compile) and JUnit Jupiter, AssertJ, Mockito and Mockito's JUnit extension (test) in every module |
| **Plugin versions** | Pinned for every plugin the build uses, and kept up to date by Dependabot |
| **Checks** | Spotless, tidy, the enforcer, Checkstyle, PMD and JaCoCo, described below |
| **Profiles** | `java-sources`, `release`, `central` and the opt-in `pmd-test` |

## Quality checks

Every check fails the build, so a module that passes `mvn verify` is formatted and covered the same way as every other one.

- **Spotless** (`validate`): Palantir Java Format, formatted Javadoc and annotations, no trailing whitespace, a final newline.
  Run `mvn spotless:apply` to fix a violation.
- **tidy** checks that `pom.xml` is formatted. **Enforcer** (`verify`) requires Java `[21,22)` and Maven `3.9.0` or newer,
  forbids `<repositories>` in a project POM (use `settings.xml` or a mirror) and duplicate dependency versions.
- **Checkstyle** with the configuration of [`common-build-config`](common-build-config), on modules with Java sources.
- **PMD** with the ruleset in the module's own `pmd/pmd-ruleset.xml`, which each project owns. Add `-Ppmd-test` to run every
  PMD rule on the test sources too, with the rules from `common-build-config`.
- **JaCoCo** merges the unit-test and integration-test coverage and enforces these minimums, on the whole module and on every
  class:

  | Counter | Module | Class |
  |---|---|---|
  | Instruction | 98 % | 95 % |
  | Branch | 85 % | 85 % |
  | Line | 98 % | 98 % |
  | Complexity | 90 % | 90 % |
  | Method | 100 % | 100 % |
  | Class | 100 % | |

Checkstyle, PMD and JaCoCo belong to the `java-sources` profile, which is active when the module has a `src/main/java`
folder, so a module with only a POM, like this one, skips them.

## Profiles

| Profile | When | What it does |
|---|---|---|
| `java-sources` | automatic, when `src/main/java` exists | Checkstyle, PMD and the JaCoCo gate |
| `release` | the release workflows | Skips the tests and the checks: the code on `main` has already passed them |
| `central` | the publish workflow | Attaches sources and Javadoc, signs the artifacts with GPG and uploads them to Maven Central |
| `pmd-test` | `-Ppmd-test`, opt in | Runs all PMD rules on the test sources and fails on a violation |

## Reusable workflows

The release steps live here, so every repository releases the same way. A repository calls them from its own workflows and
pins the commit of a `vN` tag:

```yaml
jobs:
  release:
    permissions:
      actions: read
      contents: write
      packages: write
    if: github.ref == format('refs/heads/{0}', github.event.repository.default_branch)
    uses: piotr-janczukowicz/common-parent/.github/workflows/reusable-release-package.yml@<commit sha of a vN tag> # vN
    secrets: inherit
```

| Workflow | What it does |
|---|---|
| `reusable-release-package.yml` | Works out the next version, deploys it to GitHub Packages, tags it and creates the GitHub release. It waits for a green Main run of the same commit |
| `reusable-next-snapshot.yml` | Opens the pull request that moves the POMs to the next `-SNAPSHOT` after a release |
| `reusable-publish.yml` | Builds a tag, signs it and publishes it to Maven Central |

`common-parent` itself keeps its own `release.yml`: it also sets the `common-build-config.version` property and releases
every module.

## Modules

- **`common-build-config`** holds `checkstyle.xml` and `pmd-test-ruleset.xml`. The parent pulls them in as plugin
  dependencies, so a project does not copy them.

## Security

See [SECURITY.md](SECURITY.md) to report a vulnerability.

## License

See [LICENSE](LICENSE).
