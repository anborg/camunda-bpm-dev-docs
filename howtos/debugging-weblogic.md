# Debugging WebLogic Server (WLS) 12

All things WebLogic Server that are relevant to debug it properly.

## Remote Debugging

1. Setup a WebLogic Server (e. g. via Docker)
2. Make sure that port 4000 is exposed (or any other free port)
3. Start WebLogic Server in debug mode
   1. Edit `startWebLogic.sh` located under `~/oracle/domains/base_domain/`
   2. Add the following line to the script (please don't add the leading +):
      ```diff
      #!/bin/sh

      # WARNING: This file is created by the Configuration Wizard.
      # Any changes to this script may be lost when adding extensions to this configuration.

      DOMAIN_HOME="/home/camunda/oracle/domains/base_domain"

      +JAVA_OPTIONS="-Xdebug -Xnoagent -Xrunjdwp:transport=dt_socket,address=4000,server=y,suspend=n"

      ${DOMAIN_HOME}/bin/startWebLogic.sh $*
      ```
   3. In case another debugging port should be used, make sure to change the port `address=$PORT` in the `JAVA_OPTIONS` environment variable
   4. Start WebLogic Server by running the script `startWebLogic.sh` (make sure it hasn't been started already)
4. You can now connect to WebLogic Server with your IDE

## Enable Certain Loggers

TODO
