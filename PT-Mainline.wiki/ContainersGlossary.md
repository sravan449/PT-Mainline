### API Server
This component of the Master Node is the entry point for all the REST commands used to control the cluster.

### Container
A container is a runtime instance of an image - what the image becomes in 
memory when execute (that is, an image iwth state, or a user process).
A container runs natively on Linux and shares the kernel of the host machine
with other containers.  It runs a discrete process, taking no more mrmory than
any other executable, making it lightweight.

### Deployment
Once you have a running Kubernetes cluster, you can deploy your containerized applications on top of it. To do so, you create a Kubernetes Deployment configuration. The Deployment instructs Kubernetes how to create and update instances of your application. Once you've created a Deployment, the Kubernetes master schedules mentioned application instances onto individual Nodes in the cluster.

Once the application instances are created, a Kubernetes Deployment Controller continuously monitors those instances. If the Node hosting an instance goes down or is deleted, the Deployment controller replaces it. This provides a self-healing mechanism to address machine failure or maintenance.

In a pre-orchestration world, installation scripts would often be used to start applications, but they did not allow recovery from machine failure. By both creating your application instances and keeping them running across Nodes, Kubernetes Deployments provide a fundamentally different approach to application management.

### Docker
Docker is an open platform for developers and sysadmins to build, ship, and run distributed applications, whether on laptops, data center VMs, or the cloud.  Docker performs operating-system-level virtualization also known as containerization.

In a K8s environment, Docker runs on each of the worker nodes, and runs the configured pods.  Docker takes care of downloading the images and starting the containers.

### etcd storage
This component of the Master Node is a simple, distributed, consistent key-value store, mainly used for shared configuration and service discovery.  An example of data stored by K8s in etcd is jobs being scheduled, created and deployed, pod/service details and stae, namespaces and replication information, etc.

### Helm
Helm helps you manage Kubernetes applications by helping to define, install and upgrade Kubernetes applications.

- Charts describe even the most complex apps, provide repeatable app installation, and serve asa  single point of authority.

- Charts take the pain out of updates with in-place upgrades and cusotm hooks.

- Charts are easy to version, share, and host on public or private servers.

- Use helm rollback to roll back to an older version of a release with ease.

### IBM Cloud Private
IBM Cloud Private is an application platform for developing and managing on-premises, containerized applications. It is an integrated environment for managing containers that includes the container orchestrator Kubernetes, a private image repository, a management console, and monitoring frameworks.

### ICP
Insane Clown Posse, an American hip-hop band.

### Image
An image is an executable package that includes everything neeeded to run
an application - the code, a runtime, libraries, environment variables, and
configuration files.  A container is launched by running an image.

### juggalos
A juggalo (feminine juggalette, or juggala in Spanish) is a fan of the group Insane Clown Posse. Juggalos have developed their own idioms, slang, and characteristics.
Common characteristics of identifying a member of the Juggalo subculture are as follows:

- Drinking and spraying the inexpensive soft drink Faygo.
- Listening to horrorcore and other types of underground rap music.
- Wearing face paint, generally those either like an evil clown or perhaps similar to corpse paint.
- Wearing HatchetGear.
- Having the Hatchet man logo applied on personal effects and, die cast, worn as jewelry.
- Doing hair in the spider legs style, i.e. like the Twiztid members.
- Displaying the gesture of wicked clown, the westside sign with the left hand and the C sign in ASL with the right, with arms crossed over.
- Making and responding to "whoop, whoop" calls.
- Expressing a (generally) tongue-in-cheek obsession with murder, committed with a blade weapon.

### kubectl ("cube-control")
Command line tool used to communicate with the API service and send commands to the master node.

### kubelet
This component runs in a worker node.  It gets the configuration of a pod from the apiserver and ensures that the described containers are up and running.

### kube-proxy
This component runs in a worker node, acting as a network proxy and a load balancer for a service on a single worker node.  It takes care of the network routing for TCP and UDP packets.

### Kubernetes
Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications.

Kubernetes groups containers that make up an application into logical units for easy management and discovery. Kubernetes builds upon 15 years of experience of running production workloads at Google, combined with best-of-breed ideas and practices from the community.

### Master Node
The master node is responsible for the management of a Kubernetes cluster.

### Minikube
Minikube is a tool that makes it easy to run Kubernetes locally.  Minikube runs a single-node Kubernetes cluster inside a VM on your laptop for users looking to try out Kubernetes or develop with it day-to-day.

### Minishift
Minishift is a tool that helps you run OpenShift locally by launching a single-node OpenShift cluster inside a virtual machine. With Minishift you can try out OpenShift or develop with it, day-to-day, on your local machine.

You can run Minishift on the Windows, macOS, and GNU/Linux operating systems. Minishift uses libmachine for provisioning virtual machines, and OpenShift Origin for running the cluster.

### OpenShift
OpenShift is a computer software product from Red Hat for container-based software deployment and management. It is a supported distribution of Kubernetes using Docker containers and DevOps tools for accelerated application development.

### Pod
The smallest unit that can be scheduled to be deployed through K8s is a pod.

Kubernetes targets the management of elastic applications that consist of multiple microservices communicating with each other.  Often those microservices are tightly coupled forming a group of conatiners that would typically, in a non-containerized setup run together on one server.

This group of containers would share storage, Linux namespaces, cgroups, IP addresses.  Pods are not intended to live long.  They are created, destroyed and re-created on demand, based on the state of the server and the service itself.

A K8s service is an abstraction on top of a number of pods, typically requiring to run a proxy on top, for other services to communicate with it via a virtual IP address.  This is where you can configure load balancing for your numerous pods and expose them as a service.

### ReplicationController
A ReplicationController ensures that a specified number of pod replicas are running at any one time (ie are up and available).

### scheduler
This component of the Master Node handles the deployment of configured pods and services onto the nodes.

### Virtual Machine
A virtual machine runs a full-blown "guest" operating system with virtual
access to host resources through a hypervisor.

### Worker Node
Pods run on the worker node, which contains all the services necessary to manage the networking between containers, communicate with the master node and assign resources to the containers scheduled.
