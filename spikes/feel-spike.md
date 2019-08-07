# Intermediate Results of Scala FEEL Spike

## In a Nutshell

### Why Scala FEEL?

* The feature set of ...
  * Java FEEL is [very limited](very-limited)
  * Scala FEEL is [feature complete](feature-complete)
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
Scala FEEL Engine or accept that the Scala Library will not be updated anymore.

## Shading

* The Scala Library is included in the Scala FEEL Engine artifact
* The Scala Library package is relocated from `scala` to e. g. `camundajar.scala`
* Imports that point to the `scala` package are rewritten

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

## Migration Path

* Java FEEL Engine provides custom function mechanism which is part of private API
* We need to outline how users can migrate their custom functions for Scala Feel Engine

[feature-complete]: https://github.com/camunda/feel-scala#status
[very-limited]: https://docs.google.com/spreadsheets/d/1eLQjvLTr8nnnQV7h_rMk8fZbyW_oyipj1TO1wTiM_SA/edit#gid=0