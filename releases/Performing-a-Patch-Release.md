# Patch Release Procedure

Note: If you want to build a camunda-bpm-spring-boot-starter patch release, please refere to the release guide for [secondary projects](https://github.com/camunda/camunda-bpm-dev-docs/blob/master/releases/Release-secondary-projects.md#patch-release).  

The release procedure has 2 phases:

## Phase 1: Prepare the Release

- [ ] [Check Preconditions](#check-preconditions)
- [ ] [Close the Branch](#close-the-branch)
- [ ] [Trigger the release build](#trigger-the-release-build)
- [ ] [Update the Enterprise Download Page](#update-the-enterprise-download-page)
- [ ] [Test the Release Build](#test-the-release-build)
- [ ] [Unblock the Branch](#unblock-the-branch)
- [ ] [Release Javadocs](https://github.com/camunda/camunda-bpm-dev-docs/blob/master/releases/Performing-an-Alpha-Release.md#release-javadocs)

## Phase 2: Publish the Release

- [ ] [Release the Patch Version in JIRA](#release-the-patch-version-in-jira)
- [ ] [Forward Security Reports](#forward-security-reports)
- [ ] [Publish the Enterprise Download Page](#publish-the-enterprise-download-page)
- [ ] [Inform the Support Team](#inform-the-support-team)
- [ ] [Improve this guide](#improve-this-guide) (*)

## Check Preconditions

- There are no code problems ([check ci](https://ci.cambpm.camunda.cloud/view/all/job/7.7/view/Broken/) - replace the version in path with the appropriate one)
- There are no code tickets in review or test

## Close the Branch

```
Hi Team,

please stop pushing to the 7.7 branch since we are going to build a patch release.
```

## Trigger the Release Build

Use the following confluence page to select the appropriate link to the Jenkins CI job

https://app.camunda.com/confluence/pages/viewpage.action?pageId=16583012



Click on the "Run" icon. Make sure to set the configuration to something like:

```
BRANCH:              7.7
DEVELOPMENT_VERSION: 7.7.2-SNAPSHOT
RELEASE_VERSION:     7.7.1
PUSH_REMOTE:         true
SKIP_TESTS:          true
SKIP_DEPLOY:         false
OVERRIDE_TAG:        false
```

Click Build. 
Wait for the following jobs to turn green before continuing with the next step:
* 7.7-RELEASE-build-camunda-bpm-maintenance-tags

Track the progress of release builds here.
https://release.cambpm.camunda.cloud/view/Release-7.11/

## Update the Enterprise Download Page

Add the latest release to the following page [`camunda-docs-static/enterprise/content/download.md`](https://github.com/camunda/camunda-docs-static/blob/master/enterprise/content/download.md).

All releases are handled as HUGO page variables in the document header. Add the release in the following format:

```
  branches:
  - branch: "7.7.1"
    releases:
    - number: "7.7.1"
      note: "https://app.camunda.com/jira/secure/ReleaseNote.jspa?projectId=10230&version=14896"
      date: "2017.07.11"
```

Commit the changes and build the enterprise page:

```
git commit -m "chore(download): add 7.7.1 to download page"

git push origin master
```

Review your commit on http://stage.docs.camunda.org/enterprise/download/

## Check the Docker Images
Verify that the docker images CE and EE are built.

CE job successfully run - https://ci.cambpm.camunda.cloud/job/7.11/job/7.11-platform-docker-ce/
EE job successfully run - https://ci.cambpm.camunda.cloud/job/7.11/job/7.11-platform-docker-ee/

## Test the Release Build

For each application server one developer should perform a test. Download the release artifacts from http://stage.docs.camunda.org/enterprise/download/

If you want to test Spring Boot Starter follow the [spring-boot-start setup guide](https://github.com/camunda/camunda-bpm-dev-docs/blob/master/howtos/setup-camunda-spring-boot.md#howto-setup-camunda-springboot) to start the Camunda Platform with Spring Boot. 

### Standard Regression Test
1. Download the release artifact from the enterprise download page
2. Combine the platform with a database of choice.
3. Start the platform  
3.1 Check the server log that no exceptions occur  
4. Open Admin  
4.1 Check if the dashboard and main menu contain the sections 'Users', 'Groups', 'Tenants', 'Authorizations', 'System'  
4.2 Add a license key to the platform  
4.3 Add a new User  
4.4 Asign the new user to a group  
4.5 Provide access authorization for Cockpit to the new user  
5. Open Tasklist  
5.1 Start a new invoice process  
5.2 Walk through the invoice process step by step  
5.3 Create a new filter with a meaningful filter criteria  
5.4 Check the results of the filter  
5.5 Delete the filter  
6. Open Cockpit  
6.1 Use the dashboard search plugin to find all finished invoice instances (There should be at least one)  
6.2 Open the historic instance view and check if all information is provided correctly ('Audit Log', 'Variables', 'Called Process Instances', 'Called Case Instances', 'Executed Decision Instances', 'Incidents', 'User Tasks', 'Job Log', 'External Tasks Log')  
6.3 Migrate all instances from the invoice process version 1 to version 2  
6.4 Use the dashboard search plugin to find all instances started after a certain date  
6.5 Cancel these instances by using the batch operations  
6.6 Select a process instance and go to the process instance view  
6.7 Perform a process instance modification  
6.8 Add a variable to the process instance  

### Release Specific Test
According to the implemented bug fixes choose some of the fixes to test them manually. 
If you don't know wich fixes are worth the effort use the following rule of thumb: 
* Prefer UI fixes as the test coverage is usually lower compared to backend fixes. Use different browser for the test.
* Prefer fixes related to customer support issues as our customers expect that these fixes are working correctly.
* Prefer fixes that came from critical or blocking bug reports

## Unblock the Branch

If everything is satisfying with the release, send an email to camundabpm@camunda.com:

```
Hi Team,

the release test passed, you can commit the 7.7 branch again :)

```

## Release the Patch Version in JIRA

Select the released version in [JIRA](https://app.camunda.com/jira/browse/CAM?selectedTab=com.atlassian.jira.jira-projects-plugin:versions-panel&subset=-1)  click Release and enter the release date.

On this page you will find the link to the JIRA release notes.


## Forward Security Reports

Determine all security reports for which fixes have are released and forward them to 1) Thorben 2) Roman. They will then take care of publishing security notices. Find all such issues with the following JIRA query:

```
project = CAM AND fixVersion = <released version> AND type = "Security Report"
```


## Publish the Enterprise Download Page

Once the release test has been done, release the enterprise docs by triggering the following build:
https://ci.cambpm.camunda.cloud/view/Docs/job/docs/job/camunda-docs-release%20(enterprise)/

## Release the Documentation (Manual)
Once the release has been done, Check for update scripts for patches and trigger the following build with respective version 
https://ci.cambpm.camunda.cloud/view/Docs/job/docs/job/camunda-docs-release%20(manual-7.11)/

## Inform the Support Team
Add information about the each patch release accroding to this page
https://app.camunda.com/confluence/display/SUP/Support+Services

Mail to team-support@camunda.com
Subject: [RELEASE-NOTICE] Camunda BPM Patch Release XXX
```
Hi Support,

we have published the following patch release(s):

* X.X.X  (Version + Release Note)

Best,
XX
```

## Improve this Guide

If you noticed any errors in the guides, any steps that were left out, please edit this document and keep it up to date.

Note: For the version numbers used here it is ok to leave older versions in.
