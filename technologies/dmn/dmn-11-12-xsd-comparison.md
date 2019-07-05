### Comparison of the DMN 1.1 and 1.2 XML schemas:

#### `tDefinitions`

* `typeLanguage` has a new default value (old: `http://www.omg.org/spec/FEEL/20140401`; new: `http://www.omg.org/spec/DMN/20180521/FEEL/`)

#### `tDefinitions`

* `expressionLanguage` has a new default value (old: `http://www.omg.org/spec/FEEL/20140401`; new: `http://www.omg.org/spec/DMN/20180521/FEEL/`)

#### `tInvocable`

* new abstract type

#### `tBusinessKnowledgeModel`

* sub class of `tInvocable`

#### `tInformationRequirement`

* sub class of `tDMNElement`

#### `tAuthorityRequirement`

* sub class of `tDMNElement`

#### `tExpression`

* attribute `typeRef` ist now `string` instead of `qname`

#### `tItemDefinition`

* element `typeRef` ist now `string` instead of `qname`

#### `tInformationItem`

* element `typeRef` ist now `string` instead of `qname`

#### `tDecisionTable`

* new child element `annotation` (minOccurs 0)

#### `tRuleAnnotation`

* new type

#### `tRuleAnnotationClause`

* new type

#### `tContextEntry`

* sub class of `tDMNElement`

#### `tFunctionDefinition`

* new attribute `kind` (default `FEEL`)

#### `tFunctionKind`

* new type

#### `tDecisionService`

* element `outputDecision` has now minOccurs 0 (was unspecified before, so by default 1)

#### `tImport`

* sub class of `tNamedElement`
* `tNamedElement` defines a required attribute `name`