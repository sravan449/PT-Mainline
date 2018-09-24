# Edit Reports in Eclipse

The web analytics reports are defined in XML format. You may need to edit directly the XML file in order to:
* make large changes to reports
* make the report [translatable](#translating-reports) (using strings from an associated .properties file)
* [hide](#hiding-a-report-a-page-or-a-view) the report, some pages or some views based on some condition
* include a part of a report into another report (without actually copying it)
* define CS (context sensitive) help IDs for each page of the report
* define sub-pages

## Declaration
Reports are declared using the extension point `com.ibm.rational.test.lt.execution.stats.core.reports`.
It is advised to import the project `com.ibm.rational.test.lt.execution.stats.core.eclipse` to have the schema available for editing the extensions using Eclipse editor.  
An extension merely declares an ID, a report file, and an associated properties file that provide translations.  
By convention a report ID should be composed of a single word in lower case, or several words in Caml case. E.g: `http`, `citrixVerificationPoint`... It should be short but unique.

## File extension & Location
The file extension for report files is `report` for regular reports, and `trendreport` for trending reports. The format is absolutely identical, and the extension is only used to discriminate the category the report should fall into.
By convention, reports should be located in a `reports` folder directly under the root of the plugin that contributes them.

## Importing the report schema into Eclipse
Before doing any change to a report file, it is advised to add the XSD (schema) file defining the syntax of the report. This will enable the Eclipse XML editor for content assist and error checking.

Follow [these instructions](Setup-Eclipse-for-Editing-Counter-Descriptors-and-Reports) to do so.

Once this is done, double-clicking on a `.report` or `.trendreport` file should bring up Eclipse XML editor. You are free to use the Design or Source tabs. In the source tab, you can use Ctrl+Space to auto complete and see which elements can be added at the current position.

## Report format
The report format is somewhat documented by the schema above. You can also check the Java code located in the package `com.ibm.rational.test.lt.execution.stats.core.report.model` in project `core/com.ibm.rational.test.lt.execution.stats.core`.
Also, the best source for learning are examples. Search for projects that contain a `reports` folder in the repository.

# Development Tasks

## Translating reports
Any label, such as report name, page name or view name, presented to the user is by default translatable. You should use a key instead of the English label, and provide the English version in the associated .properties file.
The .properties file is associated to a report through the declaration in plugin.xml, as in the following example:
~~~
   <report
         content="reports/MyReport.report"
         id="myreport"
         kind="regular"
         translationFile="reports/report.properties">
   </report>
~~~

Content of `MyReport.report`:
~~~
<Report xmlns="http://com.ibm.rational.test.lt.stats" version="5" name="MYREPORT" >
  <pages>
    <Page name="MYPAGE">
    </Page>
  </pages>
</Report>
~~~
Content of `report.properties`:
~~~
MYREPORT=My Report
MYPAGE=My Page
~~~

You can exclude a label from translation by prefixing it with `!`.
~~~
<Report xmlns="http://com.ibm.rational.test.lt.stats" version="5" name="!This is not translated" >
</Report>
~~~

## Context-sensitive help
To enable context-sensitive help on your reports, you need to add two kinds of attributes to your report XML file:
* set `helpProvider="pluginId"` on the `<Report>` element
* set `helpId="id"`" on each `<Page>`element.
If you do so, a question mark should be visible on the active page of a report. The context sensitive topic that will be invoked when clicking on this question mark is `pluginId.id`.

## Versioning

### Why is versioning needed?

* Because users may customize the default reports. We need to know which version of a default report a user report is based on, so we can tell the user that his/her report has to be updated.
* Because default reports are published to RTCP/HQS (when an execution result is published, all associated default reports that are not already on the server are published). To avoid re-uploading the same report over and over, we must know if the local default report is newer that the default report on the server.

### Versioning Strategy

The versioning of reports is done by setting the attribute `reportVersion` in the report file. This is an integer that must be incremented (+1) every time a significant change is made to the report. Significant change means any change except syntactic changes (spaces, indentations, comments).

It may be acceptable to not increment a report version within a sprint, however this may cause issues if several intermediate versions are used to edit a report, or publish reports to RTCP. In case of doubt, do not hesitate to increment the version.

### What to do after a change has been made

1. Import the project `core.tests/com.ibm.rational.test.lt.execution.stats.integration.test` in your workspace.
2. Run the JUnit test `com.ibm.rational.test.lt.execution.stats.integration.test.reports.ReportValidityTest`
3. If the test does not detect any difference, stop here.
4. If the report version has already been incremented in the current sprint (and you do not wish to increment it again), go to step 6.
5. Increment by 1 unit the value of attribute `reportVersion`.
6. Run the JUnit test `com.ibm.rational.test.lt.execution.stats.integration.test.reports.GatherReferenceReports`.
7. In the folder `gather/reports` (refresh if empty), copy the report that you edited to the folder `ref/reports`, to make it the new "reference" report.
8. Run step 2 again to make sure the error is gone.
9. Commit the new version of the report under `ref/reports`. Discard any files under `gather/reports`.

### Caution: do not confuse with the attribute `version`!
This attribute defines the XML format used (this attribute exists since the beginning). All reports delivered in RPT should always use the latest version (defined in `com.ibm.rational.test.lt.execution.stats.core.internal.report.serialize.StatsReportConstants` field `REPORT_CURRENT_VERSION`); this is ensured by the validation JUnit tests. This attribute tells the parser what to expect. It should be using a lower version only in user-created reports (that were created in an older version). Reports are always converted to the last version format when you export a report definition using the wizard, or when you query the report through a REST API.

## Validating a report

The reports can be validated by running a JUnit test, which checks all reports registered in plugin.xml.
To do so:
1. import into your dev workspace the plugin `com.ibm.rational.test.lt.execution.stats.integration.test` (under the folder `core.test`)
2. Select the class `com.ibm.rational.test.lt.execution.stats.integration.test.reports.ReportValidityTest`
3. Right-click, Run As > JUnit Plug-In Test

_Note: These tests are automatically run during RPT, RST and RTW builds_

For each report, the test will display a node `#: report_name REGULAR|TREND`
Under each node, the following tests are run.
- basedOnLastFeatureVersion: If failed, it means that reports do not use the latest versions of counters descriptors. The framework has to upgrade the counter paths to the latest version each time the report is loaded. Check the `<features>` section in the report, verify which features are listed, and change the version attribute to the latest version of the counter descriptors available for these features. You may need to update some counter paths if they have changed since the old version.
- translations: Fails if a string in the report has no matching entry in the associated .properties file.
- semantics: Fails if a `<Query>` or `<Counter>` node's path attribute does not point to a valid counter. If the counter really exists, make sure that it belongs to a feature that is declared in the `<features>` section.
- features: Fails if a report declares it depends on unknown features, or an unknown version of a feature. The feature are those associated to the counter descriptors.
- file: Fails if the report file cannot be read or parsed as XML.
- help: Fails if the report is missing help IDs on pages, or a helpProvider on the report node.
- expansion: Fails if the report cannot be "expanded", i.e. the report has includes, but the includes cannot be resolved.
- entry: Fails if the report fails to be registered to the framework.
- dependencies: Fails if the report "includes" does not point to an existing report.
- version: Fails if the report has the same version as the "reference" report AND the report has a different content. See instructions [above](#what-to-do-when-a-change-is-made-to-a-report).

# Advanced Features

## Hiding a report, a page or a view
You can add a condition to reports, pages and views, so they are visible only if a certain counter is present or absent.
The condition is defined by a `<filter>` element, directly under a `<Report>`, `<Page>` or any view element (`<LineChart>`, `<Table>`...).

The filter accepts two lists: `<requiredCounters>` and `<forbiddenCounters>`. An element is displayed if:
* none of the forbidden counters exists
* any of the required counters exists

Each Counter is defined by a path, and must correspond to a counter (not a folder, nor a query). A counter exists when the runtime code creates it, even if no values were emitted for that counter.

### Example 1

The annotated report/page/view is displayed only if the counter `/RateRunner/[RATERUNNER]/Started` exists.
~~~
    <filter>
  	<requiredCounters>
  	   <Counter path="/RateRunner/[RATERUNNER]/Started"/>
  	</requiredCounters>
    </filter>
~~~

### Example 2

The annotated report/page/view is displayed only if the counter `/ResourceMonitoring/[RESOURCE]` does not exist.
~~~
    <filter>
  	<forbiddenCounters>
  	   <Counter path="/ResourceMonitoring/[RESOURCE]"/>
  	</forbiddenCounters>
    </filter>
~~~

## Definining Sub (aka Drilldown) Pages
_To be completed_

## Including Reusable Bits in Several Reports

You can show the same view (graph or table), set of views, page or set of pages in several 
reports. This happens in two steps:
1. define a report that contains the elements to reuse
2. include these elements in other reports.

### Defining a reusable element

Actually, any view or page in any report can be "included" from another report. However 
you may want to define a hidden report whose sole purpose is to provide the reusable 
elements.

To do so, define a new report as you would do for a regular, except for the following 
aspects:
- In the .report file, add the attribute `hidden="true"` to the Report element.
- Make the report name not translatable. To do so, add a "!" before its name.
- Do the same for pages, if the pages themselves will not be displayed.

This is an example:
~~~
<?xml version="1.0" encoding="UTF-8"?>
<Report xmlns="http://com.ibm.rational.test.lt.stats/reports" version="6" reportVersion="1" name="!SharedViews" hidden="true">
  <features>
    <Feature id="com.ibm.rational.test.lt.feature.lt" version="2" />
  </features>
  <pages>
    <Page name="!LoadGraph">
        <!-- This page name is not translatable because other reports will include its views, not the page itself -->
        <LineGraph name="Reusable graph">
        </LineGraph>
    </Page>
    <Page name="Shared page">
        <!-- This page name is translatable because other reports will include the page itself -->
        <!-- Add the views to share here -->
    </Page>
  </pages>
</Report>
~~~

### Including elements from another report

There are four supported "includes":
- Include another report's page in a report: define a page as follows:
~~~
<IncludePage name="reportId#pageId"/>
~~~~
- Include all pages from another report in a report: define a page as follows:
~~~
<IncludePage name="reportId"/>
~~~~
- Include another report's view in a page: define a view as follows:
~~~
<IncludeView name="reportId#pageId#viewId"/>
~~~~
- Include all views from another report's page in a page: define a view as follows:
~~~
<IncludeView name="reportId#pageId"/>
~~~~
`reportId` refers to the report ID as it is defined in plugin.xml. `pageId` and `viewId` 
refers to the "name" attribute of the referenced page/view.

For instance, the shared elements defined above can included as follows (provided that 
the report ID is "shared"):
~~~~
<Report xmlns="http://com.ibm.rational.test.lt.stats/reports" version="6" reportVersion="1" name="MY_REPORT">
    <pages>
        <-- This includes the page itself -->
        <IncludePage name="shared:Shared page"/>
        <Page>
            <-- This includes all views defined under the specified page -->
            <IncludeView name="shared:!LoadGraph"/>
        </Page>
    </page>
</Report>
~~~~

### RPT Shared views and pages

To display a page with resource monitoring details, add this to your report's pages:
~~~~
<IncludePage name="shared#RESOURCES"/>
~~~~

To display a graph with user load or rate generator load, add this to your page's views:
~~~~
<IncludeView name="shared#!LoadGraph">
</IncludeView>
~~~~

To display a table with user or rate generator details, add this to your page's views:
~~~~
<IncludeView name="shared#!LoadSummary">
</IncludeView>
~~~~