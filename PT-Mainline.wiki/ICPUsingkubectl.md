# Executing Tests Only Using kubectl

* Shows how the 'performancetester.automation.containers' works behind the scenes.
* Not user friendly, should use 'performancetester.automation.containers' scripts or Helm charts.

## Create services
>
	kubectl create -f services.yml
	
	cat services.yml
	apiVersion: v1
	kind: Service
	metadata:
	  labels:
	    io.kompose.service: ptwb
	  name: ptwb
	spec:
	  type: NodePort
	  ports:
	  - name: "7080"
	    port: 7080
	    targetPort: 7080
	  - name: "7443"
	    port: 7443
	    targetPort: 7443
	  selector:
	    io.kompose.service: ptwb
	status:
	  loadBalancer: {}
	---
	apiVersion: v1
	kind: Service
	metadata:
	  labels:
	    io.kompose.service: agent1
	  name: agent1
	spec:
	  ports:
	  - name: "7080"
	    port: 7080
	    targetPort: 7080
	  selector:
	    io.kompose.service: agent1
	status:
	  loadBalancer: {}

## Get IP configuration
>
	kubectl get services
	
	NAME      TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                         AGE
	agent1    ClusterIP   10.0.0.119   <none>        7080/TCP                        3m
	ptwb      NodePort    10.0.0.124   <none>        7080:31534/TCP,7443:31020/TCP   3m

## Configure the agents to the workbench
vi deployment.yml (editing AGENT_IP -> 10.0.0.119)
edit RATIONAL_LICENSE_FILE to your license server
edit images to your system (mycluster.icp:8500/daily/ibm-rtw:9.2.1, etc)
possibly change agent names (agent1, etc)
>
	cat deployment.yml
	apiVersion: extensions/v1beta1
	kind: Deployment
	metadata:
	  labels:
	    io.kompose.service: ptwb
	    pt.classification: workbench
	  name: ptwb
	spec:
	  replicas: 1
	  strategy:
	    type: Recreate
	  template:
	    metadata:
	      labels:
	        io.kompose.service: ptwb
	        pt.classification: workbench
	        pt.name: ptwb
	    spec:
	      containers:
	      - command:
	        - /bin/bash
	        - -c
	        - cmdline && sleep 31536000
	        env:
	          - name: RATIONAL_LICENSE_FILE
	            value: 27000@10.115.83.50
	        image: mycluster.icp:8500/daily/ibm-rtw:9.2.1
	        name: ptwb
	        ports:
	        - containerPort: 7080
	        - containerPort: 7443
	        resources: {}
	      restartPolicy: Always
	status: {}
	---
	apiVersion: extensions/v1beta1
	kind: Deployment
	metadata:
	  labels:
	    io.kompose.service: agent1
	    pt.classification: agent
	  name: agent1
	spec:
	  replicas: 1
	  strategy: {}
	  template:
	    metadata:
	      creationTimestamp: null
	      labels:
	        io.kompose.service: agent1
	        pt.classification: agent
	        pt.name: agent1
	    spec:
	      containers:
	      - env:
	        - name: AGENT_NAME
	          value: agent1
	        - name: AGENT_IP
	          value: 10.0.0.119
	        - name: MASTER_NAME
	          value: ptwb
	        image: mycluster.icp:8500/daily/ibm-rpta:9.2.1
	        name: agent1
	        resources: {}
	      restartPolicy: Always
	status: {}
		
## Create pods and get wb pod name
>
	kubectl create -f deployment.yml
	
	kubectl get pods
	
	NAME                      READY     STATUS    RESTARTS   AGE
	agent1-65bf56b6cd-r4mzh   1/1       Running   0          9m
	ptwb-7fc9558ff9-x2jtl     1/1       Running   0          9m
	
	WB_POD=ptwb-7fc9558ff9-x2jtl

## Copy assets in
>
	kubectl cp tests/HelloWorldDocker.zip ${WB_POD}:.

## Execute
>
	kubectl exec -it ${WB_POD} -- bash -c 'export TEST_IMPORT_PATH=HelloWorldDocker.zip && cmdline -workspace /tmp/ws -project HelloWorldDocker -schedule Schedules/vuSch -exportlog tl.log'

## Copy assets out
>
	kubectl cp ${WB_POD}:/tmp/CommandLineLog.txt .
	kubectl cp ${WB_POD}:/tmp/ws/.metadata/.log log.txt
	kubectl cp ${WB_POD}:tl.log .

## Kill systems
>
	kubectl delete -f deployment.yml
	kubectl delete -f services.yml


