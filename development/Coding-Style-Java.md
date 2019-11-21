## Intro:

* These are the coding style guidelines that should be used for writing Java code
* Rules must always be followed
* Best practices should be followed, but exceptions can be made

## Rules:

### General Formatting

* Indentation is two spaces
* Indentation for chaining method calls on new lines is four spaces
* No trailing whitespace; no whitespace on empty lines
* Lines of code must not exceed 100 characters
* If lines with method signatures exceed 100 characters, each parameter must be on a new line
* Bracket placement: [Kernighan and Ritchie style](https://en.wikipedia.org/wiki/Indentation_style#K&R_style) ("Egyptian brackets") for non-empty blocks and block-like constructs; plus: opening brackets of classes and methods go on the same line as the declaration
```java
return new MyClass() {
  @Override
  public void method() {
    if (condition()) {
      try {
        something();
      } catch (ProblemException e) {
        recover();
      }
    } else if (otherCondition()) {
      somethingElse();
    } else {
      lastThing();
    }
  }
};
```
* Local variables must be declared close to their first usage, not at the beginning of the block
* Names of static final member variables (class fields) are uppercase with words separated by underscores ("_"); All other variable names are camel case

### Variables
* On declaring variables (local or global), always use the highest possible type that your implementation still can handle, e. g. ... 
  * when declaring a map, prefer the `Map` interface over a concrete implementation type like `HashMap`
  * `Collection` vs. `List` vs. `ArrayList`

### Files

* Files must be encoded in UTF-8

### Java Keywords

* In our main codebase (i.e. no tests), modifier `private` is not allowed for fields and methods; `protected` should be used instead; this allows our users to work around problems or extend the engine easier
* Always use `@Override` when you override or implement a method
* Do not use `@author` tags; Do not remove existing tags
* Avoid redundant interface modifiers: public (methods / fields), static (fields) & final (fields)
* Annotations placement:
  * Methods, fields and classes: Above the declaration, one annotation per line
  * Method parameters and local variables: On the same line
* Always use a default branch in switch-case statements, may be omitted for switch statements on enums if all values are used
* Use the recommended order of modifiers: `public protected private abstract default static final transient volatile synchronized native strictfp`

### Java Language Features

* Use Diamond Operator (`List<String> list = new ArrayList<>();`), [try-with-resources](https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html), underscores in numeric literals when it improves readability

### Imports

* Do not use star imports
* All unused imports must be removed
* Static imports in a single block
* Non-static imports in a single block, divided from static imports by a blank line

### Need exact proposal:

* Import order consistent for all IDEs
* Everyone should commit the same line endings
* Consistent placement of new lines of a source file

### Comments

* Do not push `TODO` or `FIXME` comments to master. Instead, either fix it immediately or create a JIRA issue that describes the user impact.
* Add Javadoc comments to new `ProcessEngineConfigurationImpl` and `ProcessEngineConfiguration` properties to make the purpose of the property visible to users and developers without the need of diving into code.

### Specifics

* Make setters in `ProcessEngineConfigurationImpl` and `ProcessEngineConfiguration` fluent

## Best Practices:

* Within the same source file, the ordering of annotations should be consistent
* Consider writing Javadoc for non-trivial code that you encounter. This can be new code or already existing code that you inspected for a current task.

### Need exact proposal:

* No IDE warnings should be committed
  * Eclipse/IntelliJ settings that produce the same amount of warnings should be used
