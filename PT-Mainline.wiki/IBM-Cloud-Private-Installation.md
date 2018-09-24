## Installation
These instructions come from Owen Burroughs of the RIT team.  That team has ICP is running on Red Hat Enterprise Linux Server release 7.3

Although you probably won’t hit this (I did because of default naming in Centos), make sure the hostname of the machine is in lowercase only

I have installed ICP 2.1.0.3 CE (single node). I do not believe it is worth installing any earlier version, not least because upgrading from 2.1.0.2 failed for me. 

The ultimate install followed the instructions at:

[Installing IBM Cloud Private-CE](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/installing/install_containers_CE.html)

The various command line tools you need to use have to be installed/configured separately afterwards as they are not part of the main install:

[kubectl](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/manage_cluster/cfc_cli.html)

[ICP Command Line](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/manage_cluster/install_cli.html)

[Helm](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/app_center/create_helm_cli.html)