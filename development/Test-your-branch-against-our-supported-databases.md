How to do it:

1. Go to the ['Test-Platform-Branch'](https://hq2.camunda.com/jenkins/ci/view/Test-Platform-Branch/) view on our HQ CI Jenkins

2. Start the ['7.7-platform-TEST-BRANCH-ON-ALL-DATABASES'](https://hq2.camunda.com/jenkins/ci/view/Test-Platform-Branch/job/7.7-platform-TEST-BRANCH-ON-ALL-DATABASES/) job at the top of the view. It asks you for the **branch or commit** to build. It will then trigger the **H2 build** first, which upon success will propagate to the actual databases like mysql etc. See [this dependency-graph](https://hq2.camunda.com/jenkins/ci/view/Test-Platform-Branch/job/7.7-platform-TEST-BRANCH-ON-ALL-DATABASES/depgraph-view/), when which database will be triggered.

Hints:

* You can also just test your platform branch against a specific database version/type by triggering the correct job from the view.
* A full run, covering all databases, takes around **120 minutes** when the CI is running under low load. Otherwise it might take a bit longer. No SLAs given! ;)
* Currently executed maven profiles/modules are:
  * engine
  * sql-scripts
  * test-db-upgrade
  * test-db-rolling-update
  * **No Engine-IT** tests!
* Broken builds won't be visible on the broken board, you either have to **check them manually** or (in case you do not have blocked the Jenkins mails) an email will be send just to the person who started the build in case of any failure / fixed.

 