## What is a JDK?

* JDK = Implementation of Java + Bytecode Compiler + JVM (z.B. Hotspot, Eclipse J9, ..) + secondary tools (e.g. java fligh recorder)

Overview over 3rd party OpenJDK builds: https://dzone.com/articles/java-and-the-jdks-which-one-to-use
Overview of the transition in Oracle's development and release model: https://www.azul.com/eliminating-java-update-confusion/

### Azul Zulu:

* Zulu is OpenJDK with HotSpot
* Azul does its own backports of critical fixes (not clear if/when they contribute those back to OpenJDK)
* Builds are free to use in production; Enterprise support available

* Details by an Azul Developer: https://www.reddit.com/r/java/comments/8jvv8e/what_would_be_the_reasons_to_use_the_zulu_build/
* High-level Comparison to other JDKs: https://www.azul.com/products/zulu-enterprise/jdk-comparison-matrix/

### Amazon Corretto:

* Corretto is OpenJDK with HotSpot;
* Amazon does its own backporting of bugfixes:
  "Patches will include security fixes, performance enhancements (e.g., speeding up frequently-used functions), garbage collection scheduling, and preventing out-of-memory situations, as well as improved monitoring, reporting, and thread management."
* Not clear when they contribute those back to OpenJDK

* FAQ: https://aws.amazon.com/de/corretto/faqs/

### Adopt OpenJDK:

* Adopt guarantees builds of OpenJDK 8 and 11 for a long time (until 2023)
* Does not maintain any OpenJDK code, i.e. they do not backport anything themselves

* https://www.reddit.com/r/java/comments/9hd97k/openjdk_vs_adoptopenjdk/
* http://mail.openjdk.java.net/pipermail/jdk-dev/2018-August/001830.html

### Oracle OpenJDK:

* Oracle provides builds and fixes only for the currently latest Java version

### OracleJDK:

* Oracle builds its own patches for its LTS releases (8, 11, 17, ...)
* Contributes fixes back to OpenJDK only to the currently newest version

* Relevant discussion (check also previous messages): http://mail.openjdk.java.net/pipermail/jdk-dev/2018-August/001843.html
