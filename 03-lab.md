#### Google Cloud Computing Foundations | C01 Cloud Computing Fundamentals
#### M03: Use Google Cloud to Build Your Apps 

# Lab 03-01: Create a Virtual Machine

## Overview 
Compute Engine (IaaS) lets you create virtual machines that run different operating systems, including multiple flavors of Linux (Debian, Ubuntu, Suse, Red Hat, CoreOS) and Windows Server, on Google infrastructure. You can run thousands of virtual CPUs on a system that is designed to be fast and to offer strong consistency of performance.
 
## Task 1: Create a new instance from the Cloud Console
1. In the Cloud Console, on the Navigation menu (Navigation menu), click Compute Engine > VM Instances. 
> Note: This may take a minute to initialize for the first time.

2. To create a new instance, click CREATE INSTANCE.

3. There are many parameters you can configure when creating a new instance. Use the following for this lab:

| Field | Value | Additional Information  
| ---- | ---- | ---- |
| Name | gcevalue | Name for VM instance |
| Region | us-central1 (Iowa) | For more information about regions, see Regions and Zones. |
| Zone | us-central1-f | Note: Remember the zone that you selected: you'll need it later. For more information about zones, see Regions and Zones. |
| Series | N1 | Name of the series |
| Machine Type | 2 vCPU | This is an (n1-standard-2), 2-CPU, 7.5GB RAM instance. Several machine types are available, ranging from micro instance types to 32-core/208GB RAM instance types. For more information, see Machine Types. Note: A new project has a default resource quota, which may limit the number of CPU cores. You can request more when you work on projects outside this lab. |
| Boot Disk	 | New 10 GB balanced persistent disk OS Image: Debian GNU/Linux 10 (buster) | Several images are available, including Debian, Ubuntu, CoreOS, and premium images such as Red Hat Enterprise Linux and Windows Server. For more information, see Operating System documentation. |
| Firewall | Allow HTTP traffic | Select this option in order to access a web server that you'll install later. Note: This will automatically create a firewall rule to allow HTTP traffic on port 80. |

4. Click Create. 
> Note: It should take about a minute for the machine to be created. After that, the new virtual machine is listed on the VM Instances page.

5. To use SSH to connect to the virtual machine, in the row for your machine, click SSH.

## Task 2: Install an NGINX web server
1. Get root access in the SSH terminal
```bash
sudo su -
```

2. As the root user, update OS
```bash
apt-get update
```

3. Install NGINX
```bash
apt-get install nginx -y
```

4. Confirm NGINX is running
```bash
ps auwx | grep nginx
```

5. See the web page by returning to the Cloud Console and click the External IP link in the row for your machine, or add the External IP value to http://EXTERNAL_IP/ in a new browser window or tab.

## Task 3: Create a new instance with gcloud
Instead of using the Cloud Console to create a virtual machine instance, you can use the command line tool gcloud, which is pre-installed in Google Cloud Shell. Cloud Shell is a Debian-based virtual machine loaded with all the development tools you'll need (gcloud, git, and others) and offers a persistent 5-GB home directory.

1. In the Cloud Shell, use gcloud to create a new virtual machine instance from the command line
```bash
gcloud compute instances create gcelab2 --machine-type n1-standard-2 --zone us-central1-f
```
**Expected output:**
```bash
Created [...gcelab2].
NAME: gcelab2
ZONE: us-central1-f
MACHINE_TYPE: n1-standard-2
PREEMPTIBLE:
INTERNAL_IP: 10.128.0.3
EXTERNAL_IP: 34.136.51.150
STATUS: RUNNING
```
> Note: The new instance has these default values:
> * The latest Debian 10 (buster) image.
> * The n1-standard-2 machine type. In this lab, you can select one of these other machine types: n1-highmem-4 or n1-highcpu-4. When you're working on a project outside Qwiklabs, you can also specify a custom machine type.
> * A root persistent disk with the same name as the instance; the disk is automatically attached to the instance.

2. To see all the defaults, run:
```bash
gcloud compute instances create --help
```
> Note: You can set the default region and zones that gcloud uses if you are always working within one region/zone and you don't want to append the --zone flag every time. To do this, run these commands:
> ```bash
> gcloud config set compute/zone ...
> gcloud config set compute/region ...
> ```

3. To exit `help`, press <kbd>CTRL</kbd>+<kbd>C</kbd>.

4. In the Cloud Console, on the Navigation menu, click Compute Engine > VM instances. Your 2 new instances should be listed.

5. You can also use SSH to connect to your instance via gcloud. Make sure to add your zone, or omit the --zone flag if you've set the option globally
```bash
gcloud compute ssh gcelab2 --zone us-central1-f
```
**Expected output:**
```bash
WARNING: The public SSH key file for gcloud does not exist.
WARNING: The private SSH key file for gcloud does not exist.
WARNING: You do not have an SSH key for gcloud.
WARNING: [/usr/bin/ssh-keygen] will be executed to generate a key.
This tool needs to create the directory
[/home/gcpstaging306_student/.ssh] before being able to generate SSH
Keys.
```

6. Type <kbd>Y</kbd> to continue.
```bash
Do you want to continue? (Y/n)
```

7. Press <kbd>ENTER</kbd> through the passphrase section to leave the passphrase empty.
```bash
Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase)
```

8. After connecting, disconnect from SSH by exiting from the remote shell:
```bash
exit
```

## Test your understanding
1. Through which of the following ways can you create a VM instance in Compute Engine?
- [x] The gcloud command line tool
- [x] The Cloud Console

----
# Lab 03-02:  App EngineL Qwik Start - Python

## Overview
App Engine (PaaS) allows developers to focus on doing what they do best, writing code. The App Engine standard environment is based on container instances running on Google's infrastructure. Containers are preconfigured with one of several available runtimes (Java 7, Java 8, Python 2.7, Go and PHP). Each runtime also includes libraries that support App Engine Standard APIs. For many applications, the standard environment runtimes and libraries might be all you need.

The App Engine standard environment makes it easy to build and deploy an application that runs reliably even under heavy load and with large amounts of data. It includes the following features:

* Persistent storage with queries, sorting, and transactions.
* Automatic scaling and load balancing.
* Asynchronous task queues for performing work outside the scope of a request.
* Scheduled tasks for triggering events at specified times or regular intervals.
* Integration with other Google cloud services and APIs.

Applications run in a secure, sandboxed environment, allowing App Engine standard environment to distribute requests across multiple servers, and scaling servers to meet traffic demands. Your application runs within its own secure, reliable environment that is independent of the hardware, operating system, or physical location of the server.

## Enable Google App Enginge Admin API
The App Engine Admin API enables developers to provision and manage their App Engine Applications.

1. In the left menu click APIs & Services > Library.

2. Type "App Engine Admin API" in search box.

3. Click App Engine Admin API.

4. Click Enable. If there is no prompt to enable the API, then it is already enabled and no action is needed.

## Download the Hello World app
1. Enter the following command to copy the Hello World sample app repository to your Google Cloud instance:
```bash
git clone https://github.com/GoogleCloudPlatform/python-docs-samples.git
```

2. Go to directory that contains the sample code
```bash
cd python-docs-samples/appengine/standard_python3/hello_world
```

## Test the application
Test the application using the Google Cloud development server (dev_appserver.py), which is included with the preinstalled App Engine SDK.

1. From within your helloworld directory where the app's app.yaml configuration file is located, start the Google Cloud development server
```bash
dev_appserver.py app.yaml
```
> Note: The development server is now running and listening for requests on port 8080.

2. View the results by clicking the Web preview > Preview on port 8080

## Make a change
You can leave the development server running while you develop your application. The development server watches for changes in your source files and reloads them if necessary.

Let's try it. Leave the development server running. We'll open another command line window, then edit main.py to change "Hello World!" to "Hello, Cruel World!".

1. Click the (+) next to your Cloud Shell tab to open a new command line session.

2. Enter this command to go to the directory that contains the sample code.
```bash
cd python-docs-samples/appengine/standard_python3/hello_world
```

3. Enter the following to open main.py in nano to edit the content.
```bash
nano main.py
```

4. Change "Hello World!" to "Hello, Cruel World!". Exit and save the file.

5. Reload the Hello World! Browser or click the Web Preview > Preview on port 8080 to see the results.

## Deploy your app
1. To deploy your app to App Engine, run the following command from within the root directory of your application where the app.yaml file is located:
```bash
gcloud app deploy
```

2. You will be prompted to enter where your App engine will be located. 
```bash
Please choose the region where you want your App Engine application
located:
[1] asia-east2
[2] asia-northeast1
[3] asia-northeast2
[4] asia-northeast3
[5] asia-south1
[6] asia-southeast2
[7] australia-southeast1
[8] europe-west
[9] europe-west2
[10] europe-west3
[11] europe-west6
[12] northamerica-northeast1
[13] southamerica-east1
[14] us-central
[15] us-east1
[16] us-east4
[17] us-west2
[18] us-west3
[19] us-west4
[20] cancel
Please enter your numeric choice:
```

Enter the number that represents your region. The App Engine application will then be created.

**Example output:**
```bash
Creating App Engine application in project [qwiklabs-gcp-233dca09c0ab577b] and region [asia-south1]....done.
Services to deploy:
descriptor:      [/home/gcpstaging8134_student/python-docs-samples/appengine/standard/hello_world/app.yaml]
source:          [/home/gcpstaging8134_student/python-docs-samples/appengine/standard/hello_world]
target project:  [qwiklabs-gcp-233dca09c0ab577b]
target service:  [default]
target version:  [20171117t072143]
target url:      [https://qwiklabs-gcp-233dca09c0ab577b.appspot.com]
Do you want to continue (Y/n)?
```

3. Enter <kbd>Y</kbd> when prompted to confirm the details and begin the deployment of service.

**Example output:**
```bash
Beginning deployment of service [default]...
Some files were skipped. Pass `--verbosity=info` to see which ones.
You may also view the gcloud log file, found at
[/tmp/tmp.dYC7xGu3oZ/logs/2017.11.17/07.18.27.372768.log].
╔════════════════════════════════════════════════════════════╗
╠═ Uploading 5 files to Google Cloud Storage                ═╣
╚════════════════════════════════════════════════════════════File upload done.
Updating service [default]...done.
Waiting for operation [apps/qwiklabs-gcp-233dca09c0ab577b/operations/2e88ab76-33dc-4aed-93c4-fdd944a95ccf] to complete...done.
Updating service [default]...done.
Deployed service [default] to [https://qwiklabs-gcp-233dca09c0ab577b.appspot.com]
You can stream logs from the command line by running:
  $ gcloud app logs tail -s default
To view your application in the web browser run:
  $ gcloud app browse
```

> Note: If you receive an error as "Unable to retrieve P4SA" while deploying the app, then re-run the above command.

## View your application

1. To launch your browser enter the following command, then click on the link it provides.
```bash
gcloud app browse
```

2. Example output, note that your link will be different:

Did not detect your browser. Go to this link to view your app:
https://qwiklabs-gcp-233dca09c0ab577b.appspot.com

## Test your knowledge

1. Container instances running on Google's infrastructure are preconfigured with which of the several available runtimes?
- [ ] Java 7
- [ ] Java 8
- [ ] PHP
- [ ] Go
- [ ] Python 2.7
- [x] All of them

----
# Lab 03-03: Cloud Functions - Qwikstart - Command Line

## Overview
Cloud Functions (SaaS) is a serverless execution environment for building and connecting cloud services. With Cloud Functions you write simple, single-purpose functions that are attached to events emitted from your cloud infrastructure and services. Your Cloud Function is triggered when an event being watched is fired. Your code executes in a fully managed environment. There is no need to provision any infrastructure or worry about managing any servers.

Cloud Functions can be written in Node.js, Python, and Go, and are executed in language-specific runtimes as well. You can take your Cloud Function and run it in any standard Node.js runtime which makes both portability and local testing a breeze.

### Connect and Extend Cloud Services
Cloud Functions provides a connective layer of logic that lets you write code to connect and extend cloud services. Listen and respond to a file upload to Cloud Storage, a log change, or an incoming message on a Cloud Pub/Sub topic. Cloud Functions augments existing cloud services and allows you to address an increasing number of use cases with arbitrary programming logic. Cloud Functions have access to the Google Service Account credential and are thus seamlessly authenticated with the majority of Google Cloud services such as Datastore, Cloud Spanner, Cloud Translation API, Cloud Vision API, as well as many others. In addition, Cloud Functions are supported by numerous Node.js client libraries, which further simplify these integrations.

### Events and Triggers
Cloud events are things that happen in your cloud environment.These might be things like changes to data in a database, files added to a storage system, or a new virtual machine instance being created.

Events occur whether or not you choose to respond to them. You create a response to an event with a trigger. A trigger is a declaration that you are interested in a certain event or set of events. Binding a function to a trigger allows you to capture and act on events. For more information on creating triggers and associating them with your functions, see Events and Triggers.

### Serverless
Cloud Functions removes the work of managing servers, configuring software, updating frameworks, and patching operating systems. The software and infrastructure are fully managed by Google so that you just add code. Furthermore, provisioning of resources happens automatically in response to events. This means that a function can scale from a few invocations a day to many millions of invocations without any work from you.

### Use Cases
Asynchronous workloads, for example lightweight ETL or cloud automations, like triggering application builds, no longer need their own server and a developer to wire it up. You simply deploy a Cloud Function bound to the event you want and you're done.

The fine-grained, on-demand nature of Cloud Functions also makes it a perfect candidate for lightweight APIs and webhooks. Because there is automatic provisioning of HTTP endpoints when you deploy an HTTP Function, there is no complicated configuration required as there is with some other services. See the following table for additional common Cloud Functions use cases:

| Use Case | Description |
| ---- | ---- |
| Data Processing / ETL | Listen and respond to Cloud Storage events such as when a file is created, changed, or removed. Process images, perform video transcoding, validate and transform data, and invoke any service on the Internet from your Cloud Function. |
| Webhooks | Via a simple HTTP trigger, respond to events originating from 3rd party systems like GitHub, Slack, Stripe, or from anywhere that can send HTTP requests. |
| Lightweight APIs | Compose applications from lightweight, loosely coupled bits of logic that are quick to build and that scale instantly. Your functions can be event-driven or invoked directly over HTTP/S. |
| Mobile Backend | Use Google's mobile platform for app developers, Firebase, and write your mobile backend in Cloud Functions. Listen and respond to events from Firebase Analytics, Realtime Database, Authentication, and Storage. |
| IoT | Imagine tens or hundreds of thousands of devices streaming data into Cloud Pub/Sub, thereby launching Cloud Functions to process, transform and store data. Cloud Functions lets you do in a way that's completely serverless. |

## Create a Function
First, you're going to create a simple function named helloWorld. This function writes a message to the Cloud Functions logs. It is triggered by cloud function events and accepts a callback function used to signal completion of the function.

For this lab the cloud function event is a cloud pub/sub topic event. A pub/sub is a messaging service where the senders of messages are decoupled from the receivers of messages. When a message is sent or posted, a subscription is required for a receiver to be alerted and receive the message. For more information about pub/subs, see Google Cloud Pub/Sub: A Google-Scale Messaging Service.

To create a cloud function:
1. In the Cloud Shell command line, create a directory for the function code.
```bash
mkdir gcf_hello_world
```

2. Move to the `gcf_hello_world` directory.
```bash
cd gcf_hello_world
```

3. Create and open `index.js` to edit.
```bash
nano index.js
```

4. Copy the following into the index.js file
```javascript
/**
* Background Cloud Function to be triggered by Pub/Sub.
* This function is exported by index.js, and executed when
* the trigger topic receives a message.
*
* @param {object} data The event payload.
* @param {object} context The event metadata.
*/
exports.helloWorld = (data, context) => {
const pubSubMessage = data;
const name = pubSubMessage.data
    ? Buffer.from(pubSubMessage.data, 'base64').toString() : "Hello World";
console.log(`My Cloud Function: ${name}`);
};
```

5. Exit nano (Ctrl+x) and save (Y) the file.

## Create a cloud storage bucket
1. Create a new cloud storage bucket for function
```bash
gsutil mb -p [PROJECT_ID] gs://[BUCKET_NAME]
```
* PROJECT_ID is the Project ID in the connection details of this lab
* BUCKET_NAME is the name you give to the bucket. It must be a globally unique name. For more information, see Bucket naming guidelines.

## Deploy your function
When deploying a new function, you must specify `--trigger-topic`, `--trigger-bucket`, or `--trigger-http`. When deploying an update to an existing function, the function keeps the existing trigger unless otherwise specified.

For this lab, you'll set the `--trigger-topic` as hello_world.

1. Deploy the function to a pub/sub topic named hello_world, replacing [BUCKET_NAME] with the name of your bucket:
```bash
gcloud functions deploy helloWorld \
  --stage-bucket [BUCKET_NAME] \
  --trigger-topic hello_world \
  --runtime nodejs8
```
> Note: If you get OperationError, ignore warning and re-run the command.
If prompted, enter <kbd>Y</kbd> to allow unauthenticated invocations of a new function.

2. Verify the status of the function.
```bash
gcloud functions describe helloWorld
```
> Note: An ACTIVE status indicates that the function has been deployed.
> * Every message published in the topic triggers function execution, the message contents are passed as input data.

## Test the function
1. After you deploy the function and know that it's active, test that the function writes a message to the cloud log after detecting an event.
```bash
DATA=$(printf 'Hello World!'|base64) && gcloud functions call helloWorld --data '{"data":"'$DATA'"}'
```

2. The cloud tool returns the execution ID for the function, which means a message has been written in the log.

3. View logs to confirm that there are log messages with that execution ID.

## View logs
Check the logs to see messages in the log history
```bash
gcloud functions logs read helloWorld
```

If the function executed successfully, messages in the log appear as follows:
```bash
LEVEL  NAME        EXECUTION_ID  TIME_UTC                 LOG
D      helloWorld  3zmhpf7l6j5b  2017-12-05 22:17:42.585  Function execution started
I      helloWorld  3zmhpf7l6j5b  2017-12-05 22:17:42.650  My Cloud Function: Hello World!
D      helloWorld  3zmhpf7l6j5b  2017-12-05 22:17:42.666  Function execution took 81 ms, finished with status: 'ok'
```
> Note: The logs will take around 10 mins to appear. Also, the alternative way to view the logs is, go to Logging > Logs Explorer.

## Test your Understanding
1. Serverless lets you write and deploy code without the hassle of managing the underlying infrastructure.
- [x] True
- [ ] False

----
# Lab 03-04: Kubernetes Engine: Qwik Start

## Overview
Google Kubernetes Engine (GKE) provides a managed environment for deploying, managing, and scaling your containerized applications using Google infrastructure. The Kubernetes Engine environment consists of multiple machines (specifically Compute Engine instances) grouped to form a container cluster.

### Cluster orchestration with Google Kubernetes Engine
Google Kubernetes Engine (GKE) clusters are powered by the Kubernetes open source cluster management system. Kubernetes provides the mechanisms through which you interact with your container cluster. You use Kubernetes commands and resources to deploy and manage your applications, perform administrative tasks, set policies, and monitor the health of your deployed workloads.

Kubernetes draws on the same design principles that run popular Google services and provides the same benefits: automatic management, monitoring and liveness probes for application containers, automatic scaling, rolling updates, and more. When you run your applications on a container cluster, you're using technology based on Google's 10+ years of experience with running production workloads in containers.

### Kubernetes on Google Cloud
When you run a GKE cluster, you also gain the benefit of advanced cluster management features that Google Cloud provides. These include:

* Load balancing for Compute Engine instances
* Node pools to designate subsets of nodes within a cluster for additional flexibility
* Automatic scaling of your cluster's node instance count
* Automatic upgrades for your cluster's node software
* Node auto-repair to maintain node health and availability
* Logging and Monitoring with Cloud Monitoring for visibility into your cluster

## Task 1: Set a default compute zone
Your compute zone is an approximate regional location in which your clusters and their resources live. For example, us-central1-a is a zone in the us-central1 region.
```bash
gcloud config set compute/zone us-central1-a
```
**Expected output:**
```bash
Updated property [compute/zone].
```

## Task 2: Create a GKE cluster
A cluster consists of at least one cluster master machine and multiple worker machines called nodes. Nodes are Compute Engine virtual machine (VM) instances that run the Kubernetes processes necessary to make them part of the cluster.
> Note: Cluster names must start with a letter and end with an alphanumeric, and cannot be longer than 40 characters.

To create a cluster, run the following command, replacing [CLUSTER-NAME] with the name you choose for the cluster.
```bash
gcloud container clusters create [CLUSTER-NAME]
```

You can ignore any warnings in the output. It might take several minutes to finish creating the cluster.
**Expected output:**
```bash
NAME: my-cluster
LOCATION: us-central1-a
MASTER_VERSION: 1.21.5-gke.1302
MASTER_IP: 34.69.232.119
MACHINE_TYPE: e2-medium
NODE_VERSION: 1.21.5-gke.1302
NUM_NODES: 3
STATUS: RUNNING
```

## Task 3: Get authentication credentials for the cluster
After creating your cluster, you need authentication credentials to interact with it.

To authenticate the cluster, run the following command, replacing [CLUSTER-NAME] with the name of your cluster:
```bash
gcloud container clusters get-credentials [CLUSTER-NAME]
```
**Expected output:**

```bash
Fetching cluster endpoint and auth data.
kubeconfig entry generated for my-cluster.
```

## Task 4: Deploy an application to the cluster
You can now deploy a containerized application to the cluster. For this lab, you'll run hello-app in your cluster.

GKE uses Kubernetes objects to create and manage your cluster's resources. Kubernetes provides the Deployment object for deploying stateless applications like web servers. Service objects define rules and load balancing for accessing your application from the internet.

1. To create a new Deployment `hello-server` from the hello-app container image, run the following kubectl create command:

```bash
kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:1.0
```
**Expected output:**
```bash
deployment.apps/hello-server created
```
This Kubernetes command creates a Deployment object that represents `hello-server`. In this case, `--image` specifies a container image to deploy. The command pulls the example image from a Container Registry bucket. `gcr.io/google-samples/hello-app:1.0` indicates the specific image version to pull. If a version is not specified, the latest version is used.

2. To create a Kubernetes Service, which is a Kubernetes resource that lets you expose your application to external traffic, run the following kubectl expose command:
```bash
kubectl expose deployment hello-server --type=LoadBalancer --port 8080
```
In this command:
* `--port` specifies the port that the container exposes.
* `type="LoadBalancer"` creates a Compute Engine load balancer for your container.
**Expected output:**
```bash
service/hello-server exposed
```

3. To inspect the hello-server Service, run kubectl get:
```bash
kubectl get service
```
**Expected output**
```bash
NAME              TYPE              CLUSTER-IP        EXTERNAL-IP      PORT(S)           AGE
hello-server      loadBalancer      10.39.244.36      35.202.234.26    8080:31991/TCP    65s
kubernetes        ClusterIP         10.39.240.1                  433/TCP           5m13s
```
> Note: It might take a minute for an external IP address to be generated. Run the previous command again if the EXTERNAL-IP column status is pending.

4. To view the application from your web browser, open a new tab and enter the following address, replacing [EXTERNAL IP] with the EXTERNAL-IP for hello-server.
```
http://[EXTERNAL-IP]:8080
```

## Task 5: Deleting the cluster
1. Delete the cluster
```bash
gcloud container clusters delete [CLUSTER-NAME]
```

2. When prompted, type <kbd>Y</kbd> to confirm.

> Note: Deleting the cluster can take a few minutes. For more information on deleted GKE clusters, view the documentation.

----
# Lab 03-05: Set Up Network and HTTP Load Balancers
In this hands-on lab you'll learn the differences between a network load balancer and an HTTP load balancer and how to set them up for your applications running on Compute Engine virtual machines (VMs).

## Task 1: Set the default region and zone for all resources
1. In Cloud Shell, set the default zone
```bash
gcloud config set compute/zone us-central1-a
```

2. Set the default region
```bash
gcloud config set compute/region us-central1
```

## Task 2: Create multiple web server instances
For this load balancing scenario, create three Compute Engine VM instances and install Apache on them, then add a firewall rule that allows HTTP traffic to reach the instances.

1. Create three new virtual machines in your default zone and give them all the same tag. The code provided sets the zone to us-central1-a. Setting the tags field lets you reference these instances all at once, such as with a firewall rule. These commands also install Apache on each instance and give each instance a unique home page.

```bash
gcloud compute instances create www1 \
  --image-family debian-9 \
  --image-project debian-cloud \
  --zone us-central1-a \
  --tags network-lb-tag \
  --metadata startup-script="#! /bin/bash
    sudo apt-get update
    sudo apt-get install apache2 -y
    sudo service apache2 restart
    echo '<!doctype html><html><body><h1>www1</h1></body></html>' | tee /var/www/html/index.html"
```
```bash
gcloud compute instances create www2 \
  --image-family debian-9 \
  --image-project debian-cloud \
  --zone us-central1-a \
  --tags network-lb-tag \
  --metadata startup-script="#! /bin/bash
    sudo apt-get update
    sudo apt-get install apache2 -y
    sudo service apache2 restart
    echo '<!doctype html><html><body><h1>www2</h1></body></html>' | tee /var/www/html/index.html"
```
```bash
gcloud compute instances create www3 \
  --image-family debian-9 \
  --image-project debian-cloud \
  --zone us-central1-a \
  --tags network-lb-tag \
  --metadata startup-script="#! /bin/bash
    sudo apt-get update
    sudo apt-get install apache2 -y
    sudo service apache2 restart
    echo '<!doctype html><html><body><h1>www3</h1></body></html>' | tee /var/www/html/index.html"
```

2. Create a firewall rule to allow external traffic to the VM instances
```bash
gcloud compute firewall-rules create www-firewall-network-lb \
    --target-tags network-lb-tag --allow tcp:80
```
Now you need to get the external IP addresses of your instances and verify that they are running.

3. Run the following to list your instances. You'll see their IP addresses in the EXTERNAL_IP column:
```bash
gcloud compute instances list
```

4. Verify that each instance is running with curl, replacing [IP_ADDRESS] with the IP address for each of your VMs.
```bash
curl http://[IP_ADDRESS]
```

## Task 3: Configure the load balancing service
When you configure the load balancing service, your virtual machine instances will receive packets that are destined for the static external IP address you configure. Instances made with a Compute Engine image are automatically configured to handle this IP address.

1. Create a static external IP address for your load balancer
```bash
gcloud compute addresses create network-lb-ip-1 \
 --region us-central1
```

2. Add a legacy HTTP health check resource
```bash
gcloud compute http-health-checks create basic-check
```

3. Add a target pool in the same region as your instances. Run the following to create the target pool and use the health check, which is required for the service to function
```bash
gcloud compute target-pools create www-pool \
    --region us-central1 --http-health-check basic-check
```

4. Add the instances to the pool
```bash
gcloud compute target-pools add-instances www-pool \
    --instances www1,www2,www3
```

5. Add a forwarding rule
```bash
gcloud compute forwarding-rules create www-rule \
    --region us-central1 \
    --ports 80 \
    --address network-lb-ip-1 \
    --target-pool www-pool
```

## Task 4: Sending traffic to your instances
Now that the load balancing service is configured, you can start sending traffic to the forwarding rule and watch the traffic be dispersed to different instances.

Enter the following command to view the external IP address of the www-rule forwarding rule used by the load balancer
```bash
gcloud compute forwarding-rules describe www-rule --region us-central1
```
Use curl command to access the external IP address, replacing IP_ADDRESS with an external IP address from the previous command

```bash
while true; do curl -m1 IP_ADDRESS; done
```

The response from the curl command alternates randomly among the three instances. If your response is initially unsuccessful, wait approximately 30 seconds for the configuration to be fully loaded and for your instances to be marked healthy before trying again.

Use <kbd>CTRL</kbd> + <kbd>C</kbd> to stop running the command.

## Task 5: Create an HTTP load balancer
HTTP(S) Load Balancing is implemented on Google Front End (GFE). GFEs are distributed globally and operate together using Google's global network and control plane. You can configure URL rules to route some URLs to one set of instances and route other URLs to other instances. Requests are always routed to the instance group that is closest to the user, if that group has enough capacity and is appropriate for the request. If the closest group does not have enough capacity, the request is sent to the closest group that does have capacity.

To set up a load balancer with a Compute Engine backend, your VMs need to be in an instance group. The managed instance group provides VMs running the backend servers of an external HTTP load balancer. For this lab, backends serve their own hostnames.

1. Create the load balancer template:
```
gcloud compute instance-templates create lb-backend-template \
   --region=us-central1 \
   --network=default \
   --subnet=default \
   --tags=allow-health-check \
   --image-family=debian-9 \
   --image-project=debian-cloud \
   --metadata=startup-script='#! /bin/bash
     apt-get update
     apt-get install apache2 -y
     a2ensite default-ssl
     a2enmod ssl
     vm_hostname="$(curl -H "Metadata-Flavor:Google" \
     http://169.254.169.254/computeMetadata/v1/instance/name)"
     echo "Page served from: $vm_hostname" | \
     tee /var/www/html/index.html
     systemctl restart apache2'
```

2. Managed instance groups (MIGs) let you operate apps on multiple identical VMs. You can make your workloads scalable and highly available by taking advantage of automated MIG services, including: autoscaling, autohealing, regional (multiple zone) deployment, and automatic updating. Create a managed instance group based on the template:

```bash
gcloud compute instance-groups managed create lb-backend-group \
   --template=lb-backend-template --size=2 --zone=us-central1-a
```

3. Create the `fw-allow-health-check` firewall rule. This is an ingress rule that allows traffic from the Google Cloud health checking systems (`130.211.0.0/22` and `35.191.0.0/16`). This lab uses the target tag `allow-health-check` to identify the VMs.
```bash
gcloud compute firewall-rules create fw-allow-health-check \
    --network=default \
    --action=allow \
    --direction=ingress \
    --source-ranges=130.211.0.0/22,35.191.0.0/16 \
    --target-tags=allow-health-check \
    --rules=tcp:80
```

4. Now that the instances are up and running, set up a global static external IP address that your customers use to reach your load balancer.
```bash
gcloud compute addresses create lb-ipv4-1 \
    --ip-version=IPV4 \
    --global
```
Note the IPv4 address that was reserved:
```bash
gcloud compute addresses describe lb-ipv4-1 \
    --format="get(address)" \
    --global
```

5. Create a health check for the load balancer:
```bash
gcloud compute health-checks create http http-basic-check \
    --port 80
```

6. Create a backend service:
```bash
gcloud compute backend-services create web-backend-service \
    --protocol=HTTP \
    --port-name=http \
    --health-checks=http-basic-check \
    --global
```

7. Add your instance group as the backend to the backend service
```bash
gcloud compute backend-services add-backend web-backend-service \
    --instance-group=lb-backend-group \
    --instance-group-zone=us-central1-a \
    --global
```

8. Create a URL map to route the incoming requests to the default backend service
```bash
gcloud compute url-maps create web-map-http \
    --default-service web-backend-service
```

> URL map is a Google Cloud configuration resource used to route requests to backend services or backend buckets. For example, with an external HTTP(S) load balancer, you can use a single URL map to route requests to different destinations based on the rules configured in the URL map:
> * Requests for https://example.com/video go to one backend service.
> * Requests for https://example.com/audio go to a different backend service.
> * Requests for https://example.com/images go to a Cloud Storage backend bucket.
> * Requests for any other host and path combination go to a default backend service.

9. Create a target HTTP proxy to route requests to your URL map:
```
gcloud compute target-http-proxies create http-lb-proxy \
    --url-map web-map-http
```

10. Create a global forwarding rule to route incoming requests to the proxy:
```bash
gcloud compute forwarding-rules create http-content-rule \
    --address=lb-ipv4-1\
    --global \
    --target-http-proxy=http-lb-proxy \
    --ports=80
```
> A forwarding rule and its corresponding IP address represent the frontend configuration of a Google Cloud load balancer. For a general understanding of forwarding rules, see Forwarding rule concepts.

## Task 6: Testing traffic sent to your instances

1. In the Cloud Console, from the Navigation menu, go to Network services > Load balancing.

2. Click on the load balancer that you just created (web-map-http).

3. In the Backend section, click on the name of the backend and confirm that the VMs are Healthy. If they are not healthy, wait a few moments and try reloading the page.

4. When the VMs are healthy, test the load balancer using a web browser, going to http://IP_ADDRESS/, replacing IP_ADDRESS with the load balancer's IP address.

This may take three to five minutes. If you do not connect, wait a minute, and then reload the browser.

Your browser should render a page with content showing the name of the instance that served the page, along with its zone (for example, `Page served from: lb-backend-group-xxxx`).