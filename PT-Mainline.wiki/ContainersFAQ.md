Q: What does the platform look like?

A: Three platforms are currently envisioned:
   1) Docker (pure).
      We build and provide a container and the customer will manage it.
   2) Kubernetes.
   3) ICP.
      If done right, providing Helm Charts, we can run in ICP and other
      Kubernetes platforms.


Q: What operating systems are involved?

A: In HCL we would likely support Docker on Ubuntu, because
   running Red Hat requires purchasing Docker Enterprise Edition.
   It's hard to get Red Hat in HCL, so Ubuntu would be recommended.
   The Container is lightweight, lighter than a virtual machine, and
   built up of layers.
   The base layer is the operating system, and the more layers we can
   re-use across all the products, then the less of a footprint we make
   on the customer server.
   Our products are Java-based so we should have a common base layer
   to include Java.


Q: ICP would be Intel only, right?

A: Yes.


Q: What version of Java should we support?

A: OpenJ9, though the final decision is being made by the group led by
   Alex Mulholland.


Q: What is OpenJ9?

A: OpenJ9 is a Java Virtual Machine developed by IBM and the Eclipse Foundation.
   The J9 VM is the basis of multiple IBM Java offerings, as well as the
   basis of all IBM Java Development kits since version 5.


Q: What is the status of API Testing these days?

A: API Test as a product is no longer under development.
   When you see API Testing referred to in roadmaps, it's just a more
   buzzwordy way of saying "Integration Testing".


Q: Where do we run for Q3 Containers - Docker or ICP?

A: There is pure Docker.
   ICP and OpenShift are built on Kubernetes.
   If we do this right, providing Helm Charts, we can run in
   ICP, OpenShift or other Kubernetes platforms, claiming support for those
   essentially for 'free'.
   Lots of organizations have pure Docker so we need to support that.
   We do need to support both Docker and ICP for Q3 Containers.


Q: What is the cost for dev, support, test, demo to support Docker & ICP?

A: Docker is "time and materials".  There is no up-front cost.
   You can run it on a laptop.  We have a Docker element on the build
   server for RIT.
   ICP has a bit more infrastructure to it.  Kubernetes is the lowest
   common denominator for ICP.  Kubernetes can be deployed to a laptop.
   Developers can use minikube or minishift.
   Additionally, if people are interested in ICP, there is a community
   edition that is free for download.


Q: Aren't there part of the operating system that containers share?

A: The way Docker works, as a container-based system, you use the host
   Linux kernel and all other containers also share the same Linux kernel.
   Some containers may have a dependency on the file system.   


Q: What is the current state of Container support for Integration Tester
   and Service Virtualiztion as of version 9.2?

A: According to Matt, one of the biggest issues is getting information from
   IBM about how to build things (playbooks) for ICP.  We do have a lot
   of that now and can share that with the wider team.

   For SV, one issue is how we were building Docker images before.
   Also for SV, we need new licensing to get into ICP.
   We are not planning on making licensing changes for ICP for FT/PT/IT, only SV.
   For IT only a RIT user license would be needed.


Q: Will Service Virtualization execution in containers with the HCL Edition
   use the same Flexnet metering licensing used for bare metal?

A: Yes, the HCL licensing already works for docker. In short you buy a number of stub hits or you acquire a floating seat license for tests.


Q: What about Kubernetes using Windows as the base operating system?

A: According to Jeff Turnham, HCL, no knowledge of success there.
   Typical to run a VM with Linux with Kubernetes hosted on a Windows box.


Q: Can we do ulimit change to increase file descriptors?

A: Yes (PT).


Q: Does ICP allow for Windows containers yet?

A: (PT) I don't know what Kubernetes supports in Windows, yet.
   (JT) Unaware of any success story with Windows, yet.


Q: Should we support Docker Swarm?

A: No.  Docker Storm is a competitor to Kubernetes, but not one worth
   considering (PT).


Q: Good site about Kubernetes architecture?

A: https://x-team.com/blog/introduction-kubernetes-architecture/


Q: What is the Jira issue number for the RPT Docker prototype?

A: https://jira01.hclpnp.com/browse/TP-50991


Q: The use case around executing a schedule using a containerized workbench with agents in containers is about supporting test automation in a continuous build too chain, right?  It's command line / build tool driven.  Containers are relatively short-lived.

A: Your first question is generally yes. Certainly to begin with we should be tackling the build system integration approach, for that being command line / build tool driven makes good sense (I'd like to see what additional options we could add to the Maven integration though). Containers are short lived.


Q: There is NOT a use case around a long-lived container running the workbench where the customer would interact with the GUI of the workbench, right?

A: I don't expect there to be any need for RPT UI in the container.


Q: What is the 'DCI' component used for?  Does it work in HPTA?

A: DCI is used for the Response Time Breakdown feature & is used for both HPTA & RPTA.  
