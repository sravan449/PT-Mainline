Open the Preferences window, open the Content Types pages, and select XML:

![Screenshot](wa/prefs.png)

Add the following items to File associations:
* `*.report`
* `*.trendreport`
* `*.descriptors`
* `*.remaps`

Then go to the XML Catalog page, click Add...

![Screenshot](wa/xmlcatalog.png)

* If the project `core/com.ibm.rational.test.lt.execution.stats.core` is loaded in your 
workspace, select `Workspace` and go to  `com.ibm.rational.test.lt.execution.stats.core/schema/`.
* Otherwise, select `File System`, and go to `<git repository>/core/com.ibm.rational.test.lt.execution.stats.core/schema`.

Select the file `descriptors.xsd`, click OK twice.

Repeat the operation for the other XSD files (`remaps.xsd` and `report.xsd`). You should 
get these user entries in your XML catalog:

![Screenshot](wa/xmlcatalog2.png)

You can now double click and open counter descriptors files (`*.descriptors`), remappings 
(`*.remaps`) and reports (`*.report`, `*.trendreport`), with content assist and validation 
as you type.