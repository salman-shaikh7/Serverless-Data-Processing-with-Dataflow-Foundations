# Dataflow

The **Beam Portability** framework achieves the vision that a developer can use their favorite programming language with their preferred execution backend (runner). 
 
Dataflow allows you to **separate** compute and storage.


>>**Programming Language**:

1.  Python

2.  Java

3.  SQL

4.  GO

* You can have multi-language Pipelines or cross language transform.

<br>

>>**Runner** : 

1.  Local Runner

2.  Dataflow Runner (Runner V2)

3.  Spark Runner 

4.  Apache Flink Runner 

<br>

>>**Containerized Environment**

We can containerize the developement env in docker file and then push this container to container registery in cloud.

We can use below code while running the pipeline. This will control our required env. (Like we do in airflow python virtual env)

```bash
python my-pipeline.py \
--worker_harness_container_image = $IMAGE_URI
```

<br>

>> **Cross Language transform** 

We can use java for i/o connectors then python for tensorflow operations.

Apache python SDK start local java service to ingect dependecies for java pipeline.

<br>


>> **Dataflow Shuffle Service**  (batch processing)
 
Utlized for agg operation like groupby.

Dataflow runner convert code in form job which will exceuated on worker node which will have it own persistant disk.

Currently dataflow shuffle execution happens on worker node using its disk memory and CPU.

Dataflow shuffle available only for batch processing.

**Legacy shuffle** operation happens on the worker VMs. The data is written to the disk of the worker VMs and shuffled among the workers over the network.\
**Dataflow Shuffle Service** offloads the shuffle operations to Google Cloud's distributed, managed shuffle service. The shuffle data is stored and managed independently from the VMs in Google's cloud infrastructure.


<br>

>> **Dataflow Excecuation** 

In essence:

The runner converts your Beam pipeline into something the platform (like Dataflow) can execute.

The Dataflow service backend is the overall manager that schedules tasks, provisions worker nodes, scales resources, and monitors job execution.

The worker nodes are the VMs that actually execute the transformed tasks distributed by the Dataflow service backend.

<br>


>> **Dataflow streaming Engine**.

streaming engine offloads the window state storage from the persistent disks attached to worker VMs to a back-end service.

It also implements an efficient shuffle for streaming cases.

<br>

>> **Flexible resource scheduling**

Cost saving for batch pipeline
* Advanced scheduling 
* Dataflow shuffle service 
* Mix of preemptible and standard VMs 

<br>

>> **IAM : Identity and Access Management**

If you want a user or group to be able to only view Dataflow jobs, assign them the **Dataflow viewer role.**

If a user only has the Dataflow **developer role**, they can view and cancel jobs that are currently running, but they **cannot** **create** jobs because the role does not have permissions to stage the files and view the Compute Engine quota.

The **Dataflow admin** role allows a user or group to interact with Dataflow and stage files in an existing Cloud storage bucket and view the Compute Engine quota.


Dataflow uses the **Dataflow service account** to interact between your project and Dataflow. \
For example, to check project quota, to create worker instances on your behalf, and to manage the job during job execution.

The **controller service account** is assigned to the Compute Engine VMs to run your Dataflow pipeline.

<br>

>> **Quotas** [revisit]

>>>Per region per project

CPU Quota :
Number of Inuse IP address : 
Persistant disk quota :SSD and HD

Streaming pipeline : max number of workers flag required. 
streaming engine pipeline : max number flag is not required 


### **DATA SECURITY**

>> **Data Locality** 

Data locality ensures that all data and metadata stay in one region.

There are regular health checks from the workers, workers requesting a work item and the regional endpoint responding with a work item, the worker item status, and autoscaling events.

Unexpected events are also transferred to the regional endpoint.\
For example, unhandled exceptions in user code, jobs that fail to launch because of permissions, worker item failures, and errors from another related system, such as Compute Engine.

Specify a regional endpoint to 
1.  minimize network latency and network transport costs.
2.  support your project's security and compliance needs.

>> **Shared VPC** 

Dataflow can run in networks that are either in the same project or in a separate project which we call the host project.

When a network exists in a host (Mother) project, we call the networking setup shared VPC.

Shared VPC allows organization admins to give certain people permission to manage and control specific parts of the network. 

>> **Private IP's**

This blocks the workers from accessing the internet, thus securing your data processing infrastructure.

By not using public IP addresses for your Dataflow workers, you also lower the number of public IP addresses you consume against your in-use IP address quota.

When you turn off public IP ad  dresses, the Dataflow pipeline can access resources only in the following places:

1.  Another instance in the same VPC network
2.  Shared VPC network
3.  network with VPC network peering enabled.

The flag, no_use_public_ips, lets Dataflow know that you want to launch the workers with internal IP addresses only.

>> **CMEK**

CMEK stands for customer managed encryption key.

During a Dataflow job's lifecycle, different storage locations are used to store data.

When a Dataflow job is created, a cloud storage bucket is used to store binary files containing pipeline code.

A cloud storage bucket is also used to temporarily store export or import data.

While the job is running, persistent disks attached to Dataflow workers are used for persistent disk-based shuffle and streaming state storage.

If a batch job is using Dataflow Shuffle, the backend stores the batch pipeline state during execution.

If a job is using Dataflow Streaming Engine, the backend stores the streaming pipeline state during execution.


By default, when data is stored in any of these locations, a Google-managed key is used to encrypt the data.


CMEK allows you to encrypt data at rest using one of your symmetric keys stored in Google Cloud key management system.