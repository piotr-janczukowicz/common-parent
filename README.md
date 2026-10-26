# common-parent

This module is a Maven parent that binds Checkstyle and SpotBugs with shared config. Set it as your project parent so those checks run on modules that have Java sources.

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

Put production Java under `src/main/java`. Maven then runs Checkstyle on `validate` and SpotBugs on `verify`.

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
