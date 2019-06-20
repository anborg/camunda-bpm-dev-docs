Secondary projects
* camunda-connect
* camunda-commons
* camunda-spin

This process is valid for the sideprojects as well:
* camunda-bpm-spring-boot-starter
* camunda-bpm-wildfly-swarm
* camunda-external-task-client-java
* camunda-bpm-assert

The following example is for camunda-spin project.

## Minor/Alpha release

### Adjust platform version
When releasing minor version of side project, we should always include the latest minor version of Camunda BPM available at this moment. For this, check the camunda.version property in pom.xml of corresponded project.

### Run the build job
Example: last release 1.3.1, new release 1.4.0

1. Trigger the build of the Jenkins job for the [master](https://ci.cambpm.camunda.cloud/view/Sideprojects/job/camunda-github-org/job/camunda-spin/job/master/)

* RELEASE: true
* RELEASE_VERSION: 1.4.0
* DEVELOPMENT_VERSION: 1.5.0-SNAPSHOT
* PUSH_CHANGES: true (default)
* USE_LOCAL_CHECKOUT: false (default)
* SKIP_DEPLOY_TO_MAVEN_CENTRAL: false (default)
* SKIP_DEPLOY_TO_CAMUNDA_NEXUS: false (default)
* OLD_VERSION: 1.4.0 (Set to the version against which to run future backward compatibility checks. Leave empty to keep things as they are)

[sideprojects]

* CAMBPM_VERSION: 7.x.y (the Camunda platform version against which the project will be built)
* NEXT_CAMBPM_VERSION: 7.x.0-SNAPSHOT (the next development version of Camunda platform. That way the project will be tested against the latest changes from the platform after the release.)

2. [Applies only for secondary projects] Adjust camunda-bpm-platform [bom](https://github.com/camunda/camunda-bpm-platform/blob/master/bom/pom.xml) file with the new spin [1.4.0 version](https://github.com/camunda/camunda-bpm-platform/commit/cbf0f2e8d397b5df9a58d273d00f930705797787#diff-75a56b1fc9bfafc36e14133db750da8b)

### Test the build [for sideproject]
Run the examples if there are any.

### Release Maven Central

Note: Do this after the platform artifacts are released or together with them

We have several CI jobs which upload artifacts to Maven Central into their staging repository section. In order to make them publicly available, we need to manually close the staging repositories and release the artifacts.

* Go to [Maven Central](https://oss.sonatype.org/) and login using the credentials found in our [Confluence](https://app.camunda.com/confluence/display/camBPM/Accounts)
* On the left side, click on the 'Staging Repositories' link.
* After it has been loaded, scroll down to the bottom of the list. You should find the relevant Camunda staging repositories there.


*For Example:*  

>org.camunda.bpm.model:camunda-spin:1.4.0  

* Click on 'Release' at the menu on top of the list. A window will pop up were you can enter a description but it is not necessary. Activate 'Drop repository after release automatically'. Then proceed.
* Done.

Hint: It takes about two hours until the artifacts are searchable on Maven Central.

### Release JIRA

Follow the steps in the [Minor Release Guide](https://github.com/camunda/camunda-bpm-platform/wiki/Perfoming-a-Minor-Release#release-jira) or [Alpha Release Guide](https://github.com/camunda/camunda-bpm-platform/wiki/Performing-an-Alpha-Release#release-jira).

## Patch release 

Example: last release 1.4.0, new release 1.4.1

1. Create [1.4 branch](https://github.com/camunda/camunda-spin/tree/1.4) on spin project. This will automatically create a new Jenkins job - [1.4](https://ci.cambpm.camunda.cloud/view/Sideprojects/job/camunda-github-org/job/camunda-spin/job/1.4/)
For sideprojects, a version adjustment must be performed, example: https://github.com/camunda/camunda-bpm-spring-boot-starter/commit/46974bb6e14732b21b4471ae91a34fecd6ce7dd8
2. Trigger the build of the Jenkins job for the [1.4](https://ci.cambpm.camunda.cloud/view/Sideprojects/job/camunda-github-org/job/camunda-spin/job/1.4/)

* RELEASE: true
* RELEASE_VERSION: 1.4.1
* DEVELOPMENT_VERSION: 1.5.2-SNAPSHOT
* PUSH_CHANGES: true (default)
* USE_LOCAL_CHECKOUT: false (default)
* SKIP_DEPLOY_TO_MAVEN_CENTRAL: false (default)
* SKIP_DEPLOY_TO_CAMUNDA_NEXUS: false (default)
* OLD_VERSION: 1.4.1 (Set to the version against which to run future backward compatibility checks. Leave empty to keep things as they are)

[sideprojects]

* CAMBPM_VERSION: 7.x.y (the Camunda platform version against which the project will be built)
* NEXT_CAMBPM_VERSION: 7.x.0-SNAPSHOT (the next development version of Camunda platform. That way the project will be tested against the latest changes from the platform after the release.)

3.  Adjust camunda-bpm-platform [bom](https://github.com/camunda/camunda-bpm-platform/blob/master/bom/pom.xml) file with the new spin [1.4.1 version](https://github.com/camunda/camunda-bpm-platform/commit/282a79ec000216e22af07fb86442340ad7b891e2)
4. Release Maven Central - We have several CI jobs which upload artifacts to Maven Central into their staging repository section. In order to make them publicly available, we need to manually close the staging repositories and release the artifacts.
    * Go to [Maven Central](https://oss.sonatype.org/) and login using the credentials found in our [Confluence](https://app.camunda.com/confluence/display/camBPM/Accounts)
    * On the left side, click on the 'Staging Repositories' link.
    * After it has been loaded, scroll down to the bottom of the list. You should find the relevant Camunda staging repositories there.
    *For Example:*  

    >org.camunda.bpm.model:camunda-spin:1.4.1 

    * Click on 'Release' at the menu on top of the list. A window will pop up where you can enter a description but it is not necessary. Activate 'Drop repository after release automatically'. Then proceed.
    * Done.

Hint: It takes about two hours until the artifacts are searchable on Maven Central.


5. Release JIRA: Follow the steps in the [Patch Release Guide](https://github.com/camunda/camunda-bpm-platform/wiki/Perfoming-a-Patch-Release#release-the-patch-version-in-jira).
