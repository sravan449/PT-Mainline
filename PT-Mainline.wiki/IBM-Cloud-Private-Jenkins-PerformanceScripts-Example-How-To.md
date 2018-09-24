# performancetester.automation
[Project Home](https://github01.hclpnp.com/richard-ptasnik/performancetester.automation)
## Contacts
* Richard Ptasnik
* ICP Environment - Kevin Mooney
## Requirements
Setting up a IBM Cloud Environment is a non-trivial task.  A test environment('10.121.104.23') is already setup in Cary and managed by Kevin Mooney.  The tools are already installed.  Request user access from Kevin Mooney/Richard Ptasnik.

* IBM Cloud Private
* Linux 
* kubectl
* Bluemix 'bx pr' cloud private utilities

## Basic Setup
* ssh into a Linux system (10.121.104.23)
* Create a directory to contain the examples scripts
  * mkdir testicp
  * cd testicp
* Get access to the _performancetester.automation.containers_ scripts
  * Latest build page
    * [IBM](https://rptcloudbuilder.nonprod.hclpnp.com/jenkins/job/container-testing/job/performancetester.automation.containers/job/master/lastSuccessfulBuild/artifact/output/performancetester.automation.containers.IBM-9.2.1.tar.gz)
    * [HCL](https://rptcloudbuilder.nonprod.hclpnp.com/jenkins/job/container-testing/job/performancetester.automation.containers/job/master/lastSuccessfulBuild/artifact/output/performancetester.automation.containers.OneTest-9.2.1.tar.gz)
  * Install instructions
    * wget --no-check-certificate https://rptcloudbuilder.nonprod.hclpnp.com/jenkins/job/container-testing/job/performancetester.automation.containers/job/master/lastSuccessfulBuild/artifact/output/performancetester.automation.containers.OneTest-9.2.1.tar.gz
    * tar -xf performancetester.automation.containers.OneTest-9.2.1.tar.gz
    * bash performancetester.automation.containers/postInstall

* Use 'bx pr' to login to the ICP system which configures kubectl
  * bx pr login -a https://10.121.104.23:8443 --skip-ssl-validation
  * On 10.121.104.23 a script has been created to login automatically
     * loginICP
	
## Project Directory

	|---------------------------------------------------------------------------------------------
	|
	| Configuration
	|--> properties.source                     -Configuration properties related to creating wb/agents
	|--> tests/
	|  ---------> HelloWorldDocker.zip         -Example exported test assets with dependencies
	|  ---------> HelloWorldDocker.properties  -Example command line -config file
	|
	|
	|--> yml/                          -Deployment description files
	|  ---------> sut.service.yml      -Example web server used as the SUT in the HelloWorldDocker.zip
	|  ---------> sut.deployment.yml
	|--> tmp/                          -Temp/log files
	|--> lib/                          -Common bash functions
	|--> results/                      -Location of workspace logs, exported results
	|
	| Utilities
	|--> systemUp           - Utility to deploy workbench and agents
	|--> systemDown         - Utility to un-deploy workbench and agents
	|--> executeInContainer - Utility to launch a test using the running system
	|--> bashInto           - Utility to bash into the workbench or agent
	|
	| To run the default example:
	|     - bx pr login -a https://{ICP_MANAGEMENT_IP}:8443 --skip-ssl-validation     # Sets up kubectl
	|     - systemUp              # Start the wb/agents
	|     - executeInContainer    # Execute according to the configuration in properties.source
	|     - systemDown            # Stop the wb/agents
	|
	|---------------------------------------------------------------------------------------------

## Run the Sample Project
The project contains an example working project which runs out of the box using defaults.  It deploys an example web server along with the workbench and agent.
### Bring the system up
	./systemUp
	Using namespace 'harlequin-user-ptasnik'
	service "system-under-test" created
	service "rptwb" created
	service "rptagent1" created
	service "rptagent2" created
	deployment.extensions "system-under-test" created
	deployment.extensions "rptwb" created
	deployment.extensions "rptagent1" created
	deployment.extensions "rptagent2" created
	Waiting for all pods in namespace to become 'Running'
### Execute the example default Performance Tester run
	./executeInContainer
	Using namespace 'harlequin-user-ptasnik'
	rptwb-9698758cd-mthkh
	Importing: /tmp/Test.zip
	
	EclipseHome: /opt/IBM/SDP/
	Workspace: /tmp/workspace
	Project: HelloWorldDocker
	Schedule/Test: null
	Plugins: /opt/IBM/IBMIMShared/plugins
	Results Location: auto
	VMARGS: -Djava.io.tmpdir=/tmp/rpttmp
	
	July 17, 2018 7:02:57 PM UTC
	Parameters accepted, starting the test run.
	
	Instantiated adapter with /opt/IBM/SDP/
	
	July 17, 2018 7:05:13 PM UTC
	Test execution completed in 136.075 seconds.
	Imported: /tmp/Test.zip
	
	EclipseHome: /opt/IBM/SDP/
	Workspace: /tmp/workspace
	Project: HelloWorldDocker
	Schedule/Test: Schedules/vuSch.testsuite
	Plugins: /opt/IBM/IBMIMShared/plugins
	Results Location: auto
	VMARGS: -Djava.io.tmpdir=/tmp/rpttmp
	
	July 17, 2018 7:05:13 PM UTC
	Parameters accepted, starting the test run.
	
	Instantiated adapter with /opt/IBM/SDP/
	2018-07-17 19:06:46.953:INFO::Thread-7: Logging initialized @92635ms
	--VERDICT=PASS:Passed
	
	July 17, 2018 7:07:53 PM UTC
	Test execution completed in 159.844 seconds.
	Results in ./results/20180717-1502

### Locate the logs and exported results
	ls ./results/20180717-1502
	console.out.txt  eclipse.log  results.tar.gz  testlog.txt

### ssh into the workbench or agents 
	/bashInto wb
	Using namespace 'harlequin-user-ptasnik'
	Executing:  kubectl --namespace=harlequin-user-ptasnik exec -it rptwb-9698758cd-mthkh -- /bin/bash
	root@rptwb-9698758cd-mthkh:/opt/IBM/SDP/cmdline# echo "I'm in the wb"
	I'm in the wb
	root@rptwb-9698758cd-mthkh:/opt/IBM/SDP/cmdline# exit
	exit
	[ptasnik@harlequin hello-world-deployment]$ ./bashInto agent 1
	Using namespace 'harlequin-user-ptasnik'
	Executing:  kubectl --namespace=harlequin-user-ptasnik exec -it rptagent1-68f6976785-8rxfl -- /bin/bash
	root@rptagent1-68f6976785-8rxfl:/opt/IBM/SDP/Majordomo# echo "I'm in agent 1"
	I'm in agent 1
	root@rptagent1-68f6976785-8rxfl:/opt/IBM/SDP/Majordomo# exit
	exit

### Bring the system down
	./systemDown
	Using namespace 'harlequin-user-ptasnik'
	deployment.extensions "system-under-test" deleted
	deployment.extensions "rptwb" deleted
	service "rptagent1" deleted
	service "rptagent2" deleted
	service "system-under-test" deleted
	service "rptwb" deleted
	deployment.extensions "rptagent1" deleted
	deployment.extensions "rptagent2" deleted

## Customizing Scripts
Edit the following files to customize the automation to your environment.  All the scripts are bash files, so feel free to customize them.

	cat properties.source
	export HCL_LICENSING_URL=${HCL_LICENSING_URL:-'https://hclsoftware-uat.compliance.flexnetoperations.com'}
	export HCL_LICENSING_ID=${HCL_LICENSING_ID:-'B5CLC2XFBKV3'}
	export WB_IMAGE=${WB_IMAGE:-'rptcloudbuilder.nonprod.hclpnp.com:5000/hcl-onetest-suite:9.2.1'}
	export AGENT_IMAGE=${AGENT_IMAGE:-'rptcloudbuilder.nonprod.hclpnp.com:5000/hcl-onetest-agent:9.2.1'}
	export SYSTEM_UNDER_TEST_IMAGE=${SYSTEM_UNDER_TEST_IMAGE:-'docker.io/hcltesting/web-server'}
	export PT_SCH_NAME=${PT_SCH_NAME:-'Schedules/vuSch'}
	export PT_PROJECT_NAME=${PT_PROJECT_NAME:-'HelloWorldDocker'}
	export PT_TEST_ZIP=${PT_TEST_ZIP:-$PT_BASEDIR/tests/HelloWorldDocker.zip}
	export PT_CMDLINE_CONFIG=${PT_CMDLINE_CONFIG:-$PT_BASEDIR/tests/HelloWorldDocker.properties}
	export PT_AGENT_PREFIX=${PT_AGENT_PREFIX:-'agent'}
	export PT_AGENT_AMOUNT=${PT_AGENT_AMOUNT:-'2'}

Performance Tester config file

	cat tests/HelloWorldDocker.properties
	project=$PT_PROJECT_NAME
	schedule=$PT_SCH_NAME
	workspace=/tmp/workspace
	results=auto
	exportlog=/tmp/results/testlog.txt
	vmargs=-Djava.io.tmpdir=/tmp/rpttmp

### When things to wrong...
	export PT_DEBUG=debug
also edit the scripts with
	#!/bin/bash -x