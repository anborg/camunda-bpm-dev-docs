# Maintaining Backwards Compatibility

We guarantee backwards compatibility for our *public API*.

Types of backwards compatibility:

* Binary backwards compatibility
* Behavioral backwards compatibility
* Database schema backwards compatibility

## Binary Backwards Compatibility

### Definition

A client application that was compiled against 7.X can run with 7.Y (Y > X) without being recompiled.

### What kind of changes are binary compatible?

See https://docs.oracle.com/javase/specs/jls/se7/html/jls-13.html

### Toolbox

#### Add API functionality in a restricted context

Let's assume we want to extend the `ProcessInstance` interface, but the method makes only sense in the context
of process instantiation. When making a process instance query, that method should not be accessible. Thus, adding
the method to `ProcessInstance` is not a solution.

Our current instantiation method is:

```java
public interface RuntimeService{
  ProcessInstance startProcessInstanceByKey(String key);
}
```

Note that changing the declared return type breaks binary compatibility. That means, this is not a solution:

```java
public interface RuntimeService{
  EnhancedProcessInstance startProcessInstanceByKey(String key);
}
```

##### Solution 1: Add new a new API method

We add a new method to `RuntimeService` (or the instantiation builder), that returns our desired interface. The existing method
returning `ProcessInstance` must remain or else we break binary compatibility.

```java
public interface RuntimeService{
  ProcessInstance startProcessInstanceByKey(String key);

  EnhancedProcessInstance startProcessInstanceByKeyAndReturnMore(String key);
}
```

Advantages:

* The extra method makes users aware that it provides added functionality

Disadvantages:

* Cluttered API. Users unaware of the API's evolution may be confused by the fact that there are multiple methods to achive a goal.

##### Solution 2: Type-erasure hack

We extend the existing API method's return value and declare it to be an implementation of both `ProcessInstance` and `EnhancedProcessInstance`. See http://www.angelikalanger.com/GenericsFAQ/FAQSections/ProgrammingIdioms.html#FAQ104.

```java
public interface RuntimeService{
  <T extends ProcessInstance & EnhancedProcessInstance> T startProcessInstanceByKey(String key);
}
```

Note that the compiler erases the generic type `T` to the left-most upper bound in the list, here `ProcessInstance`. Thus, adding new interfaces this way **must**
append to the right of the list. See http://www.angelikalanger.com/GenericsFAQ/FAQSections/TechnicalDetails.html#FAQ105

Advantages:

* We remain with one method

Disadvantages:

* Writing `ExecutionEntity execution = runtimeService.startProcessInstanceByKey("foo");` would not result in a compilation error
* Since the erased type is `ProcessInstance`, IDE auto-completion tools are going to generate `ProcessInstance processInstance = runtimeService.startProcessInstanceByKey("foo");` when assigning the result to a variable
* Users may be confused by the rare syntax
* Implementing the method requires to cast to the unknown type `T`
* Apparently, the compiler does not detect a problem with for example `Job job = runtimeService.startProcessInstanceByKey("foo")` and this only fails at runtime

## Behavioral backwards compatibility

From one version to the next, the behavior of an API method must not change. In other words: If I call an API method `ApiService#foo` with parameters `a` and `b` (i.e. `ApiService.foo(a, b)`), then it has the same side effects and return value on all Camunda versions in which it exists.

Exceptions:

* The old behavior is a bug in any usage scenario.
* The old behavior is a technicality that is not specified by Javadoc or the product documentation (e.g. changing a `List` return value from `null` to an empty list)
* The old behavior is deliberately changed; Constraints: This must be described in the migration guide and there shold be a configuration flag to restore the old behavior, so people can opt-in to backwards compatibility.

## Database schema backwards compatibility

### Restrictions on Schema Evolution

Sql Scripts need to be backwards compatible. When the Database schema is changed from one version of the process engine to the next version, it must be ensured that the old version of the process engine can work on the new schema. This situation intermittierend exists while a user performs a rolling upgrade.

The following is a list of **changes I can do** to the schema because these changes maintain  backwards compatibility:

* Create New Table
* Alter Table: 
    * Add new column (nullable)
    * Add new  column (not null but with default value)
    * Add index on existing column(s) (non-unique)
    * Add index on new columns (unique and non-unique)

The following is an incomplete list of **changes I cannot do** to the schema because these changes would break backwards compatibility:

* Delete or rename a Table
* Delete or rename a Column
* Changing the type of a Column
* Add a new non-nullable column without a default value.
* Add constraint like foreign key, unique, check or not null without default to existing column 
* Add new column with foreign key constraint


### Restrictions on Data Evolution

This section covers restrictions on changes that can be made to the way data is stored in the database.

#### Structured Data

Structured data is when a data-structure (like the execution tree) is stored in a structured way from the point of view of the database.

Restrictions on Data Evolution

* An update of the process engine **must not structuraly change the data**. Example: if the previous version of the process engine used to create a dedicated execution for activities with an i/o mapping, the new version of the process engine must also create a dedicated execution. Other example: IS_SCOPE / IS_CONCURRENT / IS_ACTIVE ... must have same values in the same situations.

#### Unstructured Data

Unstructured data is when a data-structure (like a map) is stored in an unstructured way from the point of view of the database. Example is the storing of filter data as a JSON object.

Restrictions on Data Evolution

* An update of the process engine **must not remove required fieds** from the data. When a new version of the process engine writes unstructured data, it must not ommit fields in the serialized data which the previous version of the process engine requires to be present. This would break rolling upgrade scenarios.

