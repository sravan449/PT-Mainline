Using kubectl in ICP is goofy.
- Install IBM Cloud CLI (not to be confused with IBM Cloud Private CLI)
- Install IBM Cloud Private CLI
- Install kubectl using the ICP installer

[Installing kubectl for ICP](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/manage_cluster/cfc_cli.html)

Each time you want to use kubectl, you need to configure it for your cluster.

  $ bx pr login -a https://10.121.104.23:8443 --skip-ssl-validation

  $ bx pr clusters

  $ bx pr cluster-config mycluster

Note that having configured kubectl for your cluster, this setup will only work for the next 12 hours!  After that you have to configure again.