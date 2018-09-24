## Description
This documents the current continuous integration testing process related to docker containers and IBM Cloud Private.

For access to the Jenkins system contact Richard Ptasnik.

## Projects
### Name
[Pipeline ibm-rtw-container-continuous](https://rptcloudbuilder.nonprod.hclpnp.com/jenkins/job/container-testing/job/ibm-rtw-container-continuous/)
### Description
Runs an end-to-end continuous integration testing of the 'ibm-rtw' and 'ibm-rpta' docker images.  It is triggered docker image builds occurring at http://it-build1.nonprod.hclpnp.com/container-based/master/I/. 

Items include, importing the new images into various docker registries. 
Performs a smoke test Performance Tester command-line using docker. 
Performs a smoke test Performance Tester command-line using IBM Cloud Private. 
Validation of the results including junits against the command-line output. 
e-mails
### Example 

![](http://rptcloudbuilder.nonprod.hclpnp.com/images/jenkins/1.png)

### Name
[Pipeline import-docker-images-icp](https://rptcloudbuilder.nonprod.hclpnp.com/jenkins/job/container-testing/job/import-docker-images-icp/)
### Description
Imports the latest available docker images into all our ICP environments
### Parameters 
![](http://rptcloudbuilder.nonprod.hclpnp.com/images/jenkins/2.png)
### Example 
![](http://rptcloudbuilder.nonprod.hclpnp.com/images/jenkins/3.png)

### Name
[Pipeline rpt.docker.icp.helloworld](https://rptcloudbuilder.nonprod.hclpnp.com/jenkins/job/container-testing/job/rpt.docker.icp.helloworld/)
### Description
Example project to test Performance Tester end-to-end command-line smoke test using IBM Cloud Private.
### Parameters 
![](http://rptcloudbuilder.nonprod.hclpnp.com/images/jenkins/4.png)
### Example 
![](http://rptcloudbuilder.nonprod.hclpnp.com/images/jenkins/5.png)
### Output Artifacts 
![](http://rptcloudbuilder.nonprod.hclpnp.com/images/jenkins/6.png)

### Name
[JUNIT - rpt.docker.icp.helloworld](https://rptcloudbuilder.nonprod.hclpnp.com/jenkins/job/container-testing/job/junits/job/rpt.docker.icp.helloworld/)
### Description
Run the JUNITs against the last successful rpt.docker.icp.helloworld
### Example 
![](http://rptcloudbuilder.nonprod.hclpnp.com/images/jenkins/7.png)
### Output Artifacts 
![](http://rptcloudbuilder.nonprod.hclpnp.com/images/jenkins/8.png)
![](http://rptcloudbuilder.nonprod.hclpnp.com/images/jenkins/9.png)

