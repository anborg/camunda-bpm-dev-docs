# HowTo Setup Camunda Springboot

- Clone the spring boot starter example [repository](https://github.com/camunda/camunda-bpm-examples/tree/master/spring-boot-starter/example-webapp-ee)
- Check the version compatibility in our [docs](https://docs.camunda.org/manual/develop/user-guide/spring-boot-integration/version-compatibility/) and adjust the pom.xml file accordingly:
```xml
<properties>
  <camunda.version>7.x.x</camunda.version>
  <spring.boot.version>2.1.4.RELEASE</spring.boot.version>
  <camunda.spring.boot.starter.version>3.3.0-SNAPSHOT</camunda.spring.boot.starter.version>
</properties>
```
Detailed information about spring versions can be found on the Maven Repository page for [Spring-Boot](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot) and [Camunda-Spring-Boot-Starter](https://mvnrepository.com/artifact/org.camunda.bpm.springboot/camunda-bpm-spring-boot-starter)
- Enable the Groovy script engine if neccessary. Add following configuration to the pom.xml:
```xml
<!-- https://mvnrepository.com/artifact/org.codehaus.groovy/groovy-jsr223 -->
<dependency>
    <groupId>org.codehaus.groovy</groupId>
    <artifactId>groovy-jsr223</artifactId>
    <version>2.4.13</version>
</dependency>
```

## Application and Engine Configuration
Application and engine configuration can be dset in the appliaction.yaml file. Our [docs](https://docs.camunda.org/manual/develop/user-guide/spring-boot-integration/configuration/#camunda-engine-properties) provide a list of exposed engine properties. Properties that are not exposed can be set as generic engine propertie:
```yaml
camunda:
  bpm:
    generic-properties:
      properties:
        enable-password-policy: true
```

### User autheroization and Admin user configuration
```yaml
camunda:
  bpm:
    filter:
      create: All
    job-execution:
      core-pool-size: 10
    admin-user:
      id: xyz
      password: xyz
    authorization:
      enabled: true
```

## Springboot Database Setup

### MYSQL
#####  pom.xml
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
    <version>${spring.boot.version}</version>
</dependency>
     <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.17</version>
    </dependency>
```
#####  Application.yaml

```yaml
spring:
  datasource:
    url: jdbc:mysql[URL to you database]
    driver-class-name: com.mysql.jdbc.Driver
    username: camunda
    password: camunda
```
For example, the url to a mysql DB in our new portainer environment would look like this:
```
jdbc:mysql://portainer.camunda.loc:30037/process-engine?useUnicode=true&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC
```

### PostgreSQL
#####  pom.xml
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
    <version>${spring.boot.version}</version>
</dependency>
    <dependency>
      <groupId>org.postgresql</groupId>
      <artifactId>postgresql</artifactId>
      <version>9.4.1211.jre6</version>
    </dependency>
```
#####  Application.yaml

```yaml
spring:
  datasource:
    url: jdbc:postgresql:[URL to your database]
    driver-class-name: org.postgresql.Driver
    username: camunda
    password: camunda
```

## Enable the REST API

Follow the instructions on our [docs](https://docs.camunda.org/manual/develop/user-guide/spring-boot-integration/rest-api/) to enable the REST API. To change the REST API path add the following configuration in the appliaction.yaml file:
```yaml
spring:
  jersey:
    application-path: engine-rest
```
