## jpa-entity-generator

[![Build Status](https://travis-ci.org/smartnews/jpa-entity-generator.svg?branch=master)](https://travis-ci.org/smartnews/jpa-entity-generator)
[![Maven Central](https://maven-badges.herokuapp.com/maven-central/com.smartnews/jpa-entity-generator/badge.svg)](https://maven-badges.herokuapp.com/maven-central/com.smartnews/jpa-entity-generator)

This is a Java library which generates Lombok-wired JPA entity source code. The project provides Gradle plugin and Maven plugin.

### Getting Started

#### build.gradle

```groovy
apply plugin: 'java'

buildscript {
  dependencies {
    classpath 'com.h2database:h2:1.4.197'
    classpath 'com.smartnews:jpa-entity-generator:0.99.2'
  }
}

configurations { providedCompile }
sourceSets.main.compileClasspath += configurations.providedCompile
sourceSets.test.compileClasspath += configurations.providedCompile
sourceSets.test.runtimeClasspath += configurations.providedCompile

repositories {
  mavenCentral()
}
dependencies {
  providedCompile 'org.projectlombok:lombok:1.16.20'
  providedCompile 'org.hibernate.javax.persistence:hibernate-jpa-2.1-api:1.0.0.Final'
}

apply plugin: 'entitygen'
entityGen {
    configPath = 'src/main/resources/entityGenConfig.yml'
}
```

##### For Maven users

Maven plugin to run the code generator is also available.

```xml
<plugin>
  <groupId>com.smartnews</groupId>
  <artifactId>maven-jpa-entity-generator-plugin</artifactId>
  <version>0.99.2</version>
  <dependencies>
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.46</version>
    </dependency>
  </dependencies>
</plugin>
```

Put `src/main/resources/entityGenConfig.yml`, and then run the following command:

```
mvn jpa-entity-generator:generateAll
```

#### src/main/resources/entityGenConfig.yml

```yaml
jdbcSettings:
  url: "jdbc:h2:file:./db/blog;MODE=MySQL"
  username: "user"
  password: "pass"
  driverClassName: "org.h2.Driver"

packageName: "com.example.entity"
```

If you need more examples, check the sample `entityGenConfig.yml` in this repository.

- https://github.com/smartnews/jpa-entity-generator/tree/master/src/test/resources/entityGenConfig.yml

#### entityGen task

```bash
./gradlew entityGen compileJava
```

`entityGen` task generates entity classes for all the existing tables in the database.

```java
package com.example.entity;

import java.sql.*;
import javax.persistence.*;
import lombok.Data;

@Data
@Entity(name = "com.example.entity.Blog")
@Table(name = "blog")
public class Blog {

  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  @Column(name = "\"id\"")
  private Integer id;
  @Column(name = "\"name\"")
  private String name;
  @Column(name = "\"active\"")
  private Byte active;
  @Column(name = "\"created_at\"")
  private Timestamp createdAt;
}
```

### How to release new version

```bash
./gradlew clean uploadArchives
mvn deploy -Dmaven.test.skip=true
```
