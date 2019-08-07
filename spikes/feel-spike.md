# Intermediate Results of Scala FEEL Spike

## In a Nutshell

### Why Scala FEEL?

* The feature set of ...
  * Java FEEL is [very limited][very-limited] (comparison spreadsheet)
  * Scala FEEL is [feature complete][feature-complete]
* Java FEEL is based on the JUEL engine and it is hard to implement new features

### Why not Scala FEEL?

* Adds more complexity to the product since... 
  * it is written in a completely different programming language
  * the Scala Library has a size of 5 MB and needs to be shipped with our product
* Not compatible with Java 7 (not developed for Java 7; It makes heavy use of Java 8 
Date/Time API)

## Java Compatibility

* The Scala FEEL Engine uses Scala 2.13 which has a minimal requirement of Java 8

### Java 8 EOL

* Scala plans to switch to the next Java LTS Version 12 as minimal requirement
* This is not planned for Scala 2.x
* Dropping Java 8 is likely to happen for Scala 3.0 (which will be released in early 2020)

### Impact

If Scala 2.x maintenance is dropped, we are forced to make Java 12 a minimal requirement for the 
Scala FEEL Engine or accept that the Scala Library will not receive patches anymore.

## Shading

* The Scala Library is included in the Scala FEEL Engine artifact
* The Scala Library package is relocated from `scala` to e. g. `camundajar.scala`
* Imports that point to the `scala` package are rewritten
* There exist JAR optimizers like `ProGuard` to reduce the size of the shaded artifact

### Pros

* The Scala Library version of the Scala FEEL Engine is pinned to a specific version
 * The version cannot be changed by the user and is always the one we test against with our QA
    * We don't need to make sure that multiple Scala Library versions are compatible
 * An additional Scala Library version present on the classpath of the Container does not interfere 
 with the Scala FEEL Engine

### Cons

* The plugin mechanism to write custom Scala functions uses Scala types (which are prefixed after 
shading)

## Breaking Changes

1. String literals can be compared with single quotes in Java Feel engine
2. Built-in functions cannot be used directly in unary tests (may not be compliant to 
specification; To be investigated)
3. Util date as input cannot be compared with built-in date function (always compares against 
time zoned date values, i. e. Java 8 date time API is used
  * To be investigated what is specification compliant in this situation

## User Migration Path

* Java FEEL Engine provides custom function mechanism which is part of private API
* We need to outline how users can migrate their custom functions for Scala Feel Engine

## Action Items & Estimation

### FEEL Engine

#### House Keeping

1.  `nscala-time` not used in Scala FEEL Engine \
    **Action-Item:** Remove dependency \ 
    **Effort:** Easy-pick
2.  `slf4j-api` should not be included in artifact \
    **Action-Item:** Make `slf4j-api` a dependency of scope `provided` \
    **Effort:** Easy-pick 
3.  There exists no `impl` package => this means everything has API guarantees \
    **Action-Item:** Move all classes to `impl` package \
    **Effort:** Easy-pick
4.  No license headers present on source code \
    **Action-Item:** Add license header to all source code files \
    **Effort:** Easy-pick
6.  There exists an updated documentation of FEEL \
    **Action-Item:** Take over comprehensive [`feel-scala` documentation][feel-scala-docs] \
    **Effort:** Medium
7.  There exists a migration guide between the old and the new Custom Function Mechanism \
    **Action-Item:** Write documentation for the migration guide \
    **Effort:** Easy-pick

#### Breaking Changes

1.  String literals must be double quoted according to DMN 1.0/1.1/1.2 (cf. DMN 1.1, pg. 109, 
    section 10.3.2.32) \
    **Action-Item:** Add documentation to migration guide \
    **Effort Estimation:** Easy-pick
2.  Unary test: Function result cannot be compared with implicit equals \
    **Action-Item:** Make it work \
    **Effort Estimation:** Medium
3.  No timezone information for date input expression is compared with date \
    **Action-Item:** Make it work
    * **Case 1:** Input expression value has timezone information and is compared against date 
      value with timezone information => Happy Path!
    * **Case 2:** Input expression value has no timezone information and is compared against 
      date value without timezone information => local time comparision
    * **Case 3:** Input expression value has no timezone information and is compared against 
      date value with timezone information => timezone information of JVM is added to input 
      expression
    * **Case 4:** Input expression value has timezone information and is compared against date 
      value without timezone information => Like Case 3
      
    **Effort Estimation:** Medium

### Camunda BPM Runtime

1.  Camunda specific FEEL artifact is build \
    **Action-Items:**
    * Take over `feel-engine-factory` project in `camunda-bpm-platform` repository
      * `feel-engine` project remains in `feel-scala` project
    * Define maven coordinates of artifact
    * Shade `scala-library` and define a sensible prefix (e. g. `camundajar.`) \
    **Effort:** Easy-pick
2.  Fallback to Java FEEL implementation if Scala FEEL is not available \
    **Action-Items:**
    * In decision engine configuration `Class#forName` lookup is performed for FEEL factory
    * Decision Engine is bootstrapped with Scala FEEL if available
    * If Scala FEEL is unavailable, it falls back to Java FEEL \
    **Effort:** Easy-pick


[feature-complete]: https://github.com/camunda/feel-scala#status
[very-limited]: https://docs.google.com/spreadsheets/d/1eLQjvLTr8nnnQV7h_rMk8fZbyW_oyipj1TO1wTiM_SA/edit
[feel-scala-docs]: https://camunda.github.io/feel-scala/