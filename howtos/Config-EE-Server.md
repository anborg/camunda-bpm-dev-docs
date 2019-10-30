# Automatic setup

## Websphere and Weblogic on Portainer

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

# Manual Setup

## Websphere

### Create a Postgres Datasource on Websphere 9 in Portainer

#### Make JDBC Driver available

1. Copy the Postgres JDBC driver to the file system of the websphere container (if running on Portainer, this can be done via scp. Make sure port 22 is mapped out of the container). Let's call this file `/home/camunda/jdbc/postgres.jar`.

#### Create JDBC Provider

1. In Websphere Admin console, navigate to **Resources -> JDBC -> JDBC providers**.
1. Select a scope, e.g. `Node=localhostNode01` has worked for me.
1. Click `New...`
1. Step 1:
   * Database type: User-defined
   * Implementation class name: `org.postgresql.ds.PGConnectionPoolDataSource`. This must be an implementation of `javax.sql.ConnectionPoolDataSource`.
   * Name: Postgres
1. Step 2:
   * Classpath: `/home/camunda/jdbc/postgres.jar` (local path to jar file).
1. Step 3: Finish and Save.

#### Create a Data Source

1. In Websphere Admin console, navigate to **Resources -> JDBC -> Data Sources**.
1. Select the same scope as before.
1. Click `New...`
1. Step 1:
   * Data source name: process-engine (or any other name)
   * JNDI name: jdbc/ProcessEngine
1. Step 2:
   * Select previously created JDBC provider
1. Step 3: Leave the data store helper as is, i.e. as `com.ibm.websphere.rsadapter.GenericDataStoreHelper`
1. Step 4: Do nothing
1. Step 5: Finish and Save.
1. In the overview, click on the new data source
1. Click on **Custom properties** and set the following:
   * user: `<database user name>`
   * password: `<database user password>`
   * url: `<JDBC url>`, in my case `jdbc:postgresql://portainer.camunda.loc:30039/process-engine`
