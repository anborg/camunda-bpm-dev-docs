Here are the steps if you want to use the script to configure WebSphere/WebLogic server

1. Create a portainer [link], currently available:

* WebSphere 9.0 + db2 10.5 
* WebSphere 8.5 + db2 9.7 - Please note that it is not working for 7.10+
* WebLogic 12R2 + PostgreSQL 9.4
* WebLogic 12R2 + Oracle 12

2. Provide parameters

* Camunda version - a tag or a branch name, example - 7.9.6, 7.10.0-alpha5, 7.9 or master
* Nexus password (hq2-portainer pass from [conflluence](https://app.camunda.com/confluence/display/SRE/New+machine+accounts+for+CI))

3. Map ports if it is possible (some might be already taken).

4. Connect to the image via ssh or the console view and execute:

* cd ~ 
* copy the content of https://github.com/camunda/camunda-bpm-env-setup/blob/master/configEEServer.sh
* curl configEEServer.sh | bash

5. The script will prompt you for your git credentials

6. Wait to finish and then you can connect


TODO: camunda-ci-weblogic:12R2-oracle-12 is not working, quick fix is to adjust the oracle profile in /qa/pom.xml:
```
     <profile>
       <id>oracle</id>
       <properties>
         <weblogic.datasource.filename>datasource-camunda-jdbc.xml</weblogic.datasource.filename>
+        <my.database.url>${database.url}</my.database.url>
+        <my.database.host>${database.host}</my.database.host>
       </properties>
     </profile>
```
