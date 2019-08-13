# Intermediate Results of Scala FEEL Spike

## Table of Contents

* [Pros & Cons](#pros--cons-in-a-nutshell)
* [Java Compatibility](#java-compatibility)
* [Shading](#shading)
* [Breaking Changes](#breaking-changes)
* [Action Items & Estimation](#action-items--estimation)
* [To be Investigated](#to-be-investigated)

## Pros & Cons in a Nutshell

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

* Scala plans to switch to the next Java LTS Version 11 as minimal requirement
* This is not planned for Scala 2.x
* Dropping Java 8 is likely to happen for Scala 3.0 (which will be released in early 2020)

### Impact

If Scala 2.x maintenance is dropped, we are forced to make Java 11 a minimal requirement for the 
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

In the course of the Spike a [bunch of test cases][test-cases] were created to spot breaking 
changes.

### Public API
1. String literals can be compared with single quotes in Java Feel engine
2. Expression result cannot be compared with equals in unary test
3. Util date as input cannot be compared with built-in date function (always compares against 
time zoned date values, i. e. Java 8 date time API is used)
4. Handling SPIN values has changed, i. e. Scala FEEL Engine automatically maps values to the FEEL 
structure "context"
5. Joda DateTime (with timezone) cannot be compared anymore (not mapped by Scala FEEL engine)

### Private API

1. Java FEEL Engine provides custom function mechanism which is part of private API
   * We need to outline how users can migrate their custom functions for Scala Feel Engine

## Factory vs. Engine Plugin
* The `feel-engine` registers two Java `javax.script.ScriptEngine` names (i. e. `feel-scala`, `feel-scala-unary-tests`) via `src/main/resources/META-INF/services/javax.script.ScriptEngineFactory`
* The Engine Plugin changes the default expression languages of the DMN Engine to the respective `ScriptEngine` names
* If the Scala Feel Engine is registered with the help of the Factory in the `DefaultDmnEngineConfiguration`, it is not available as `ScriptEngine`. This has the following main downsides:
  * The Custom Function Mechanism is not available
  * Performance is worse compared to the `ScriptEngine` registration (`ScriptEngine` makes heavy use of caching)

## To be Investigated
* Process engine context functions (e.g. `now()`, `currentUser()`, etc.) are available in a redundant manner
* Optimized shaded Jar to reduce the size of Scala Library
* Scala DMN Engine

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
2.  Unary test: Function result cannot be compared with implicit equals according to DMN 1.2 (cf. DMN 1.2, pg. 92, section 8.3.3 a.) \
    **Action-Item:** Make it work \
    **Effort:** Medium
3.  Util date as input value can be compared with date value without timezone information according to DMN 1.1 (cf. DMN 1.1, pg. 110, section 10.3.2.3.6) \
    **Action-Item:** Make it work \
    **Effort:** Medium
4. SPIN XML & JSON values are automatically mapped to the FEEL structure "context" (cf. DMN 1.1, pg. 125, section 10.3.3) \
   In a Java FEEL expression it is possible to access SPIN values via SPIN Java API \
   **Action-Item:** Add documentation to migration guide \
   **Effort:** Easy-pick
5. Joda DateTime (with timezone) cannot be compared anymore \
   **Action-Item:** Make it work \
   **Effort:** Easy-pick

### Camunda BPM Runtime

1.  Camunda specific FEEL artifact is build \
    **Action-Items:**
    * Take over `feel-engine-factory` project in `camunda-bpm-platform` repository
      * `feel-engine` project remains in `feel-scala` project
    * Define maven coordinates of artifact
    * Shade `scala-library` and define a sensible prefix (e. g. `camundajar.`)
    
    **Effort:** Easy-pick
2.  Fallback to Java FEEL implementation if Scala FEEL is not available \
    **Action-Items:**
    * In decision engine configuration `Class#forName` lookup is performed for FEEL factory
    * Decision Engine is bootstrapped with Scala FEEL if available
    * If Scala FEEL is unavailable, it falls back to Java FEEL
    
    **Effort:** Easy-pick


[feature-complete]: https://github.com/camunda/feel-scala#status
[very-limited]: https://docs.google.com/spreadsheets/d/1eLQjvLTr8nnnQV7h_rMk8fZbyW_oyipj1TO1wTiM_SA/edit
[feel-scala-docs]: https://camunda.github.io/feel-scala/
[test-cases]: https://github.com/camunda/camunda-bpm-platform/tree/feel-scala-integration
