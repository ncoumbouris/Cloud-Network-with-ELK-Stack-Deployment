# Cloud-Network-with-ELK-Stack-Deployment and DVWA
Azure Cloud was used to create a virtual network with an ELK Stack Server that remotely monitors two servers running an instance of DVWA.


## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

"Images/AzureCloud-ELK-Stack-Deployment-Network.png"

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the "playbook" file may be used to install only certain pieces of it, such as Filebeat.

	ansible.cfg
	ansible_setup.yml
	install_elk.yml
	filebeat-config.yml
	filebeat-playbook.yml
	metricbeat-config.yml
	metricbeat-playbook.yml

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly AVAILABLE, in addition to restricting traffic to the network.

-Load Balancers protect availability by monitoring the amount of requests to the backend machines. If the number of requests is greater than the machine can process, the load balancer will redirect the traffic to an identical machine where the same request can be fulfilled, thus preventing network downtime.

-The advantage of a jump box is that it is identical to a gateway router. The jump box is exposed to the public internet and sits in front of other machines on the network which are not exposed. By implementing security configurations on the jump box, we can protect the entire network from a single point since all traffic will be passing through the publicly exposed jump box.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the logs and system traffic.

-Filebeat is a lightweight shipper, part of the ElasticStack which allows for the management of system logs/files by "forwarding and centralizing" log data.

-Metricbeat is a lightweight shipper, part of the ElasticStack which allows for the monitoring of system metrics. This monitoring will proide information on system-level CPU usage, memory, filesystem and statistics for all processes running on your systems.

The configuration details of each machine may be found below.


| Name      | Function                    | IP Address | Operating System     |
|-----------|-----------------------------|------------|----------------------|
| JumpBoxVM | VM & Gateway                | 10.0.0.4   | Linux (Ubuntu 18.04) |
| Web-1     | Web Server 1 - Running DVWA | 10.0.0.5   | Linux (Ubuntu 18.04) |
| Web-2     | Web Server 2 - Running DVWA | 10.0.0.6   | Linux (Ubuntu 18.04) |
| UbuntuVM  | VM Running ELK Stack Server | 10.1.0.4   | Linux (Ubuntu 18.04) |


### Access Policies

The machines on the internal network are not exposed to the public Internet. 

-Only the "JumpBoxVM" and "UbuntuVM" machines can accept connections from the Internet but access to these machine are only allowed from the following IP addresses:

"98.7.24.216" 
NOTE: This is the only IP address permitted to access these machines.

Machines within the network can only be accessed by the "JumpBoxVM".

The ELK VM "UbuntuVM" machine can be accessed by the "JumpBoxVM" (10.0.0.4) and the only whitelisted IP address "98.7.24.216"
The ELK VM is also accessed by "Web-1" (10.0.0.5) and "Web-2" (10.0.0.6) because these two webservers hosting "DVWA" send data to the ELK VM collected through filebeat and metricbeat for analysis and monitoring by the ELK VM "UbuntuVM".

A summary of the access policies in place can be found in the table below.
Note: The machines publicly accessible to the internet can only be accessed from one whitelisted IP

| Name      | Publicly Accessible | Allowed IP Addresses                         |
|-----------|---------------------|----------------------------------------------|
| JumpBoxVM | Yes                 | 98.7.24.216                                  |
| Web-1     | No                  | 10.0.0.4 / 10.1.0.4                          |
| Web-2     | No                  | 10.0.0.4 / 10.1.0.4                          |
| UbuntuVM  | Yes                 | 98.7.24.216 / 10.0.0.4 / 10.0.0.5 / 10.0.0.6 |


### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because of the following:

Ansible allows a single machine or multiple machines to be configured effortlessly by running a single command. While Ansible may be not necessary if you are only configuring one machine, the appreciation for Ansible becomes apparent for a company requiring tens or hundreds of machines to operate. Such a company would suffer in the time it takes to configure each machine manually.

The playbook implements the following tasks:
The steps of the ELK installation play are as follows (To be performed within the Ansible container of JumpBoxVM):
- Configure the Ansible's hosts file to include a new group [elk] and list the IP of the new VM hosting the elk server under that group
- Create an Ansible playbook which will Download and install the Docker engine and Elk image/container, and specify configurations
- Launch and expose the container by executing the newly created Ansible playbook
- Confirm Elk installed successfully via ssh connection into the VM hosting ELK and confirming the ELK image/container is up and running

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

"Images/docker_ps_output.png"


### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- "Web-1"
- "Web-2"

We have installed the following Beats on these machines:
- filebeat
- metricbeat

These Beats allow us to collect the following information from each machine:
- Filebeat will collect system logs which can be used to keep track of what applications have been used.
- Metricbeat will collect system metrics such as CPU usage, memory, and data related to processes running on the system. Monitoring these metrics can help to improve system availability and reliability


### Using the Playbook

In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps
- Copy the Playbook file from within '/etc/ansible' of the Ansible control node to the '/etc/filebeat' and '/etc/metricbeat' folders within the Destination VM's ("Web-1" and "Web-2")
- Update the Ansible "Hosts" file to include the private IP address of "Web-1" and "Web-2" under the webservers group
- Run the playbook, and ssh into the "Web-1" and "Web-2" machines to verify that "filebeat" and "metricbeat" packages were installed.
- The playbook is a .YML (YAML) file. It is copied from the Ansible node within the "JumpBoxVM" machine onto the destination machine.
- The Ansible Hosts file is updated to include the specific machine you want the playbook to run on. You will specify the machines by their private IP addresses within the network.
- The ELK server will run on the "UbuntuVM" machine at '10.1.0.4' and "Filebeat" will be runniing on the web servers ("Web-1" and "Web-2") machines at "10.0.0.5" and "10.0.0.6"
- Which URL do you navigate to in order to check that the ELK server is running?
The following URL will be navigated to in order to check the ELK server is running:
- http://localhost:5601/app/kibana (where localhost is the public IP address of the "UbuntuVM" machine and "5601" is the port used by "Kibana".

