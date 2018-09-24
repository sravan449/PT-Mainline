# Development Tasks

## Counters Versioning

Each feature can define a set of counter descriptors. This set is marked with a version, starting at `1.0`.  
Whenever a change occurs in the counter descriptors defined by the feature, the version must be incremented.
There are two type of version increments:
* Major version increments. This must happen when a breaking change occurs, i.e. a counter is removed, renamed or moved, or a counter type is changed in a non-compatible way.
* Minor version increments. This must happen whenever a non-breaking change occurs, e.g. a new counter is added or its type is changed in a compatible way.

The JUnit test will catch these changes and fail if you do not increment the version. In addition, it will tell you whether you need to increment the major version.

### Minor Version Increments
If you need to increment the minor version of the counter descriptors for a feature, edit the `descriptorsRegistry` declaration in plugin.xml, and increment the second part of the version (if there is no point in the version, add it and set the minor version to 1). Examples: `2.1 -> 2.2`, `3 -> 3.1`.

Then run the JUnit test `com.ibm.rational.test.lt.execution.stats.integration.test.counters.GatherReferenceCounters` in project `core.tests/com.ibm.rational.test.lt.execution.stats.integration.test`, and copy the corresponding file from `gather/counters` to `ref/counters` (copy only the file you expect to change, not all!).

### Major Version Increments
Suppose you have a file named `myfeature.descriptors`, declared as version `M.m` in `plugin.xml`.
* Create a copy of `myfeature.descriptors` (before the changes) and name it `myfeature.vM.descriptors` (replacing `M` with the old major version).
* Make the changes to `myfeature.descriptors` (see [these instructions](Setup-Eclipse-for-Editing-Counter-Descriptors-and-Reports) to get content assist while editing a `*.descriptors` file.).
* In `plugin.xml`, go to the `descriptorsRegistry` declaration
* Set the version field to the next major version (i.e. `M+1`)
* Add a `previous` node, point it to myfeature.vM.descriptors, and set its version to `M.m`.
* Save plugin.xml

~~~
      <descriptorsRegistry
            feature="com.ibm.rational.test.lt.feature.myfeature"
            file="counters/com.ibm.rational.test.lt.feature.myfeature.descriptors"
            translationFile="counters/counters.properties"
            version="2.0">
         ...
         <previous
               file="counters/com.ibm.rational.test.lt.feature.myfeature.v1.descriptors"
               translationFile="counters/counters.properties"
               version="1.3">
         </previous>
~~~
**Note**: You do not need to create a `previous` node for all minor versions, only the last minor version

Additional steps: create a mapping file.

The mapping files tells how to convert old paths (from version `M`) to new paths (version `M+1`).
* Open the JUnit test `com.ibm.rational.test.lt.execution.stats.integration.test.counters.CounterValidityTest` in project `core.tests/com.ibm.rational.test.lt.execution.stats.integration.test`
* Set the field `GENERATE_MISSING_MAPPINGS` to `true`
* Run this test as a JUnit Plug-In test (it is normal that the test fails since you have not already gone through all the steps)
* Refresh the project and check that a file named `myfeature.vMvM+1.remaps` was created.
* Move this file in your `counters` folder.
* In `plugin.xml`, go to the `descriptorsRegistry` declaration, and add a `map` node
* Set `file` to the file you have just moved,
* Set `fromVersion` to `M` and `toVersion` to `M+1`.
* Save `plugin.xml`
~~~
      <descriptorsRegistry
            feature="com.ibm.rational.test.lt.feature.myfeature"
            file="counters/com.ibm.rational.test.lt.feature.myfeature.descriptors"
            translationFile="counters/counters.properties"
            version="2.0">
         ...
         <map
               file="counters/com.ibm.rational.test.lt.feature.myfeature.v1v2.remaps"
               fromVersion="1"
               toVersion="2">
         </map>
~~~
* Edit the `*.remaps` file that you have just moved, and edit the line where there is `CHANGE_ME`. If a counter was moved, set the path attribute to the new path (in version `M+1`). If there is no replacement for the original counter, replace the `path=""` attribute with `removed="true"`.
  See [these instructions](Setup-Eclipse-for-Editing-Counter-Descriptors-and-Reports) to get content assist while editing a `*.remaps` file.


## Defining Requirement Candidates

### Definitions

A _requirement_ is a constraint on a counter defined by the user. E.g, "response time 
must be under 200ms", or "Percent Verification Point Passed must be above 95%".

A _requirement candidate_ is a counter that can be used in a requirement. The list of 
requirement candidates is presented to the user in the test editor (either in a test 
or a schedule). The user may select some of these candidates, define a criteria (e.g. 
`>= 95`) to turn these _candidate requirements_ into effective _requirements_.

There are two kinds of requirement candidates:
* The global candidates. E.g. the average response time of all pages, or the overall 
rate of all transactions. These candidates are presented in the schedule editor.
* The per-instance candidates. E.g. the response time of a specific HTTP page, the rate
of a specific transaction. These candidates are presented in the advanced panel of the 
specific instance.

### How to define Requirement Candidates

Use these attributes in the counter descriptors file to define requirement candidates:

`requirementCategory`  
**Use** Mandatory  
**Description** Defines that this counter is a requirement candidate and defines its category.  
**Accepted values** `performance` \| `functional` \| `infrastructure`  

`requirementModelSpec`  
**Use** Only on per-instance candidates  
**Description** Defines the test model element type for which the test editor should show this requirement.   
**Accepted values** This is the type returned by `CBActionElement.getType()` and used in several extension
points. Several values may be specified, separated with a comma.

`requirementComponents`  
**Use** Only for composite counter types  
**Description** Defines the components of the counter that should be candidates.  
**Accepted values** A comma-separated list of component names. E.g. `Mean,Min,Max,Percentile`

`requirementDefaults`  
**Use** Optional  
**Description** Defines the default operator, and optionally the default comparison value, 
and whether this requirement should be enabled by default.
If both the operator and the value are defined, adding a "!" at the end sets the candidate as 
default, and automatically enables it on new tests and in schedules.  
**Accepted values** `<operator>` or `<operator><value>[!]` where `<operator>` is one of 
`<`, `>`, `<=`, `>=`, `=` and `!=` and [] means optional.

## Viewing Available Counters

### In the report
These is how users usually can view the available counters. However to do this, you must open an execution result and can only view counters associated to the features (HTTP, Citrix, Web UI...) of that execution result.

Open an execution result. Click on the Menu, then Edit. Then navigate to any chart or table of the report and click on the small button "Settings" at the top of it. Then navigate to the counters tab, and click on the drop-down arrow next to a counter name. The drop-down will display all available counters for this execution result (i.e. all counters associated to the features associated to the execution result, e.g. core, http, sap, selenium...).

Note: if there are several counters in the counters tab, the drop-down may not display all counters due to restrictions to multi-counters views. In that case, delete all counters but one (you can discard your changes by clicking on the Cancel button at the top of the window).

### In the workbench

RPT provides a view for the developers to inspect the whole list of counters. It can be found under the Test category as "Statistics Counters Registry".
With the drop-down at the top of the view, you can either
- view the counters defined by a specific feature (http, socket, web UI...)
- view all counters
- view all "counters expanded". The expanded counters are used in the queries of a report. It is the path of a counter, with additional segments defining which part of the counter should be used (e.g. Mean, Max, Percentile/80).

Tips:
- You can righ-click on a counter to copy its path to the clipboard (and directly paste it to a .report that you are editing).
- The button "Display translation" lets you switch between the untranslated name of the counters (used during time, or in report queries), and the translated names (displayed in execution reports).
- The counters that are used as arguments of a synthetic counter are marked with a number in the column "Consumers". If you click on such a counter, the list of synthetic counters that use it is displayed in the right-pane.
- When selecting a synthetic counter (a counter whose value is computed from other counters), the details about it are displayed in the right-pane.

### Using REST

Base URL for RPT/HPT: http://127.0.0.1:7878/

Base URL for RTCP/HQS: https://127.0.0.1:5443/results/

The full counters list can be queried using a GET on `baseurl + analytics/descriptors/all`.
To get the expanded list, use `baseurl + analytics/descriptors/all.expanded`.

To filter counters from a specific feature, add the query argument `?feature=featureID`. You may repeat this argument for several features.

The output format is XML by default but it can be JSON if you include the header `Accept: application/json`.

