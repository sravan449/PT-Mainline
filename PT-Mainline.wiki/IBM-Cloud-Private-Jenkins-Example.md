
## Hello World ICP Example
### Description
This project shows how one could use _IBM Cloud Private / Jenkins / GIT / Performance Tester_ to build a continuous integration pipeline.  It uses the command-line tool kubectl which has been configured to a running ICP instance.  Using kubectl we can easily automate a Jenkins pipeline to run automated performance tests using _Performance Tester_.
### Automated Work Flow
* A build occurs which produces docker images of our daily installs
  * Performance Tester / Functional Tester Workbench
  * Performance Tester Agent 
* Detection of a new build triggers a Jenkins continuous integration build
* The latest automation project is downloaded from git
  * Automation scripts, performance tester projects, etc.
* The docker images are uploaded to a docker registry
* kubectl is configured to a running IBM Cloud Private instance
* Workbench and agents are dynamically provisioned inside the ICP instance
* Performance Tester tests are executed
* Results are validated and saved to Jenkins
* An e-mail is sent

### Links
* [Daily docker images](http://it-build1.nonprod.hclpnp.com/container-based/)
* [Project producing daily container images](https://github01.hclpnp.com/testingproducts/container-based) 
* [performancetester.automation](https://github01.hclpnp.com/richard-ptasnik/performancetester.automation)
  * Latest automation script packages 
    * [IBM version](https://rptcloudbuilder.nonprod.hclpnp.com/jenkins/job/container-testing/job/performancetester.automation.containers/job/master/lastSuccessfulBuild/artifact/output/performancetester.automation.containers.IBM-9.2.1.tar.gz)
    * [HCL version](https://rptcloudbuilder.nonprod.hclpnp.com/jenkins/job/container-testing/job/performancetester.automation.containers/job/master/lastSuccessfulBuild/artifact/output/performancetester.automation.containers.OneTest-9.2.1.tar.gz)
  * [Usage Guide](https://github01.hclpnp.com/testingproducts/PT-Mainline/wiki/IBM-Cloud-Private-Jenkins-PerformanceScripts-Example-How-To)
* [Jenkins pipeline projects/automation](https://github01.hclpnp.com/richard-ptasnik/cloud-end-to-end)
  * [Continuous Integration Testing Explained](https://github01.hclpnp.com/testingproducts/PT-Mainline/wiki/IBM-Cloud-Private-Jenkins-Workflow)
* [Wiki containing links about containers in general](https://github01.hclpnp.com/testingproducts/PT-Mainline/wiki/Containers)
* [Only using kubectl to execute](https://github01.hclpnp.com/testingproducts/PT-Mainline/wiki/ICPUsingkubectl)
* Old Links (Deprecated Projects)
  * [Example project using kubectl to automate Performance Tester](https://github01.hclpnp.com/richard-ptasnik/rpt.docker.compose.helloworld)
    

### Help
For project access contact Richard Ptasnik or the repository owner.



