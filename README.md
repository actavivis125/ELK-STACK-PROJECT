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

In addition to the above, Azure has provisioned a load balancer in front of all machines except for the jump box. The load balancer's targets are organized into the following availability zones:

Availability Zone 1: Web-1 + Web-2 Availability Zone 2: ELK




# ELK Configuration

The ELK VM exposes an Elastic Stack instance. Docker is used to download and manage an ELK container.

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because this allows for easy replication and portability.

The Ansible playbook implements the following tasks: Installing Docker, Installing pip3, Installing the Docker Python Module, Setting the system memory necessary for this machine to work properly, and Downloading and launching a Docker ELK container.

The Ansible playbook can be found in the YAML file elk.yml in this repo, and is also included here for convenience.

ELK configuration with .yml file:

```
---
- name: Configure Elk VM with Docker
  hosts: elk
  remote_user: azureuser
  become: true
  tasks:
    # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        force_apt_get: yes
        name: docker.io
        state: present

      # Use apt module
    - name: Install python3-pip
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

      # Use pip module (It will default to pip3)
    - name: Install Docker module
      pip:
        name: docker
        state: present

      # Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144

      # Use sysctl module
    - name: use sysctl to increase memory VM
      sysctl:
        name: vm.max_map_count
        value: "262144"
        reload: yes

      # Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        # Please list the ports that ELK runs on
        published_ports:
          -  9200:9200
          -  5044:5044
          -  5601:5601

> 
```

This screenshot displays the result of running `docker ps` after successfully configuring the ELK instance:
<img width="1414" alt="Screen Shot 2020-10-23 at 18 27 44" src="https://user-images.githubusercontent.com/73254593/97064798-91f7a580-155d-11eb-8e15-fad2dfcdd380.png">


## Target Machines and Beats.


In order to use the playbook, you will need to have an Ansible control node already configured. After you have such a control node set up, we will need to perform the following steps: -Copy the playbooks to the Ansible Control Node -Run each playbook on the appropriate targets

SSH into the control node (Docker Anisble container on the JumpBox) and follow the steps below:

The easiest way to copy the playbooks is to use Git:
```
$ cd /etc/ansible
$ mkdir files
$ git clone https://github.com/actavivis125/elk-stack-project.git
```

#Move Playbooks and hosts file Into `/etc/ansible`

```
$ cp elk-stack-project/playbooks/* .
$ cp elk-stack-project/files/* ./files
```

After this we run the Ansible playbook:

```
$ cd /etc/ansible
$ ansible-playbook elk.yml
$ ansible-playbook filebeat-playbook.yml webservers
$ ansible-playbook metricbeat-playbook.yml webservers
```

To verify success, wait five minutes to give ELK time to start up.

Then, navigate to the public IP of your ELK Server machine using port 5601. An example would be entering 0.0.0.0:5601 in your web browser. This should take you to the Kibana home screen.

To ensure filebeat is working properly, from your ELK Server home page (Kibana) click on Add Log Data then choose System Logs. Then you simply have to scroll to the page bottom and click Check Data. If all is well, it will display "Data successfully received from this module"

If this is not the case, you likely need to add a filebeat configuration file and add your ELK Server IP address in this config.

From back on your Ansible container on the JumpBox:




