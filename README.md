## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![Network Diagram](https://github.com/Sprattles/JackieS-CyberSec-Project1/blob/main/Diagrams/Network_Diagram_incl_ELK.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the YAML files may be used to install only certain pieces of it, such as Filebeat.

  - [Ansible Playbook files](https://github.com/Sprattles/JackieS-CyberSec-Project1/tree/main/Ansible) 

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in use
  - Machines being monitored
- How to use the ansible build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting access to the network.
Load balancers distribute traffic evenly across servers and play a crucial role in mitigating DDoS attacks.  They are typically set up with a health probe function that checks the machines behind it are 'healthy' before sending traffic to them.

A jump box provisioner is used to control and maintain the webserver virtual machines on the network from one location.  An advantage of this jump box is that it is only accessible by an administrator from a specified IP address outlined in the Network Security Rules.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the logs and system files.

There are two beats used in this setup;
 - Filebeat: watches for and logs information about the system, recording which files have changed and when
 - Metricbeat: collects the metrics from the system such as CPU and Memory usages.

The configuration details of each machine may be found below.
| Name    | Function  | IP Address                | Operating System     |
|---------|-----------|---------------------------|----------------------|
| JumpBox | Gateway   | 20.37.247.61 / 10.1.0.7   | Linux (Ubuntu 18.04) |
| Web-1   | Webserver | 10.1.0.8                  | Linux (Ubuntu 18.04) |
| Web-2   | Webserver | 10.1.0.9                  | Linux (Ubuntu 18.04) |
| Web-3   | Webserver | 10.1.0.10                 | Linux (Ubuntu 18.04) |
| ELK-VM  | Elk       | 52.189.227.118 / 10.0.0.4 | Linux (Ubuntu 18.04) |


### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the jump box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
 - My public IP address on my local machine

Machines within the network can only be accessed by SSH connections on port 22 from the jump box.

A summary of the access policies in place can be found in the table below.

| Name    | Publicly Accessible | Allowed IP Addresses                                                     |
|---------|---------------------|--------------------------------------------------------------------------|
| Jumpbox |          No         | My public IP address on local machine                                    |
| Web-1   |         Yes         | 20.36.42.110 (load balancer)                                             |
| Web-2   |         Yes         | 20.36.42.110 (load balancer)                                             |
| Web-3   |         Yes         | 20.36.42.110 (load balancer)                                             |
| ELK-VM  |          No         | 10.1.0.7 (Jumpbox) & My public IP address on local machine via port 5601 |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because it saves time and ensures consistency across installations reducing the chance of errors.

The playbook implements the following tasks:
- Installs docker.io
- Installs python-pip
- Installs docker module
- Increases and uses more virtual memory
- Downloads and launches a docker elk container on published ports: 5601, 9200, 5044

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![docker ps](https://github.com/Sprattles/JackieS-CyberSec-Project1/blob/main/Images/docker_ps_ELK.png )

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
| Machine name | Private IP address |
|--------------|--------------------|
| Web-1        | 10.1.0.8           |
| Web-2        | 10.1.0.9           |
| Web-3        | 10.1.0.9           |

We have installed the following Beats on these machines:
- [Filebeat](https://github.com/Sprattles/JackieS-CyberSec-Project1/blob/main/Ansible/filebeat-playbook.yml)
- [Metricbeat](https://github.com/Sprattles/JackieS-CyberSec-Project1/blob/main/Ansible/metricbeat-playbook.yml)

These Beats allow us to collect the following information from each machine:
- Filebeat: collects data from various logs to report when and where file changes happen.  This example here is when filebeat was first installed on the webserver machines showing the systemd logs;
![filebeat](https://github.com/Sprattles/JackieS-CyberSec-Project1/blob/main/Images/Filebeat_example.png)
- Metricbeat: collects statistical data from the system and services running on the webserver machines such as CPU and Network usage.  In this example below we can see the Network input and output in bytes at time of installation;
![metricbeat](https://github.com/Sprattles/JackieS-CyberSec-Project1/blob/main/Images/Metricbeat_example.png)

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the required YAML file to `/etc/ansible/`
- Update the [hosts](https://github.com/Sprattles/JackieS-CyberSec-Project1/blob/main/Ansible/hosts) file to include the IPs of the webservers and ELK machine.  This is so that the playbook knows which host to install the required playbook on.  i.e. you install the ELK server on the [elk] host and filebeat/metricbeat on the [webservers].
- Run the playbook with `ansible-playbook *playbook_file*.yml`, and navigate to http://*yourELKVM.IP*:5601/app/kibana to check that the installation worked as expected. 

### Commands to install ELK, filebeat and metricbeat
note: users and IP addresses listed below are for this specific project, so will be different for a new virtual network setup.

#### Install ELK onto ELK-VM
>SSH into jumpbox (from local machine): 
> `ssh azadmin@20.37.247.61`
>
>list docker containers:
> `sudo docker container list -a`
>
>start docker container
>`sudo docker container start compassionate_leakey`
>
>attack docker container
>`sudo docker container attach compassionate_leakey`
>
>Add webservers and ELK machines to hosts file
>`nano /etc/ansible/hosts`
>- eg for this project it looked like;
>> [webservers]                                                                              
10.1.0.8 ansible_python_interpreter=/usr/bin/python3                                                                                 
10.1.0.9 ansible_python_interpreter=/usr/bin/python3                                                                                  
10.1.0.10 ansible_python_interpreter=/usr/bin/python3
[elk] 
10.1.0.10 ansible_python_interpreter=/usr/bin/python3
>run install-elk.yml playbook
>`ansible-playbook /etc/ansible/install-elk.yml`
>
>go to http://*yourELKVM.IP*:5601/app/kibana to check the ELK installation worked

#### Install filebeat & metricbeat onto webservers
Within your ansible container;
>copy filebeat and metricbeat config files to /etc/ansible/files
>`