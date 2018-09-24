This page will help you set up a dev environment for working on Unified Reporting.

# Initial Setup

## Eclipse Plug-ins

In Eclipse, go to Help > Eclipse Marketplace... and search for "Liberty". Install "IBM 
Liberty Developer Tools for Oxygen". Restart Eclipse.

## Software

In Installation Manager, add either the RTCPO (IBM) or HQSO (HCL) offering to the preferences.
The offerings can be found under [this location](http://it-build1.nonprod.hclpnp.com/it-main/master/).

During installation, pay attention to:
1. the installation directory. Do not install under Program Files, you will have troubling 
   using it as a target development because it requires root elevation to work in this 
   directory. Rather choose something like c:/IBM or c:/HCL (or whatever directory that 
   does not require root elevation to be able to write to it).
2. the start up options. It is recommended to not install the service or at least disable its automatic startup.

## Eclipse Projects

From the Git view, import the projects
~~~
comm/com.hcl.test.serialization

analytics/com.hcl.test.reports.analytics
analytics/com.hcl.test.reports.common
analytics/com.hcl.test.reports.registry
analytics/com.hcl.test.reports.registry.webapp
analytics/com.hcl.test.reports.static
analytics/com.hcl.test.reports.webapp
analytics/com.hcl.test.reports.webapp.libs

core/com.ibm.rational.test.lt.execution.stats
core/com.ibm.rational.test.lt.execution.stats.core
core/com.ibm.rational.test.lt.execution.stats.file

server/com.ibm.rational.test.lt.client.analytics
server/com.ibm.rational.test.lt.server.analytics
server/org.apache.wink.common
server/org.apache.wink.server
server/org.slf4j.rpt
~~~

Optionally, if you want to be able to generate/update the swagger/OpenAPI documentation,
also import these projects:
~~~
analytics/com.hcl.test.reports.registry.doc
analytics/com.hcl.test.reports.registry.doc2
analytics/com.hcl.test.reports.serialization.swagger
analytics/com.hcl.test.reports.serialization.swagger2
analytics/io.swagger
analytics/io.swagger2
~~~

## Eclipse Setup

Add the RTCP or HQS server to Eclipse configured servers.
1. Go to the Servers view, and click New > Server...
2. Select IBM > Liberty Server
3. Click Add... next to the Runtime field.
4. Very important: name it as `WebSphere Application Server Liberty` (mind the capital 
   S in `WebSphere`)
5. Choose "Existing Installation", and select the directory where you installed 
   RTCP or HQS.
6. Click Next, and in Add or Remove, select `com.hcl.test.reports.webapp`.

Then open the file `/WebSphere Application Server Liberty/servers/defaultServer/server.xml`
and add the following snippet under the node `webApplication`:
~~~
      <classloader delegation="parentLast"/>
~~~
... so it will look like this:
~~~
    <webApplication contextRoot="results"
      id="com.hcl.test.reports.webapp"
      location="com.hcl.test.reports.webapp.war"
      name="com.hcl.test.reports.webapp">
      <classloader delegation="parentLast"/>
    </webApplication>
~~~

# Sub-projects

## Unified Reporting Client

See [readme.md](/testingproducts/PT-Mainline/blob/develop/analytics/com.hcl.test.reports.registry.webapp/README.md) and [readme.first](/testingproducts/PT-Mainline/blob/develop/analytics/com.hcl.test.reports.registry.webapp/README.first) in the repository.

# Guidelines

- The conventional runtime name for RTCP is `WebSphere Application Server Liberty` (mind the capital S in `WebSphere`) (1)

- In component `analytics`, the file `org.eclipse.wst.common.project.facet.core.xml` **must** contain the line `<runtime name="WebSphere Application Server Liberty"/>`.

- In other components, the file `org.eclipse.wst.common.project.facet.core.xml` **must not** contain a `<runtime>`line. A comment is there stating so, and must not be removed. (2)

(1) The name is chosen during runtime creation (usually when creating the server). It is also possible to change the runtime name in Preferences > Server > Runtime Environments. Caution: renaming the runtime does not fix the problem as the original name is kept as the runtime ID. The runtime must be deleted and re-created.

(2) Because these plugins are also used in the context of RPT development, which may not have the WST or the Liberty plugins installed, or which may not be configured against a RTCP server.

# Troubleshooting

### Problems view contains "Missing runtime: XXX"

Make sure you have named your runtime as instructed during [Eclipse Setup](#eclipse-setup).
If not:
1. Delete the server in the Servers view.
2. Go to Preferences > Server > Runtime Environments, select the misnamed runtime, and
delete it.
3. Follow again the instructions from [Eclipse Setup](#eclipse-setup).

### Publish com.hcl.test.reports.webapp failed

1. Make sure you have installed RTCP to a folder that does not require root elevation. 
c:\Program Files (default folder proposed during installation) does.
To circumvent this, either uninstall and reinstall to another folder, or run your 
development eclipse as Administrator. 

2. In the servers view, right-click on your server, and select "Clean Server on Next Start".
The restart the server. You may need to do this twice.

### Exception thrown during startup

If you get this exception:
~~~
[ERROR   ] SRVE0777E: Exception thrown by application class 'org.apache.wink.common.internal.http.Accept.valueOf:139'
java.lang.NullPointerException
    at org.apache.wink.common.internal.http.Accept.valueOf(Accept.java:139)
    at org.apache.wink.server.internal.contexts.HttpHeadersImpl.getAcceptHeader(HttpHeadersImpl.java:152)
    at org.apache.wink.server.internal.contexts.HttpHeadersImpl.getAcceptableMediaTypes(HttpHeadersImpl.java:106)
~~~

... then you are missing the `<classloader delegation="parentLast"/>` in server.xml. 
See the [Eclipse Setup](#eclipse-setup) section.

### Application com.hcl.test.reports.webapp requires feature "openapi"

If you see this exception while attempting to add the application `com.hcl.test.reports.webapp`,
rename the file  `/com.hcl.test.reports.webapp/WebContent/META-INF/openapi.yaml` to
`openapi_.yaml`, then retry adding the application.
After it is successfully added, you can undo the renaming.