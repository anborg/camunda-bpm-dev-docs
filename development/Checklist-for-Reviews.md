# Always

* Is [Backwards Compatibility](https://github.com/camunda/camunda-bpm-platform/wiki/Backwards-Compatibility) maintained?
* Are relevant Test Cases provided?
* Does the code keep internal concepts and classes out of the public API?
* Do the design decisions make sense?
* Does the Commit introduce Resource Leaks?
* Is the code Thread-Safe?
* Do the changes adhere to Coding Style?
* Is the code expressive and contains enough comments?
* Does the code contain outcommented snippets?
* Does the code have remaining TODO's?
* Is the code ported to all branches listed in the *Fix Version* field of the JIRA ticket?
* Are all versions in the field *Fix Version* specified, so they contain no wildcard characters (like 7.5.**x**) in their name?

# If the code introduces new API

* Is an authorization check in place?
* Is a tenant check in place?
* Should the API write a [user operation log entry](https://docs.camunda.org/manual/latest/user-guide/process-engine/history/#glossary-of-operations-logged-in-the-user-operation-log)? This is not required for every API. If in doubt, ask another dev.
* Is there documentation (depending on the feature: Javadoc, User Guide, REST API)? Is it comprehensible to an average (forum/support) user?
* Is the API easy to use?
* Is the API consistent with other, previously existing API methods?