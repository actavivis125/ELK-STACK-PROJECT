# Introduction.

This is a project I worked on in the cybersecurity bootcamp where several resources were used to deploy an ELK Stack (Elasticsearch, Mietricbeat, Filebeat, and Kibana). This Stack's intended function is to be a monitoring server to aggregate logs from two web servers that have DVWA (Damn Vulnerable Web Application) installed on them. I utilized Docker and Ansible to install the DVWA and ELK Stack container images on the VMs. I will provide more details for this project within this repository.



## Topology

Elk-Stack Deployment Automation.
I used files in this repisotory to configure the network depicted below:


![ELK](https://user-images.githubusercontent.com/73254593/97062762-358e8900-1551-11eb-9def-432dda6d2302.png)






These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the yml file(s) may be used to install only certain pieces of it, such as Metricbeat or Filebeat.

This document contains the following details: Description of the Topology, Access Policies, ELK Configuration, Beats in Use, Machines Being Monitored, and How to Use the Ansible and Container Build.





### Topology Description:

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA (the Damn Vulnerable Web Application).

Load balancing ensures the application will be highly available, while restricting inbound access to the network. The load balancer ensures that resources required to process incoming traffic will be shared by both vulnerable web servers. Access controls will ensure that only authorized users will be able to connect.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the file system.

#### Filebeat:
-Filebeat monitors the log files or locations specified, collects log events, and forwards them for indexing.

##### Metricbeat:
-Metricbeat records system and application metrics. It allows for viewing cpu,memory,disk, and network metrics, in addition to also allowing monitoring for apache, docker, and other applications installed.

The configuration details of each machine may be found below. 

| Name                	| Function   	| IP Address  	| Operating System 	|
|---------------------	|------------	|-------------	|------------------	|
| JumpBox Provisioner 	| Gateway    	| 10.0.0.7    	| Linux            	|
| DVWA 1              	| Web Server 	| 10.0.0.12   	| Linux            	|
| DVWA 2              	| Web Server 	| 10.0.0.13   	| Linux            	|
| ELK                 	| Monitoring 	| 10.2.0.5    	| Linux            	|


## Access Policies

The machines on the internal network are not exposed to the public Internet.

Only the JumpBox machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses: My public IP

Machines within the network can only be accessed by each other. The DVWA machines (Web-1 and Web-2) can send traffic to the ELK-Server

A summary of the access policies in place can be found in the table below:



| Name       	| Public Access 	| Allowed IPs' 	|
|------------	|---------------	|--------------	|
| JumpBox    	| Yes           	| My Public IP 	|
| ELK-server 	| No            	| 10.2.0.1-16  	|
| DVWA 1     	| No            	| 10.0.0.1-16  	|
| DVWA 2     	| No            	| 10.0.0.1-16  	|



