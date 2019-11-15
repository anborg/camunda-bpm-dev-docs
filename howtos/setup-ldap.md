# How to setup a LDAP test environment

**This guide is still under contructions!**  
The following information will help you to setup our internal test LDAP server. The LDIF file provides a basic user/group structure for LDAP. Feel free to add further setups for specific scenarios to this guide.

## Setup the LDAP server

The credentials to our internal Test-LDAP server can be found in [confluence](https://app.camunda.com/confluence/display/camBPM/Test+LDAP). Use a tool like (Apache Directory Studio)[https://directory.apache.org/studio/] for the administration of LDAP.

After connecting to the LDAP server you have to create users and groups. This can be described in LDIF files which are easy to import and export. 

LDIF file:
```
dn: ou=Users,o=camunda,c=com
objectClass: organizationalUnit
objectClass: top
ou: Users

dn: ou=Groups,o=camunda,c=com
objectClass: organizationalUnit
objectClass: top
ou: Groups

dn: uid=admin,ou=Users,o=camunda,c=com
objectClass: inetOrgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: top
cn: Admin	
sn: Admin
description: Admin
employeeNumber: 1
givenName: Admin
telephoneNumber: 12345
mail: admin.admin@camunda.com
uid: admin
userPassword: admin

dn: uid=demo,ou=Users,o=camunda,c=com
objectClass: inetOrgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: top
cn: Demo
sn: Demo
description: Demo User
employeeNumber: 2
givenName: Demo
telephoneNumber: 4711
mail: demo.demo@camunda.com
uid: demo
userPassword: demo

dn: cn=management,ou=Groups,o=camunda,c=com
objectClass: groupOfNames
cn: management
member: uid=demo,ou=Users,o=camunda,c=com
```

## Prepare the engines LDAP plugin

The following configuration is located in the engines standalone.xml file. The parameter are configured according to the LDIF file above.

```
<!-- LDAP CONFIGURATION -->
<!-- Uncomment this section in order to enable LDAP support for this process engine -->
<!-- Adjust configuration, see ( http://docs.camunda.org/latest/guides/user-guide/#process-engine-identity-service-the-ldap-identity-service ) -->
    <plugin>
        <class>org.camunda.bpm.identity.impl.ldap.plugin.LdapIdentityProviderPlugin</class>

        <properties>
            <property name="serverUrl">Test-LDAP-Server-Hostname:Port/</property>
            <property name="acceptUntrustedCertificates">false</property>
            <property name="managerDn">uid=admin,ou=Users,o=camunda,c=com</property>
            <property name="managerPassword">admin</property>

            <property name="baseDn">o=camunda,c=com</property>

            <property name="userSearchBase">ou=Users</property>
            <property name="userSearchFilter">(objectclass=person)</property>

            <property name="userIdAttribute">uid</property>
            <property name="userFirstnameAttribute">cn</property>
            <property name="userLastnameAttribute">sn</property>
            <property name="userEmailAttribute">mail</property>
            <property name="userPasswordAttribute">userpassword</property>

            <property name="groupSearchBase">ou=Groups</property>
            <property name="groupSearchFilter">(objectclass=groupOfNames)</property>
            <property name="groupIdAttribute">cn</property>
            <property name="groupNameAttribute">cn</property>

            <property name="groupMemberAttribute">member</property>
            <property name="sortControlSupported">false</property>
        </properties>
    </plugin>


<!-- LDAP CONFIGURATION -->
<!-- The following plugin allows you to grant administrator authorizations to an existing LDAP user -->

    <plugin>
        <class>org.camunda.bpm.engine.impl.plugin.AdministratorAuthorizationPlugin</class>
        <properties>
            <property name="administratorUserName">admin</property>
        </properties>
      </plugin>
```                             

