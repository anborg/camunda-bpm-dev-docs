# Release Procedure

Steps marked with (*) are be performed by the "Release Manager" or a member of the Runtime Team.

PM = Product Manager (Daniel Meyer)

EM = Engineering Manager (Roman Smirnov)

TL = Tech Lead (Thorben Lindhauer)

QA = Michael Schöttes

MKT = Marketing (Charley Mann)

SUP = Support (Ilias Kourtoudis)

SRE = sre@camunda.com

The release procedure itself has 4 phases:

## [Phase 1: Setup Tasks](#phase-1)

These tasks can be done within the two weeks before the release:

- [ ] [Request the new license book from Ulrike](#request-the-new-license-book) (EM)
- [ ] [Prepare testing plan](#prepare-test-plan) (* + QA + SRE)
- [ ] [Adjust and migrate Getting Started guides](#adjust-and-migrate-getting-started-guides) (*)
- [ ] [Adjust and migrate Examples](#adjust-and-migrate-examples) (*)
- [ ] [Adjust and migrate unit testing template](#adjust-and-migrate-unit-testing-template) (*)
- [ ] [Write Update Guide](#write-update-guide) (*)
- [ ] [Stage a Blogpost](#stage-the-blog-post) (*)
- [ ] [Release all secondary Camunda projects](#release-all-secondary-camunda-projects) (*)
- [ ] [Update the Entity Relationship Diagrams](#update-the-entity-relationship-diagrams) (*)
- [ ] Schedule the releases of side projects: [Spring Boot Starter](https://github.com/camunda/camunda-bpm-spring-boot-starter/wiki/Minor-release), [Wildfly Swarm](https://github.com/camunda/camunda-bpm-wildfly-swarm), [JS External Task Client](https://github.com/camunda/camunda-external-task-client-js/wiki/Release-procedure), [Java External Task Client](https://github.com/camunda/camunda-external-task-client-java), [Camunda BPM Assert](https://github.com/camunda/camunda-bpm-assert)

## [Phase 2: Prepare the Release](#phase-2)

These steps can be done a couple of days before the release:

- [ ] [Check Preconditions](#check-preconditions) (*)
- [ ] [Trigger the release build](#trigger-the-release-build) (*)
- [ ] [Test Release](#test-release) (* + QA)
- [ ] [Test standalone webapps](#test-standalone-webapps) (*)
- [ ] [Stage new version of the docs](#stage-new-version-of-the-docs) (*)
- [ ] [Create docs stage and release jobs for new branch](#create-docs-stage-and-release-jobs-for-new-branch) (SRE)
- [ ] [Stage Enterprise Download Page](#stage-enterprise-download-page) (*, QA)
- [ ] [Stage Community Download Page](#stage-community-download-page) (*)

## [Phase 3: Publish the Release](#phase-3)

This is done on the release day:

- [ ] [Release Jira](#release-jira) (TL, EM)
- [ ] [Forward Security Reports](#forward-security-reports) (TL, EM)
- [ ] [Add new Version to Delivery Manager](#add-new-version-to-delivery-manager) (TL, QA, *)
- [ ] [Release Maven Central](#release-maven-central) (*)
- [ ] [Release Javadocs](#release-javadocs) (*)
- [ ] [Release the Staged Docs](#release-the-staged-docs) (*)
- [ ] [Push Redirects / URL rewrites adjustments](#push-redirects--url-rewrites-adjustments) (*)
- [ ] [Adjust Google Custom Search Engine](#adjust-google-custom-search-engine) (PM)
- [ ] [Release the Getting Started](#release-the-getting-started) (*)
- [ ] [Release the Enterprise Download Page](#release-the-enterprise-download-page) (*)
- [ ] [Release the Community Download Page](#release-the-community-download-page) (*)
- [ ] [Release the Blog Post](#release-the-blog-post) (*)
- [ ] [Publish the release on Twitter](#publish-the-release-on-twitter) (MKT + PM)
- [ ] [Send Mailings](#send-mailings) (SUP)
- [ ] [Make a Forum Announcement](#forum-announcement) (*)
- [ ] [Improve this guide](#improve-this-guide) (*)

## [Phase 4: After the Release](#phase-4)

- [ ] [Camunda BPM Platform code changes](#camunda-bpm-platform-code-changes) (*)
- [ ] [Camunda CI tasks](#ci-tasks) (SRE)

***

# Phase 1

## Request the new license book

Ask Ulrike to create the new license book. When received, put it to [`distro/license-book`](https://github.com/camunda/camunda-bpm-platform/tree/master/distro/license-book) folder.

## Prepare test plan

Set a meeting with QA to prepare a test plan for the manual testing of the release:
1. Pick which features to test by looking at the [Confluence Roadmap list](https://app.camunda.com/confluence/display/camBPM/Roadmap+Camunda+BPM)
2. Create a (Google/Airtable) spreadsheet to be used for keeping track of the testing process (you can use [this spreadsheet](https://docs.google.com/spreadsheets/d/1D9XF5Y84qeB-uFGOo8tnDbXHPJx-aNzsTpWprdfIpiI/edit?usp=sharing) as a template)
  * The spreadsheet should include all the features that need to be tested, feature documentation and implementer name
  * The spreadsheet should include all the possible variations for a testing environment setup (App Server + DB + JDK + OS + Browser)
3. Check if there are any new supported environments (ex. new Database Versions) and if they are available for testing through [Portainer](https://hq2.camunda.com/portainer/#/templates/).
  - If there is an environment missing, ask SRE to provide it.

## Adjust and migrate Getting Started guides
      
Follow the procedure here:
https://github.com/camunda/camunda-bpm-dev-docs/blob/master/releases/Getting-started-guides.md

Keep in mind that the platform version in the `pom.xml` files should be adjusted only for testing. Only commit changes that are needed for the Guides to be functional. The platform version will be permanently adjusted through a Jenkins job at a later phase of the Release process.

## Adjust and migrate Examples

Test and adjust the existing Camunda BPM Examples for the new minor release. Do the following steps for this.

* Clone the Git Repository https://github.com/camunda/camunda-bpm-examples
* Create a new branch for the minor release (ex. 7.12) to push all the changes to the branch and merge it to master when the new minor release exists. This allow to have the examples runnable until the release is finished.
* Adjust the `camunda-version` in the Maven `pom.xml` of the current example
* Follow the README of the current example to test it
* Adjust the links of the README to point to the new version of the docs (ex.: `https://docs.camunda.org/manual/7.12`)
* Push the changes to the master/minor release branch
* Do the steps before for all examples
* Squash all the commits and merge the release branch to master
* Add a new (7.X) tag to the overview [README](https://github.com/camunda/camunda-bpm-examples/blob/master/README.md)
* Create the 7.X tag for the minor release version on the squashed commit
* Push the changes to master

### Changes to the `Java` source files in the examples
Make sure the files contain a valid license header.

## Adjust and migrate unit testing template

Bump the Camunda version in https://github.com/camunda/camunda-engine-unittest

## Write Update Guide

Document the steps which are necessary to update from Camunda BPM 7.11.x to 7.12.0 for the different application server.

* Go into the cloned Git repository `camunda-docs-manual`
* Create a new folder `711-to-712` under `content/minor`
* Describe the different steps for the current application server in an own file.

## Stage the blog post

#### 1. Update the 'master' and create a branch 7.12.0 on the [blog repository](https://github.com/camunda/blog.camunda.org).

```
git checkout master
git pull origin master
git checkout -b 7.12.0
```

#### 2. Create a file 'camunda-bpm-7120-released.md' at /content/post/2019/11/ and push it to the repo.

```
git add /content/post/2019/11/camunda-bpm-7120-released.md
git commit
git push origin 7.12.0
```

#### 3. Ask TL to write the blog post.

## Release all secondary Camunda projects

1. Check the [bom](https://github.com/camunda/camunda-bpm-platform/blob/master/bom/pom.xml#L34-L43), if there are secondary projects with an alpha/snapshot version.
2. If that's the case we need to release them. Follow this [procedure](https://github.com/camunda/camunda-bpm-platform/wiki/Release-secondary-projects)

**Hint:**
When releasing a secondary project, other secondary projects might have cross-references to it and you should update them accordingly. E.g., for camunda-commons you need to update the `pom.xml` files of the following projects as well:
* dmn-engine
* spin
* connect
* engine

## Update the Entity Relationship Diagrams

1. Go to the [database upgrade scripts](https://github.com/camunda/camunda-bpm-platform/tree/master/engine/src/main/resources/org/camunda/bpm/engine/db/create) and check what changed in the database structure.
2. Create ERDs for the new version by copying the last version ERDs (ex. erd-bpmn_7.12.mwb from a copy of erd-bpmn_7.12.mwb). Then update the new ERDs accordingly.
  * The ERD files can be found in the camunda-docs-manual repo [here](https://github.com/camunda/camunda-docs-manual/tree/master/content/user-guide/process-engine/erd-project).
  * They can be edited through the [MySQL Workbench](https://www.mysql.com/products/workbench/) tool.
  * After they are edited, generate the appropriate `.SVG` images for each ERD and put them in the `img` directory [here](https://github.com/camunda/camunda-docs-manual/tree/master/content/user-guide/process-engine/img). Ex. for `erd-bpmn_7.12.mwb` there should be a `erd_712_bpmn.svg` image.
  * Finally, update the image links in the [database.md page|https://github.com/camunda/camunda-docs-manual/blob/master/content/user-guide/process-engine/database.md]
3. Check if everything looks good after you make the adjustments in the [documentation](https://stage.docs.camunda.org/manual/latest/user-guide/process-engine/database/#entity-relationship-diagrams).

-----

# Phase 2

## Check Preconditions

- There are no Snapshot dependencies to secondary projects like Spin Connect (check [bom](https://github.com/camunda/camunda-bpm-platform/blob/master/bom/pom.xml) for snapshot dependencies)
- There are no code problems ([check ci](https://hq2.camunda.com/broken) section "master")
- [Release Test Job](https://release.cambpm.camunda.cloud/view/Release-Test/) passed successfully recently
- There are no code tickets in review or test
- Send an email to camundabpm@camunda.com:
```
Hey,

please stop pushing to master since we are going to build the 7.x release.
```
*Keep in mind that staged artifacts are kept up to 6 days, arrange the build no more than 6 days before the official release.

## Trigger the Release Build

Open the following URL in Jenkins:
https://release.cambpm.camunda.cloud/view/Release-Master/

Set correct versions, select Release Type = "Final" and click on the "Run" icon.

Wait for the following jobs to turn green before continuing with the next step:
* 7.X-RELEASE-build-camunda-bpm-tags
* 7.X-RELEASE-build-EE-nightly-tags

## Test Release

Let the team and QA test the release. Share the test plan spreadsheet with the team. If everything is satisfying with the release, send an email to camundabpm@camunda.com:

```
Hey,

the release test passed, you can commit to master again :)

```

## Test standalone webapps

You have to download the CE standalone webapps
([`camunda-webapp-SERVER-standalone-VERSION.war`](https://camunda.org/release/camunda-bpm/))
and the EE standalone webapps
([`camunda-webapp-ee-SERVER-standalone-VERSION-ee.war`](https://camunda.org/enterprise-release/camunda-bpm/)).

**Note:** There is no separate standalone webapp for wildfly, use the jboss one
for testing.

To test the standalone webapp you need a vanilla version of the application
servers (without camunda installed). You can download the vanilla distros
from our nexus:

- [Tomcat](https://app.camunda.com/nexus/content/groups/public/org/apache/tomcat/tomcat/)
- [JBoss](https://app.camunda.com/nexus/content/groups/public/org/jboss/as/jboss-as-dist/)
- [Widlfly](https://app.camunda.com/nexus/content/groups/public/org/wildfly/wildfly-dist/)

The versions to use can be found in the current [parent
pom.xml](https://github.com/camunda/camunda-bpm-platform/blob/master/parent/pom.xml#L31-L37).

To test websphere and weblogic I would suggest to ask SRE to spin up a docker image in the HQ for testing. An
alternative would be the developer VM.

To deploy the standalone webapp follow the [installation
guide](http://stage.docs.camunda.org/manual/develop/installation/standalone-webapplication/#deploy). Normally
you have to copy the war file to the corresponding webapps folder of the
application server or use the web console for websphere and weblogic.

You can configure the process engine and database by editing the
`WEB-INF/applicationContext.xml` in the war file, if you want to test something
special.

After starting the standalone webapp test the webapps by starting some
processes, completing task, adding users and so one. Also focus on new
features introduced in the upcoming release.

**Note:** The job executor is disabled in the standalone webapps, which means
no jobs will be executed. So for example batches will not be executed either.

After finishing the test compare the process engine configuration in
`WEB-INF/applicationContext.xml` of the war file with the configuration
of the process engine shipped with the distribution. In general the engines
should be configure identical.

## Stage new version of the docs

### Create new Manual Branch

First a new branch needs to be created in the camunda-docs-manual repository

```sh
git checkout -b 7.12
```

### Adjust config on branch

On the new branch, adjust the configuration. Open the file `config.yaml` with an editor and edit it as follows:

```diff
--- a/config.yaml
+++ b/config.yaml
@@ -1,5 +1,5 @@
 ---
-baseurl: "/manual/develop/"
+baseurl: "/manual/7.12/"
 languageCode: "en-us"
 title: "Camunda BPM documentation"
 theme: "camunda"
@@ -20,10 +20,10 @@ params:
       url: "/enterprise"
   section:
     id: "manual"
-    version: "develop"
+    version: "7.12"
-    versionAlias: "7.12"
     versions:
       - "latest"
+      - "7.12"
       - "7.11"
       - "7.10"
       - "7.9"
       - "7.8"
       - "7.7"
       - "7.6"
       - "7.5"
       - "7.4"
       - "7.3"
       - "7.2"
       - "7.1"
@@ -31,5 +31,5 @@ params:
       - "develop"
   github:
     repositoryName: "camunda/camunda-docs-manual"
-    branch: "master"
+    branch: "7.12"
 ...
```

Test the changes by running

```sh
hugo server -w
```

and opening http://localhost:1313/manual/7.12/ in the browser.

Commit changes and push the branch:

```sh
git commit -a -m "chore(release): create 7.12 branch"
git push origin 7.12
```

## Create docs stage and release jobs for new branch

Ask SRE to perform the following:

Clone the camunda-ci/jenkins-job-dsl-seed-jobs repository and go into it.

Now you need to create new jobs for staging and releasing the new docs branch.

This is easily be done by adding the new maintenance version to the [Versions.groovy](https://github.com/camunda-ci/jenkins-job-dsl-seed-jobs/blob/master/src/main/groovy/version/Versions.groovy) file.

Commit and push the changes

```sh
git commit -a -m "chore(release): create 7.12 docs jobs"
git push origin master
```

After drinking 1 espresso, the new jobs should now appear on https://ci.cambpm.camunda.cloud/view/Docs/

Trigger `camunda-docs-manual-stage (7.12, 7.12)` and check the result at http://stage.docs.camunda.org/manual/7.12/

## Adjust docs with new verstion (*)

### Release new docs version as latest (*)

You also need to release the new version as "latest".

Checkout the latest branch of the camunda-docs-manual repository and set to new version

```sh
git checkout latest
git reset 7.12 --hard
```

Next, adjust the configuration for the latest version

```diff
--- a/config.yaml
+++ b/config.yaml
@@ -1,5 +1,5 @@
 ---
-baseurl: "/manual/7.12/"
+baseurl: "/manual/latest/"
 languageCode: "en-us"
 title: "Camunda BPM documentation"
 theme: "camunda"
@@ -20,7 +20,8 @@ params:
       url: "/enterprise"
   section:
     id: "manual"
-    version: "7.12"
+    version: "latest"
+    versionAlias: "7.12"
     versions:
       - "latest"
       - "7.12"
```

Commit and force-push

```sh
git commit -a -m "chore(release): release 7.12 as latest"
git push origin latest --force
```

### Add new version of the manual to all other versions (*)

Next we need to make sure the user can select the new version in the version select boxes on all other branches.
This procedure is different for hugo and docpad based branches.
For hugo based branches (master, 7.4+) this means:

```diff
--- a/config.yaml
+++ b/config.yaml
@@ -21,9 +21,10 @@ params:
   section:
     id: "manual"
     version: "7.4"
     versions:
       - "latest"
+      - "7.12"
       - "7.11"
```

```sh
git commit -a -m "chore(release): add 7.12"
git push origin 7.4
```

On master, we also bump the version alias to the next minor version:

```diff
--- a/config.yaml
+++ b/config.yaml
@@ -21,9 +21,10 @@ params:
   section:
     id: "manual"
     version: "develop"
-    versionAlias: "7.12"
+    versionAlias: "7.13"
     versions:
       - "latest"
+      - "7.13"
       - "7.12"
       - "7.11"
```

```sh
git commit -a -m "chore(release): add 7.12, bump to 7.13 "
git push origin master
```

For docpad based branches (7.0 - 7.3), this means:

```diff
--- a/site/docpad.conf.js
+++ b/site/docpad.conf.js
@@ -40,6 +40,7 @@ module.exports = {
         current: process.env.DOCS_VERSION || '7.3',
         all: [
           { id: 'latest', name: 'Latest' },
+          { id: '7.12', name: '7.12 (stable)' },
           { id: '7.11', name: '7.11 (stable)' },
           { id: '7.10', name: '7.10 (stable)' },
```

```sh
git commit -a -m "chore(release): add 7.12"
git push origin <branch-name>
```

### Adjust Redirects / URL rewrites

Next we need to adjust the URL rewrites. Please note the examples are given with 7.11 release version and 7.12 next development. First for stage. Go into the repository `camunda-docs-static`:

```diff
--- a/config/stage/.htaccess
+++ b/config/stage/.htaccess
@@ -6,7 +6,7 @@ RewriteRule ^$ /manual/
 RewriteRule ^latest/api-references/java/ /manual/7.3/reference/javadoc/

 # Javadoc
-RewriteRule ^manual/develop/reference/javadoc/$ /javadoc/camunda-bpm-platform/7.11-SNAPSHOT [R=307,L]
+RewriteRule ^manual/develop/reference/javadoc/$ /javadoc/camunda-bpm-platform/7.12-SNAPSHOT [R=307,L]
 RewriteRule ^manual/latest/reference/javadoc/$ /javadoc/camunda-bpm-platform/7.11 [R=307,L]
 RewriteRule ^manual/([^/]+)/reference/javadoc/$ /javadoc/camunda-bpm-platform/$1 [R=307,L]
```

Commit & push to master

```sh
git commit -a -m "chore(release): set next javadocs redirect"
git push origin master
```

This is all that needs to be done for stage. For live we need to set the new version as the default "stable version". However, we should not commit and push this directly to master since it will become visible right away. Instead we prepare a pull request:

```sh
git checkout -b 7.12
```

Perform the following edits:

```diff
--- a/config/live/.htaccess
+++ b/config/live/.htaccess
@@ -8,31 +8,31 @@ RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R=301,L]
 RewriteRule ^$ /manual/
 
 
 # Javadoc
-RewriteRule ^manual/develop/reference/javadoc/$ /javadoc/camunda-bpm-platform/7.11 [R=307,L] # live doesn't have -SNAPSHOT
+RewriteRule ^manual/develop/reference/javadoc/$ /javadoc/camunda-bpm-platform/7.12 [R=307,L] # live doesn't have -SNAPSHOT
 RewriteRule ^manual/latest/reference/javadoc/$ /javadoc/camunda-bpm-platform/7.11 [R=307,L]
 RewriteRule ^manual/([^/]+)/reference/javadoc/$ /javadoc/camunda-bpm-platform/$1 [R=307,L]
 
 # manual without version
-RewriteRule ^manual/?$ /manual/7.11/ [R=307,L]
+RewriteRule ^manual/?$ /manual/7.12/ [R=307,L]
 
-RewriteCond %{REQUEST_URI} ^/manual((?!7.10/)[^/]+)
+RewriteCond %{REQUEST_URI} ^/manual((?!7.11/)[^/]+)
 RewriteCond %{DOCUMENT_ROOT}/manual/%1 !-d
-RewriteRule ^manual/(.*) /manual/7.11/ [R=307,L]
+RewriteRule ^manual/(.*) /manual/7.12/ [R=307,L]
 
- # Redirect /manual/X to /manual/7.11/X if folder X does not exist but 7.10/X does
-RewriteCond %{REQUEST_URI} ^/manual/((?!7.10/)[^/]+)
+ # Redirect /manual/X to /manual/7.12/X if folder X does not exist but 7.11/X does
+RewriteCond %{REQUEST_URI} ^/manual/((?!7.12/)[^/]+)
 RewriteCond %{DOCUMENT_ROOT}/manual/%1 !-d
-RewriteCond %{DOCUMENT_ROOT}/manual/7.11/%1 -d
-RewriteRule ^manual/(.*) /manual/7.11/$1 [R=307,L]
+RewriteCond %{DOCUMENT_ROOT}/manual/7.12/%1 -d
+RewriteRule ^manual/(.*) /manual/7.12/$1 [R=307,L]
 
-# Redirect /manual/X/Y to /manual/7.11/Y if folder X does not exist but 7.10/Y does
-RewriteCond %{REQUEST_URI} ^/manual/((?!7.11/?)[^/]+)(/[^/]+)?
+# Redirect /manual/X/Y to /manual/7.12/Y if folder X does not exist but 7.11/Y does
+RewriteCond %{REQUEST_URI} ^/manual/((?!7.12/?)[^/]+)(/[^/]+)?
 RewriteCond %{DOCUMENT_ROOT}/manual/%1 !-d
-RewriteCond %{DOCUMENT_ROOT}/manual/7.11%2 -d
-RewriteRule ^manual/[^/]+(/[^/]+(?:/.*))? /manual/7.11$1 [R=307,L]
+RewriteCond %{DOCUMENT_ROOT}/manual/7.12%2 -d
+RewriteRule ^manual/[^/]+(/[^/]+(?:/.*))? /manual/7.12$1 [R=307,L]
```

commit and push to branch:

```sh
git commit -am "chore(release): set 7.12 as default version"
git push origin 7.12
```

Merge this branch into master on the release day.

Hint: Essentially, you need to replace all the occurrences of the string `7.11` with `7.12`.

#### Update the documentation smoke tests

We also need to [adjust the smoke tests](https://github.com/camunda/camunda-docs-static/blob/master/test/HttpRedirectionTest.yml) for the URL rewrites that we adjusted before. Please note the examples are given with 7.11 release version and 7.12 next development.

```diff
--- a/test/HttpRedirectionTest.yml
+++ b/test/HttpRedirectionTest.yml
https://docs.camunda.org:
  /:
-    target: /manual/7.11/
+    target: /manual/7.12/
    status: 307
  /manual/:
-    target: /manual/7.11/
+    target: /manual/7.12/
    status: 307
  /manual/reference/bpmn20:
-    target: /manual/7.11/
+    target: /manual/7.12/
    status: 307
```

Commit the changes to the release branch and merge this branch into master on the release day:

```sh
git commit -am "chore(release): update smoke tests to version 7.12"
git push origin 7.12
```

Hint: As mentioned before, you need to replace all the occurrences of the string `7.11` with `7.12`.

### Stage Enterprise Download Page

The new minor version needs to be added to the enterprise download page.
Perform the following edits:

```diff
--- a/enterprise/content/download.md
+++ b/enterprise/content/download.md
@@ -35,13 +35,17 @@ downloads:
     - war

   selected:
-    branch: "7.11"
-    version: "7.11.0"
+    branch: "7.12"
+    version: "7.12.0"
     server: "tomcat"

   branches:
   - branch: "7.12"
     releases:
+    - number: "7.12.0"
+      note: "http://blog.camunda.org/post/2019/11/camunda-bpm-7120-released/"
+      date: "2019.11.29"
+
-    - number: "7.12.0-alpha5"
-      note: "http://blog.camunda.org/post/2019/10/ReleaseCandidateAvailable/"
-      date: "2019.10.31"
```

Note: You will need to remove all the alpha versions of the version you are releasing.

Commit and push to master:

```sh
git commit -a -m "chore(release): add 7.12.0 to download page"
git push origin master
```

### Stage Community Download Page

Repository: [camunda.com-new](https://github.com/camunda/camunda.com-new)
You need to adjust [releases.json](https://github.com/camunda/camunda.com-new/blob/master/data/releases.json) and check your change on [stage](https://app.camunda.com/jenkins/view/All/job/stage.camunda.com-new%20(master)/).

Note: "showAlpha" must be set to false when releasing minor 

-----

# Phase 3

## Release Jira

Talk to EM or TL.

## Forward Security Reports

Determine all security reports for which fixes have are released and forward them to 1) TL 2) EM. They will then take care of publishing security notices. Find all such issues with the following JIRA query:

```
project = CAM AND fixVersion = <released version> AND type = "Security Report"
```

## Add new Version to Delivery Manager

Select the **Versions** tab on the [Delivery Manager](https://delivery-manager.camunda.com/delivery-manager/) dashboard. Add the latest version to the list of versions.

## Release Maven Central

We have several CI jobs which upload artifacts to Maven Central into their staging repository section. In order to make them publicly available, we need to manually close the staging repositories and release the artifacts.

1. Go to [Maven Central](https://oss.sonatype.org/) and login using the credentials found in our [Confluence](https://app.camunda.com/confluence/display/camBPM/Maven+Central+Release)
2. On the left side, click on the 'Staging Repositories' link.
3. After it has been loaded, scroll down to the bottom of the list. You should find the relevant Camunda staging repositories there.
4. Mark each repository you want to release.
    
    *For Example:*  

    >org.camunda.bpm:camunda-bom:7.12.0  
    >org.camunda.bpm.webapp:camunda-webapp:7.12.0  
    >org.camunda.bpm.model:camunda-bpmn-model:7.12.0  
    >org.camunda.bpm.model:camunda-dmn-model:7.12.0  
    >org.camunda.bpm.model:camunda-cmmn-model:7.12.0  
    >org.camunda.bpm.dmn:camunda-engine-dmn-bom:7.12.0  
    >org.camunda.bpm.model:camunda-xml-model:7.12.0

5. Click on 'Release' at the menu on top of the list. A window will pop up were you can enter a description but it is not necessary. Activate 'Drop repository after release automatically'. Then proceed.
6. Done.

## Release Javadocs

1. The javadocs are staged automatically by the release build. Checkout if the javadocs are available at http://stage.docs.camunda.org/javadoc/camunda-bpm-platform/7.12 (you need to adapt the version in the link to the release version).
2. Verify that the correct javadocs are present.
3. Go to [Jenkins Camunda Docs Jobs](https://ci.cambpm.camunda.cloud/view/Docs/). After that, release the javadocs by triggering the the job `camunda-docs-release (javadoc-camunda-bpm-platform-7.12)` and `camunda-docs-release (javadoc-camunda-bpm-platform-7.13-SNAPSHOT)` (adapt versions).


## Release the Staged Docs

#### 1. Go to [Jenkins Camunda Docs Jobs](https://ci.cambpm.camunda.cloud/view/Docs/)
#### 2. Start job `camunda-docs-release (manual-7.12)`
#### 3. Start all other `camunda-docs-release (manual-7.X)` jobs
#### 4. As part of an minor release, the current `master` content needs to be released as `latest` at the [docs repository](https://github.com/camunda/camunda-docs-manual).
##### 4.1 Tag the docs
```
git checkout master
git pull
git tag -a 7.12.0 # the comment should be '7.12.0'
git push origin 7.12.0
```

##### 4.2 Release the tag as latest

Set the branch `latest` to the tag

```
git checkout latest
git reset --hard 7.12.0
```

Edit the config file on the branch

```diff
--- a/config.yaml
+++ b/config.yaml
@@ -1,5 +1,5 @@
 ---
-baseurl: "/manual/develop/"
+baseurl: "/manual/latest/"
 languageCode: "en-us"
 title: "Camunda BPM documentation"
 theme: "camunda"
@@ -20,7 +20,7 @@ params:
       url: "/enterprise"
   section:
     id: "manual"
-    version: "develop"
+    version: "latest"
     versionAlias: "7.12"
     versions:
       - "latest"
```

##### 4.3 Run `hugo` and check result

##### 4.4 Commit changes
```
git commit -a -m "chore(release): release master as latest"
```

##### 4.5 Force push to latest
```
git push -f origin latest
```

##### 4.6 Release Stage

* The content will be published to http://stage.docs.camunda.org/manual/latest/

* Make sure, that [this](https://ci.cambpm.camunda.cloud/view/Docs/job/docs/job/camunda-docs-manual-stage%20(latest,%20latest)/) build has already been triggered and successfully passed through.  

* Once the release is staged there, release it by triggering the following build:
[camunda docs release](https://ci.cambpm.camunda.cloud/view/Docs/job/docs/job/camunda-docs-release%20(manual-latest)/)   

* Now the new content is available in http://docs.camunda.org/manual/latest/

#### 5. Send an email to `camundabpm@camunda.com`

```
Hi all,

I have now released the current version of the docs as 7.12 and latest. Master has been bumped to 7.13.
If you are still documenting things that are relevant for 7.12, you need to

- commit to master
- cherry-pick to 7.12 branch
- cherry-pick to latest branch

Thanks
```

## Push Redirects / URL rewrites adjustments

1. Go the [camunda-docs-static](https://github.com/camunda/camunda-docs-static) repository, check that the branch `7.12` is available and [these steps](#adjust-redirects--url-rewrites) were performed.
2. Merge the `7.12` branch into master:
```
git checkout master
git merge 7.12
git push origin master
```

## Adjust Google Custom Search Engine

Talk to PM (Daniel)
https://cse.google.com/cse/setup/basic?cx=007121298374582869478:yaec0vxmc7e

## Release the Getting Started

1. Trigger the [update-get-started-poms](https://release.cambpm.camunda.cloud/job/maintenance/job/update-get-started-poms/), do not forget to enter the correct versions.
2. Check the repos if everything is correct (including tags).
3. Go to [Jenkins Camunda Docs Jobs](https://ci.cambpm.camunda.cloud/view/Docs/)
4. Start job `camunda-docs-release (get-started)`

## Release the Enterprise Download Page

1. Go to [Jenkins Camunda Docs Jobs](https://ci.cambpm.camunda.cloud/view/Docs/)
2. Start job `camunda-docs-release (enterprise)`

## Release the Community Download Page

> This page also needs to be updated: https://docs.camunda.org/manual/7.11/installation/standalone-webapplication/#download

Sources are here: https://github.com/camunda/camunda.com-new/live

#### 1. Update ee and ce sections in [releases.json](https://github.com/camunda/camunda.com-new/blob/live/data/releases.json) 

#### 2. Push it on `live`
```
git commit -am ":beer:(download) 7.12.0 released"
git push origin live
```

#### 3. Check the jenkins build at https://app.camunda.com/jenkins/view/All/job/camunda.com-new%20(live)/

#### 4. Check the result at http://camunda.com/

## Release the Blog Post

1. Before you publish it ensure that the `camunda.org` [download page](https://camunda.org/download/) is up to date. Note: The title of the blogpost must be in the following format: Camunda 7.12.0 released.   
2. Publish the blog post by merging the branch with the master:

```
git checkout master
git merge 7.12.0
```

## Publish the release on Twitter

Let Marketing (Charley) know that a new release is available by sending her the following email (Please **CC Daniel, Roman**):

```
Hi Charley,

we have published a new minor release:

* The version is 7.12.0.
* The link to the blogpost is https://blog.camunda.org/post/2019/11/camunda-bpm-7120-released
* The most noteworthy thing about this release is "COOLEST_FEATURE_IN_712"
* We propose to include the following image (XX Link) in the tweet.

Best,
XX
```
(Also See: https://app.camunda.com/confluence/display/MAR/Internal+Social+Media+Posting+Process)

If you get an out of office reply, tweet yourself by logging into the Camunda BPM Twitter account.

## Send mailings

Talk to Support (Ilias) or Michael to send the mailings.

## Forum Announcement

Announce the release in https://forum.camunda.org by posting in the Announcements category linking to the blogpost (adapt elements in bold):

> Title: Camunda BPM **7.12.0** released   
> Category: Announcements   
> Text:   
> Hi all,
>   
> Camunda BPM **7.12.0** has been released today.   
> Read all about it on our blog: **https://blog.camunda.org/post/2019/11/camunda-bpm-7120-released/**.

## Improve this Guide

If you noticed any errors in the guides, any steps that were left out, please edit this document and keep it up to date.

Note: For the version numbers used here it is ok to leave older versions in.

-----

# Phase 4

* time: when the artifacts are built

## Camunda BPM Platform code changes

* time: when the [jobs](https://github.com/camunda/camunda-bpm-platform/wiki/Perfoming-a-Minor-Release#ci-tasks) below are ready

### Update "old version" in maven

Update the `camunda.version.old` property in:
* camunda-bpm-platform:
  * [database pom][database-pom]
  * [qa/create new engine (here: `version.previous`)](https://github.com/camunda/camunda-bpm-platform/blob/master/qa/test-db-rolling-update/create-new-engine/pom.xml)
  * [ qa/test-db-upgrade](https://github.com/camunda/camunda-bpm-platform/blob/master/qa/test-db-upgrade/pom.xml)
* [camunda-engine-dmn][engine-dmn-pom]
* [camunda-bpmn-model][bpmn-model-pom]
* [camunda-cmmn-model][cmmn-model-pom]
* [camunda-dmn-model][dmn-model-pom]
* [camunda-xml-model][xml-model-pom]

to the previous minor version.

If the minor release includes a new commons release, update the `version.old` properties in [camunda/camunda-commons][typed-values-pom].  
If the minor release includes a new spin release, update the `spin.version.old` properties in [camunda/camunda-spin][spin-pom].  
If the minor release includes a new connect release, update the `connect.version.old` properties in [camunda/camunda-connect][connect-pom].

Update the `camunda.version` property in:

* camunda-bpm-platform:
  * [qa/test-db-rolling-update/create-old-engine](https://github.com/camunda/camunda-bpm-platform/blob/master/qa/test-db-rolling-update/create-old-engine/pom.xml)
  * [qa/test-db-rolling-update/rolling-update-util](https://github.com/camunda/camunda-bpm-platform/blob/master/qa/test-db-rolling-update/rolling-update-util/pom.xml)
  * [qa/test-db-rolling-update/test-old-engine](https://github.com/camunda/camunda-bpm-platform/blob/master/qa/test-db-rolling-update/test-old-engine/pom.xml)
  * [ qa/test-old-engine/](https://github.com/camunda/camunda-bpm-platform/blob/master/qa/test-old-engine/pom.xml)


Update test fixture:
  * add ``qa/test-db-instance-migration/test-fixture-XX/pom.xml`` and replace ``XX`` with new version
  * add the new module to [qa/test-db-instance-migration/pom.xml](https://github.com/camunda/camunda-bpm-platform/blob/cb86cadd332cd97b486c9c8efe7420110000764c/qa/test-db-instance-migration/pom.xml):
```diff
     <module>test-fixture-712</module>
+    <module>test-fixture-713</module>
     <module>test-migration</module>
```

### Add instance migration project

When the new version is 7.x, a new instance migration project that performs upgrade logic from 7.(x-1) to 7.(x) has to be added. Perform the following steps:

* In the [instance-migration project][instance-migration] and its most recent `test-fixture` project, set the property `camunda.version.current` to `7.(x-1).0`
* Add a new module called `test-fixture-7(x)`
* Configure the new module such that it executes patch scripts and upgrade scripts from `7.(x-1).0` to `7.(x).0-SNAPSHOT` (take a look at other `test-fixture` projects)
* Add the module to the [instance migration pom][instance-migration-pom]

### Create upgrade scripts

In the [sql-scripts][sql-scripts] folder of the [camunda/camunda-bpm-platform][camunda-bpm-platform]:

1. Create an empty upgrade script for each supported database following the naming pattern ```${database}_engine_${previousVersion}_to_${currentVersion}```.
2. Follow this [SQL Development Guide](https://github.com/camunda/camunda-bpm-platform/wiki/SQL-and-DDL-Management#development-1) to set the initial content of the new scripts.

[camunda-bpm-platform]: https://github.com/camunda/camunda-bpm-platform/tree/master
[sql-scripts]: https://github.com/camunda/camunda-bpm-platform/tree/master/engine/src/main/resources/org/camunda/bpm/engine/db
[database-pom]: https://github.com/camunda/camunda-bpm-platform/blob/master/database/pom.xml
[typed-values-pom]: https://github.com/camunda/camunda-commons/blob/master/typed-values/pom.xml
[instance-migration]: https://github.com/camunda/camunda-bpm-platform/tree/master/qa/test-db-instance-migration
[instance-migration-pom]: https://github.com/camunda/camunda-bpm-platform/tree/master/qa/test-db-instance-migration/pom.xml
[engine-dmn-pom]: https://github.com/camunda/camunda-engine-dmn/blob/master/pom.xml
[bpmn-model-pom]: https://github.com/camunda/camunda-bpmn-model/blob/master/pom.xml
[cmmn-model-pom]: https://github.com/camunda/camunda-cmmn-model/blob/master/pom.xml
[dmn-model-pom]: https://github.com/camunda/camunda-dmn-model/blob/master/pom.xml
[xml-model-pom]: https://github.com/camunda/camunda-xml-model/blob/master/pom.xml
[spin-pom]: https://github.com/camunda/camunda-spin/blob/master/pom.xml
[connect-pom]: https://github.com/camunda/camunda-connect/blob/master/pom.xml

### Example commit for above code changes

See this [commit](https://github.com/camunda/camunda-bpm-platform/commit/df0df1688e7582eb89ade6a1fc004505501822b8) for [camunda-bpm-platform][camunda-bpm-platform] and [this one](https://github.com/camunda/camunda-commons/commit/1e4b32952eb01fa1e6108a766cd978cc3ed9571b) for [camunda-commons](https://github.com/camunda/camunda-commons/tree/master).

## CI tasks

Create an issue for SRE to provide support for the new minor version. Following [steps](https://github.com/camunda-ci/jenkins-job-dsl-seed-jobs/blob/master/docs/NewCamBPMVersion.md) have to be completed.
  * A new (7.X) branch needs to be created on the maintenance repo through the https://release.cambpm.camunda.cloud/job/maintenance/job/RELEASE-create-camunda-bpm-maintenance-branch/ job
