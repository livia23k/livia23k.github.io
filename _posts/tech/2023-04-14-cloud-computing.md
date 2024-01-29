---
title: Introduction to Cloud Computer
categories: [Computer Science, Cloud]
img_path: /assets/img/post/2023-04-14-introcc
tags: [tech, cloud]
---

## What?

### Defination

Cloud Computing is the delivery of on-demand computing resources over the internet on a pay-as-you-go basis.

### Background

on-premise -> cloud computing

🟩Pros: lower cost, relieve some responsibility of developers, more flexible & rapid, higher availability, resource-saving, unlimited storage, ... 

🟥Cons: less secure, more depends on the network, ... 

### Essential Characters

1. on-demand self-service                  (without help, use as you need)
2. broad network access                    (through many mechanisms and platforms)
3. resource pooling                           (resources assigned by need)
4. rapid elasticity                               (whenever)
5. measured service                          (monitored, pay as you used)

--- 

## What to provision - Components
### Overview

```
**Region** (where the infrastructure cluster is geogrphically located)
	**Availability Zones** (have its own resources)         - enhence stability
		**Cloud Data Center**
			provides **Computing Resources**           
				**Infrastructure** - Services 
				->                 1 Virtual Servers (software based),
				->                 2 Bare Metal Servers (physical servers),
				->                 3 "Serverless" (abstraction).
				**Storage**
				->                 Concerns: speed, importance, frequency, security
				->                 Service Options: 0 Local Drive (Default),
				->                                        1 Block Storage,
				->                                        2 File Storage,
				->                                        3 Object Storage (most common, highly distributed and resilient).
				**Networking** {routers, switches}
				->                 Network interfaces {public, private}
				->                 ->                 IP addresses, subnets
				->                 Access authority
				->                 ->                 Security Groups, ACLs, VLANs, VPCs, VPNs
				->                 Traditional hardware appliance
				->                 ->                 firewalls, load balancers, gateways, traffic analyzers
				->                 Other networking capability provided by Cloud Provider
				->                 ->                 CDNs
```

### Infrasturcture

#### Overview

![](20230414110336810.png){: w="450"}


#### Bare Metal Machines

🟩Pros: 
1. work best for CPU and I/O intensive workloads

2. highest performance and security

3. satisfy strict compliance requirements

4. complete flexibility, control and transpency

🟥Cons:

1. come with added management and operational overhead

#### Virtual Machines

🟩Pros: 
1. cost-saving
2. agility+speed
3. portable, provide an elastic & scalable environment

#### Containers

executable units of software (composed of application code, libs, and dependencies)
could run free from guest OS

🟩Pros: 
1. resource-saving (spacing, computing...)
2. high protability

### Storage
#### Overview
should be accessed through the compute nodes

|                            | defination                                                                 | speed   | *                                                                         |
| -------------------------- | -------------------------------------------------------------------------- | ------- | ------------------------------------------------------------------------- |
| Local Storage              | storage linked directly to the cloud-based server                          | fast    | ephemeral                                                                 |
| File Storage (NFS Storage) | connected to compute nodes, over ethernet network                          | slow    |                                                                           |
| Block Storage              | attached to 1 compute node as 'volumes', over high-speed fibre connections | fast    |                                                                           |
| Object Storage             | not attached to compute nodes, accessed via an API                         | slowest | 'infinite' in size; most resilient to failure; used to store static files | 

If the transmission medium is fast enough, maybe we should evaluate the speed by IOPS (Input/Output Operations Per Second), how quickly data can be read from or written to the storage.

For Block Storage and File Storage, we can use Snapshot, a point in time image of the storage, to backup data. (is metadata instead of data)


#### CDN - Content Delivery Network
create copy if content in different geographical location, to increase access speed, security, uptime, and decrease load for servers


### Networking
#### Background

| on-premise networking | networking in cloud      | 
| --------------------- | ------------------------ |
| physical devices      | logical instances        |
| physical equipment    | use network as a service |

#### Networking Virtualization

![](20230414221814.png){: w="450"}

### Compute Resources
null

--- 

## Other Concerns

### Security

Cloud Security is a shared responsibility of users and cloud providers.

**Potential Risks**

| From perspective of |                                                 |
| ------------------- | ----------------------------------------------- |
| data                | at rest, in motion, in use                      |
| app                 | manage access, app security, container security |
| users using app     | identity, network security                      | 

**Characteristics**
be embeded in the DevOps (a collaboration cycle, mentioned in 'Trends-DevOps') life cycle

**Security Development Cycle**

1. Design
2. Build
3. Manage Security
4. Back to step 1

**Methods**

{Identity and Access Management}

1. distinguish users role: admin, developer, app user;
2. divide users into acess group to reduce the duplication of authority assignments;
3. set access identification: API Key + Account Info;
4. use 'reporting' from Cloud to audit resources accessed by users

{Cloud Encryption}

scrambling data, make it illegible
tech: encryption & decryption
when? data at rest, in transit, in use
where? server & client side 

{Monitoring}

what for: easily analyzing, tracking, and managing cloud-based services and applications
tech: not just about automated tools, also about strategies, practices, and processes

--- 

## How to service users - Models
### Deployment Models
#### Overview
about where the physical infrastructure is, who own & manage it, and how they can be used by users

Models:
1. Public                         (owned by cloud provider, used by many people)
2. Private                       (provisioned for single organization)
3. Hybrid                        (mixed)


#### Public Cloud
resources are shared by public on an as-needed basis, and are not for a single tenant

🟩Pros: on-demand resources, highly reliable, economic of scale  
🟥Cons: security, data sovereignty compliance


#### Private Cloud
used by a single organization  
on premises / external infrastructure

🟩Pros: security


👉 **Virtual Private Cloud (VPC)**  

(a special kind of Private Cloud)

private cloud service offered by public cloud


#### Hybrid Cloud
connects an organizations' on-premise private cloud and third-party public cloud

🟩Pros:
flexibility, workloads move freely, choice of security & regulation features, can handle the "cloud bursting" by leveraging additional public cloud capacity, allow highly-sensitive workloads in private cloud while running less-sensitive workloads in public cloud

**Types**

1. Hybrid Monocloud (one cloud provider)
2. Hybrid Multicloud (choose any, move from one to another)
3. Composite Multicloud (across multi-provider)

### Service Models
about which cloud-based resources the user can rent and access from service providers

1. SaaS[^1] - Application
2. PaaS - Platform 
3. IaaS - Infrastructure        
[^1]: S = Software, aaS = as a Service

| Model | user manages                                | what cloud provider manages         | persona      |
| ----- | ------------------------------------------- | ----------------------------------- | ------------ |
| IaaS  | hypervisors & os, dev tools, app code, data | physical resources                  | Everyone     |
| PaaS  | app code, data                              | all above + platform infrasturcture | Developer    |
| SaaS  |                                             | all above + app + data              | System Admin |

**physical resources**: servers & storage, data centers, cooling, power, networking & security
**infrastucture**: os, dev tools, databases, business analytics

--- 

## Trends

### Application Modernization

|       | Trends                                                              |
| -------------- | ------------------------------------------------------------------- |
| Architecture   | Monoliths -> Service-oriented Architecture **->** Microservices |
| Infrasturcture | Physical Services -> VMs **->** Cloud                               |
| Delivery       | Waterfall -> Agile **->** DevOps                                    |

**->**：Application Modernization

### Hybrid Multi-Cloud
inteperate public, private, and on-premise services seamlessly

fit cases:
1. cloud scaling (tackle data burst)
2. modernization (mobile app)
3. DATA+AI

### Microservices
break the whole work into small ones, so everyone just need to finish their part of functions individually (functional programming)


### Serverless Computing
offload developers' responsibility for infrastructure management (e.g. scaling, scheduling, patching, provisioning)

code run as individual function, no prior execution context cuz the serverless platform provide 'translation'

fit cases:
1. short-running (usually in seconds and minutes)
2. seasonal workload
3. stateless microservices

challenge:
1. workload (not applicable for huge load)
2. latency (should start up from 0 to serve a new quest everytime)


### Cloud Native Application
an app comprising of several microservices working together as a whole

developed to work only in the cloud
standardize to satisfy with cloud native principles

fit for every cases


### DevOps
Dev -> Develop
Ops -> Operate

a collaborate approach

what for? produce software in short iterations on a continuous delivery, integration, deployment, monitoring, pipeline

--- 

## Extension

### Emerging Technologies

Internet of Things, Artificial Intelligence, Blockchain and Analytics

> **Relationship**
>
> Cloud - data resource
> IoT - behave
> AI - decision making
>
> Cloud - data resource
> ​Blockchain - provide trusted data
> ​Analytics - track trends



