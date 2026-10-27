[![Maven Central](https://img.shields.io/maven-central/v/io.github.piotr-janczukowicz/common-parent)](https://central.sonatype.com/artifact/io.github.piotr-janczukowicz/common-parent)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
![Java 21](https://img.shields.io/badge/Java-21-orange.svg)
# common-parent

This module is a Maven parent that binds Checkstyle with shared config. Set it as your project parent so that check runs on modules that have Java sources.

## Dependency

Java 21 and Maven. Coordinates: `io.github.piotr-janczukowicz:common-parent:1` (`pom`).

```xml
<parent>
  <groupId>io.github.piotr-janczukowicz</groupId>
  <artifactId>common-parent</artifactId>
  <version>1</version>
  <relativePath/>
</parent>
```

## Usage

Put production Java under `src/main/java`. Maven then runs Checkstyle on `validate`.

```xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>io.github.piotr-janczukowicz</groupId>
    <artifactId>common-parent</artifactId>
    <version>1</version>
    <relativePath/>
  </parent>
  <artifactId>example</artifactId>
</project>
```

## License

MIT License
