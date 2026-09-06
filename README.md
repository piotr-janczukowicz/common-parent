# common-parent

Maven parent POM. Application modules inherit this; it brings `common-dependencies` and binds Checkstyle and SpotBugs with shared config.

```xml
<parent>
  <groupId>pl.piotrjanczukowicz</groupId>
  <artifactId>common-parent</artifactId>
  <version>1.0.0-SNAPSHOT</version>
  <relativePath/>
</parent>
```
