# HowTo
These site collects information's about testing, setting things up and debugging.

# HowTo Run DB2 with IBM JDK

```bash
# start db2 docker image 
docker run -d --privileged -p 50000:50000 registry.camunda.com/camunda-ci-db2:11.1
# connect to db2 image with ssh
ssh camunda@172.17.0.2

# change to db2inst1 user to run db2 commands
su -l db2inst1

# setup ibm jdk 
curl -L --insecure --user {USER} http://ci1.camunda.loc/ci/binaries/ibm/jdk/ibm-java-x86_64-sdk-6.0-16.3.tar.gz > jdk.tar.gz
tar -xvf jdk.tar.gz

export JAVA_HOME=`pwd`/ibm-java-x86_64-sdk-6.0-16.3
export PATH=$JAVA_HOME/bin:$PATH
# check if jdk is setup correctly
java -version
```

# HowTo monitor SQL Queries on DB2
As first see # HowTo Run DB2 with IBM JDK.

```bash
# get platform
git clone https://github.com/camunda/camunda-bpm-platform

# connect to engine db
db2 connect to engine

# enable activity collection DO THIS BEFORE THE MONITOR CREATION
db2 "alter service class sysdefaultsubclass under sysdefaultuserclass collect activity data on all database partitions with details and values"

# DO THE FOLLOWING IF YOU WANT TO MONITOR QUERIES AND WRITE THEM INTO A TABLE
# is not useful for testing with engine since we try to purge all tables -> this will fail

# create event monitor and writes to table
db2 create event monitor act_stmt for activities write to table manualstart
# check created tables
db2 list tables for all |grep DB2INST1
# drop the event monitor
db2 drop event monitor act_stmt
# query traced activities
select (select substr(stmt_text,1,70) as stmt_text from db2inst1.ACTIVITYSTMT_ACT_STMT as as1 where as1.EXECUTABLE_ID=as.EXECUTABLE_ID fetch first 1 row only), count(*) as NUM_EXECS, sum(STMT_EXEC_TIME) as SUM_STMT_EXEC_TIME, sum(TOTAL_CPU_TIME) as SUM_TOTAL_CPU_TIME, sum(LOCK_WAIT_TIME) as SUM_LOCK_WAIT_TIME, sum(ROWS_READ) as SUM_ROWS_READ, sum(ROWS_MODIFIED) as SUM_ROWS_MODIFIED from db2inst1.ACTIVITYSTMT_ACT_STMT as as left outer join db2inst1.ACTIVITYMETRICS_ACT_STMT av on as.appl_id=av.appl_id and as.uow_id=av.uow_id and as.activity_id=av.activity_id group by EXECUTABLE_ID 


# EVENT MONITOR WITH FILES
# DO THIS FOR TESTING WITH ENGINE

# create event monitor which writes into files
db2 "create event monitor act_stmt_file for activities write to file '/tmp/out' manualstart"

# create out dir
mkdir /tmp/out

# activate event monitor
db2 "set event monitor act_stmt_file state = 1"

# run tests
/opt/maven/bin/mvn -f camunda-bpm-platform/engine/pom.xml clean verify -Pdatabase,db2,db2-111 -s camunda-bpm-platform/settings/maven/nexus-settings.xml
# cancel after some tests are executed
# format the output
mkdir /tmp/source
db2evmon -path /tmp/out/ > /tmp/source/00000000.txt

# You can use vi or something else to investigate the trace output
#[...]

# deactivate event monitor
db2 "set event monitor act_stmt_file state = 0"

# to disable the collection use:
db2 "alter service class sysdefaultsubclass under sysdefaultuserclass collect activity data none"
```

# HowTo Setup and debug Websphere

```bash
# Setup database for example oracle:
docker run -d -p 1521:1521 --name oracle registry.camunda.com/camunda-ci-oracle

# Start Websphere docker image and link resources and database:


  docker run --privileged -it -p 9060:9060 -p 9080:9080 -p 7777:7777 \
  -v ~/camunda-bpm-platform/:/camunda-bpm-platform \
  -v ~/camunda-bpm-platform-ee:/camunda-bpm-platform-ee \
  -v ~/.m2:/root/.m2 \
  --link oracle:oracle registry.camunda.com/camunda-ci-websphere /bin/bash -l


# Install maven if not exist
  yum install maven

# Set java 8 as JAVA\_HOME
  export JAVA_HOME=/usr/lib/jvm/java-1.8.0


# To check if maven runs with the correct java version run:
mvn -v


# Execute integration test in `camunda-bpm-platform-ee\/qa\/` folder 
  mvn clean verify -Pwas90,oracle,oracle-12,oracle-xa,engine-integration,recreate-db \
  -Dwas.home=/home/camunda/IBM/WebSphere/AppServer/ \
  -Dwas.profile=AppSrv01 -Ddatabase.port=${ORACLE_PORT_1521_TCP_PORT} \
  -Ddatabase.host=${ORACLE_PORT_1521_TCP_ADDR}



# The profiles recreate-db and oracle-xa must be used.
# If the database is linked into the websphere docker image the database port
# and database host has to be set. Use the global environment variables( type env) to see which values are set.

# The was home have also been set if the root user is used.
# To install the web apps and engine rest run in the qa folder

  mvn clean verify -Pwas90,oracle,oracle-12,oracle-xa,webapps-integration,recreate-db \
  -Dwas.home=/home/camunda/IBM/WebSphere/AppServer/ \
  -Dwas.profile=AppSrv01 -Ddatabase.port=${ORACLE_PORT_1521_TCP_PORT} \
  -Ddatabase.host=${ORACLE_PORT_1521_TCP_ADDR}


# The port 7777 can be used to debug the WAS 9. Edit the configuration in Intellij and add a new remote point # name it WAS 9 for example and 
# change the ports to 7777. Now you are able to debug the WAS 9 (for example the engine rest project).
```


# HowTo run EE Plugins

```bash
# Start the chrome docker image and open a bash inside

	docker run -it \ 
	-v ~/camunda-bpm-platform-ee/:/camunda-bpm-platform-ee \
	-v ~/.m2/settings.xml:/root/.m2/settings.xml \
	registry.camunda.com/camunda-ci-chrome /bin/bash


# Install npm with the help of nvm.
# Find the latest nvm version on [https://github.com/creationix/nvm#manual-install](Github) and how to 
# manual install nvm.

	export NVM_DIR="$HOME/.nvm" && (
	  git clone https://github.com/creationix/nvm.git "$NVM_DIR"
	  cd "$NVM_DIR"
	  git checkout `git describe --abbrev=0 --tags --match "v[0-9]*" origin`
	) && . "$NVM_DIR/nvm.sh"


# Export correct system variables for nvm.
	export NVM_DIR="$HOME/.nvm"
	[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh" # This loads nvm


# Install an old version of npm, since the webapps only runs on the old npm version.
  nvm install 0.10.38


# Set up the npm enviroment to test the plugins.
	npm install -g npm@2.10.1
	npm install -g grunt-cli bower

# Now it is possible to run the webapp plugins. For example you can start the test-e2e profile.
  /opt/maven/bin/mvn clean verify -P test-e2e -B
```

# HowTo setup WildFly with MariaDB

Create new module folder structure and add a module.xml
```
$ mkdir -p server/wildfly-10.1.0.Final/modules/org/mariadb/jdbc/mariadb-java-client/main/
$ vim server/wildfly-10.1.0.Final/modules/org/mariadb/jdbc/mariadb-java-client/main/module.xml
```

The module xml should contain the following:
```xml
<module xmlns="urn:jboss:module:1.1" name="org.mariadb.jdbc.mariadb-java-client">
  <resources>
    <resource-root path="mariadb-java-client-@version.mariadb@.jar"/>
  </resources>

  <dependencies>
    <module name="javax.api"/>
    <module name="javax.transaction.api" />
  </dependencies>
</module>
```

Copy the jdbc driver into the folder in which the `module.xml` lies.
Maybe the jdbc driver is already in the `.m2` folder.
```
$ cp ~/.m2/repository/org/mariadb/jdbc/mariadb-java-client/1.1.8/mariadb-java-client-1.1.8.jar server/wildfly-10.1.0.Final/modules/org/mariadb/jdbc/mariadb-java-client/main/
```

Adjust the copied module xml. Add the current jdbc mariadb version.
```
$ vim server/wildfly-10.1.0.Final/modules/org/mariadb/jdbc/mariadb-java-client/main/module.xml
```


Adjust the `standalone.xml`, add the driver tag and adjust the data source.
The data source should contain the url, username and password which is needed.
```
$ vim server/wildfly-10.1.0.Final/standalone/configuration/standalone.xml 
```

```
   <datasources>
     <datasource jndi-name="java:jboss/datasources/ExampleDS" pool-name="ExampleDS" enabled="true" use-java-context="true">
       <connection-url>jdbc:h2:mem:test;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE</connection-url>
         <driver>h2</driver>
         <security>
           <user-name>sa</user-name>
           <password>sa</password>
         </security>
       </datasource>
       <datasource jndi-name="java:jboss/datasources/ProcessEngine" pool-name="ProcessEngine" enabled="true" use-java-context="true" jta="true" use-ccm="true">
         <connection-url>jdbc:mariadb://localhost:3306/process-engine</connection-url>
           <driver>org.mariadb</driver>
             <security>
               <user-name>camunda</user-name>
               <password>camunda</password>
            </security>
       </datasource>
     <drivers>
       <driver name="h2" module="com.h2database.h2">
         <xa-datasource-class>org.h2.jdbcx.JdbcDataSource</xa-datasource-class>
       </driver>
       <driver name="org.mariadb" module="org.mariadb.jdbc.mariadb-java-client">
         <xa-datasource-class>org.mariadb.jdbc.MySQLDataSource</xa-datasource-class>
       </driver>
     </drivers>
   </datasources>         
```
If you haven't already setup the mariadb. Pull the mariadb image with docker.
After that start the mariadb, also with docker.
```
docker pull registry.camunda.com/camunda-ci-mariadb
docker run -d -p 3306:3306 registry.camunda.com/camunda-ci-mariadb
```