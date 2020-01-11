name: nomad-chapter-5-title
class: title, shelf, no-footer, fullbleed
background-image: url(https://hashicorp.github.io/field-workshops-assets/assets/bkgs/HashiCorp-Title-bkg.jpeg)
count: false

# Chapter 5
## Nomad Job Specification

![:scale 15%](https://hashicorp.github.io/field-workshops-assets/assets/logos/logo_nomad.png)

???
* In this chapter, we'll discuss basic Nomad job specification.

---
layout: true

.footer[
- Copyright © 2020 HashiCorp
- ![:scale 100%](https://hashicorp.github.io/field-workshops-assets/assets/logos/HashiCorp_Icon_Black.svg)
]

---
name: chapter-5-topics
# Chapter 5 Topics

1. Declaring Jobs and Their Target Datacenters
2. Declaring Tasks, Task Groups, and Task Drivers
3. Specifying Required Resources and Networks
4. Registering Tasks as Consul Services

???
* This is our chapter topics slide.

---
class: compact, col-2
name: Declaring Jobs and Their Target Datacenters
# Declaring Jobs and Their Target Datacenters

.smaller[
- [Nomad Jobs](https://www.nomadproject.io/docs/job-specification) are the primary configuration that users interact with when using Nomad.
- Nomad models infrastructure as regions and datacenters. 
- Regions may contain multiple datacenters. 
- Servers are assigned to a specific region, managing state and making scheduling decisions within that region. 
]
<br>
```go
*job "example" {
* datacenters = ["dc1"]
  group "cache" {
    task "redis" {
      driver = "docker"
      config {
      }
    }
  }
}
```

???
* Simple redis job definition
---
class: compact, col-2
name: Declaring Tasks, Task Groups, and Task Drivers
### Declaring Task Groups

.smaller[
- The [group stanza](https://www.nomadproject.io/docs/job-specification/group.html) defines a series of tasks that should be co-located on the same Nomad client.
- Any task within a group will be placed on the same client.
]
<br>
<br>
<br>
<br>
<br>
```go
job "example" {
  datacenters = ["dc1"]
*  group "cache" {
*    task "redis-1" {
*     driver = "docker"
*     config {
*     }
*    task "redis-2" {
*     driver = "docker"
*     config {
*     }
*   }
  }
}
```

???
* expanding the redis example with groups
---
class: compact, col-2
name: Declaring Tasks, Task Groups, and Task Drivers
### Declaring Tasks
.smaller[
- The [task stanza](https://www.nomadproject.io/docs/job-specification/task.html) creates an individual unit of work, such as a Docker container, web application, or batch processing.
- Tasks can be short-lived batch jobs or long-running services such as a web application, database server, or API.
]
<br>
```go
job "example" {
  datacenters = ["dc1"]
   group "cache" {
*    task "redis" {
*      driver = "docker"
*     config {
*     }
    }
  }
}
```

???
* same redis example

---
class: compact, col-2
name: Declaring Tasks, Task Groups, and Task Drivers
### Declaring Task Drivers
.smaller[
-   [Task drivers](https://www.nomadproject.io/docs/drivers) are used by Nomad clients to execute a task and provide resource isolation.
    -  Task driver resource isolation is intended to provide a degree of separation of Nomad client CPU / memory / storage between tasks.
    -  Resource isolation effectiveness is dependent upon individual task driver implementations and underlying client operating systems.
]
<br>
```go
job "example" {
  datacenters = ["dc1"]
   group "cache" {
     task "redis-1" {
*     driver = "docker"
      config {
      }
     task "redis-2" {
*     driver = "docker"
      config {
      }
    }
  }
}
```

???
* task driver explained

---
class: compact, col-2
name: Declaring Tasks, Task Groups, and Task Drivers
### HashiCorp Task Drivers
.smaller[
- There are 5 HashiCorp developed and maintained Task Drivers
  - [Docker Driver](https://www.nomadproject.io/docs/drivers/docker.html)
  - [Isolated Fork/Exec Driver](https://www.nomadproject.io/docs/drivers/exec.html)
  - [Java Driver](https://www.nomadproject.io/docs/drivers/java.html)
  - [QEMU Driver](https://www.nomadproject.io/docs/drivers/qemu.html)
  - [Raw Fork/Exec Driver](https://www.nomadproject.io/docs/drivers/raw_exec.html)
<br>

###  Community Task Drivers
- There are 5 OSS Community supported Task Drivers
  - [LXC Driver Driver](https://www.nomadproject.io/docs/drivers/external/lxc.html)
  - [Singularity Driver](https://www.nomadproject.io/docs/drivers/external/singularity.html)
  - [Jail Task Driver](https://www.nomadproject.io/docs/drivers/external/jail-task-driver.html)
  - [Pot Task Driver](https://www.nomadproject.io/docs/drivers/external/pot.html)
  - [Firecracker Task Driver](https://www.nomadproject.io/docs/drivers/external/firecracker-task-driver.html)
]

???
* showing all the drivers except depricated drivers

---
class: compact, col-2
name: Specifying Required Resources and Networks
# Specifying Required Resources
- The [resources stanza](https://www.nomadproject.io/docs/job-specification/resources.html) describes the requirements a task needs to execute. 
  - Resource requirements include memory, network, CPU, and device.

<br>
<br>

```go
job "example" {
  datacenters = ["dc1"]
  group "cache" {
    task "redis" {
      driver = "docker"
*     resources {
*       cpu    = 500
*       memory = 256
*       network {
          mbits = 100
          port  "db"  {}
        }
      }
    }
```

???
* expanding the redis example with resources

---
class: compact, col-2
name: Specifying Required Resources and Networks
# Specifying Networking
- The [network stanza](https://www.nomadproject.io/docs/job-specification/network.html) specifies the networking requirements for the task. 
  - Bandwidth
  - Port allocations

<br>
<br>
<br>

```go
job "example" {
  datacenters = ["dc1"]
  group "cache" {
    task "redis" {
      driver = "docker"
      resources {
        cpu    = 500
        memory = 256
*       network {
*         mbits = 100
*         port  "db"  {}
*       }
      }
    }
```

???
* expanding the redis example with networking

---
class: compact, col-2
name: Specifying Required Resources and Networks 
# Specifying Port Mapping
.smaller[
- Nomad supports [Dynamic Ports](https://www.nomadproject.io/docs/job-specification/network.html#dynamic-ports), [Static Ports](https://www.nomadproject.io/docs/job-specification/network.html#static-ports), and [Mapped Ports](https://www.nomadproject.io/docs/job-specification/network.html#mapped-ports).
]
    In this example for the Docker driver, the service is listening on port 6379 inside the Redis container. The driver will automatically map the dynamic port to this service.
<br>
<br>
<br>
<br>
<br>
<br>

```go
job "example" {
  datacenters = ["dc1"]
  group "cache" {
    task "redis" {
        driver = "docker"
*       config {
*           port_map = {
*           db = 6379
*           }
*       }
*       resources {
*           network {
*           port "db" {}
*           }
        # ...
```

???
* port mapping

---
class: compact, col-2
name: Specifying Required Resources and Networks
# Specifying Bridged Networks
.smaller[
-  When the network stanza is defined at the group level with [bridge as the networking mode](https://www.nomadproject.io/docs/job-specification/network.html#bridge-mode), all tasks in the task group share the same network namespace.
-  Tasks running within a network namespace are not visible to applications outside the namespace on the same host.
]

<br>
<br>
<br>

```go
job "example" {
  datacenters = ["dc1"]
  group "cache" {
    task "redis" {
      driver = "docker"
      resources {
        cpu    = 500
        memory = 256
        network {
*       mode = "bridge"
*           port "http" {
*           static = 9002
*           to     = 9002
*           }
        }
```

???
* good time to throw in consul ;)

---
class: compact, col-2
name: Job Types and Schedulers
# Nomad Job Types and Schedulers
.smaller[
- The [type stanza](https://www.nomadproject.io/docs/job-specification/job.html#type) specifies the Nomad scheduler to use. 
 - Nomad has three scheduler types that can be used when creating your job: [service](https://www.nomadproject.io/docs/schedulers.html#service),  [batch](https://www.nomadproject.io/docs/schedulers.html#batch) and [system](https://www.nomadproject.io/docs/schedulers.html#system) schedulers.
] 

<br>

```go
job "example" {
  datacenters = ["dc1"]
* type = "system"
```

---
class: compact, col-2
name: Job Types and Schedulers
# Service Scheduler
.smaller[
- The [service scheduler](https://www.nomadproject.io/docs/schedulers.html#service) is designed for scheduling long lived services that should never go down
- Service jobs are intended to run until explicitly stopped by an operator. 
- If a service task exits it is considered a failure and handled according to the job's restart and reschedule stanzas.
] 


```go
job "example" {
  datacenters = ["dc1"]
* type = "service"
  group "cache" {
    task "redis" {
      driver = "docker"
      # ...
      # ...
```

---
class: compact, col-2
name: Job Types and Schedulers
# Batch Scheduler
.smaller[
  - [Batch jobs](https://www.nomadproject.io/docs/schedulers.html#batch) are short lived, usually finishing in a few minutes to a few days, and much less sensitive to short term performance fluctuations.
  - Batch jobs are intended to run until they exit successfully. 
  - Batch tasks that exit with an error are handled according to the job's restart and reschedule stanzas.
] 


```go
job "example" {
  datacenters = ["dc1"]
* type = "batch"
  group "cache" {
    task "redis" {
      driver = "docker"
      # ...
      # ...
```

---
class: compact
name: Job Types and Schedulers
# System Scheduler
.smaller[
- The [system scheduler](https://www.nomadproject.io/docs/schedulers.html#system) is used to register jobs that should be run on all clients that meet the job's constraints. 
- The system scheduler is also invoked when clients join the cluster or transition into the ready state. 
  - This means that all registered system jobs will be re-evaluated and their tasks will be placed on the newly available nodes if the constraints are met.
- This scheduler type is extremely useful for deploying and managing tasks that should be present on **every node** in the cluster.
- Systems jobs are intended to run until explicitly stopped either by an operator or preemption. 
- If a system task exits it is considered a failure and handled according to the job's restart stanza; *system jobs do not have rescheduling*.
] 

---
class: compact, col-2
name: Registering Tasks as Consul Services
# Registering Tasks as Consul Services
.smaller[
- The [service stanza](https://www.nomadproject.io/docs/job-specification/service.html) instructs Nomad to register the service with Consul for Service Discovery.
- We can define tags, health checks, ports, and [several other attributes](https://www.nomadproject.io/docs/job-specification/service.html#service-parameters).
]
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
```go
job "example" {
  datacenters = ["dc1"]
  group "cache" {
    task "redis" {
*     service {
*       tags = ["leader", "redis"]
*       port = "db"
*         check {
*           type     = "tcp"
*           port     = "db"
*           interval = "10s"
*           timeout  = "2s"
*         }
        # ...
```

???
* for sure now talk about consul