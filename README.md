# ELK-Stack-Project
Project to configure an ELK stack server in order to set up a cloud monitoring system. 

## Network Diagram
Below is a graph illustrating the setup of the cloud infrastructure I created to host the DVWA site, plus the ELK VM I am using for monitoring the web VMs for security.
In the diagram you can see:

- The Azure Resource Group (called Queen-Cailla)
- The Queen-Cailla Virtual Network (with the IP range of 10.0.0.0/16)
- The 2 VMs I use to host the DVWA site, plus the load balancer to balance traffic between these 2 servers
- A jumpbox which contains Anbsible which I used to deploy docker and configure containers on the web VMs
- A Network Security Group into the Queen-Cailla network, only allowing ssh over port 20 and http requests over port 80
- Access to the Jumpbox is also only possible through ssh if the device has the ssh key

![alt text](https://github.com/CaillaRose/ELK-Stack-Project/blob/main/images/Week12.jpg?raw=true)

## Deployment
For this setup I used Azure Cloud to launch an implementation of the DVWA (Damn Vulnerable Web Application) and an ELK stack to monitor any attacks on that site. 
1. The first thing to do in Azure is to create a Resource Group that will contain everything in the cloud implementation, including the ELK stack. After that I created a virtual network (VNet) to connect evetything in the resource group. The IP range for our VNet was 10.0.0.0/16.
2. I then set up a Network Security Group. This allows you to manage security rules for users accessing the network.
3. I created some inbound security rules - the first was to deny all inbound HTTP traffic. This is because I haven't set up the ELK stack yet so I want to keep the servers secure while I finish implementing everything.
4.  Next I set up the Virtual Machines which will host the DVWA site. I set up 3 VMs - 1 JumpBox VM which will be used to configure the site VMs. Because this jumpbox gives access to all the servers, I used cryptographic SSH keys to allow access. These are generated with ssh-keygen and then are pasted into the Azure VM setup.

![alt_text](https://github.com/CaillaRose/ELK-Stack-Project/blob/main/images/jumpbox.jpg?raw=true)

5. I then created 2 more VMs in the same resource group and VNet. These will host the DVWA website - I use 2 to ensure redundancy in case 1 server goes down and also for load-balancing of incoming traffic. A load balancer is created between the 2 VMs to distribute traffic.
6. I then used Docker containers on all 3 VMs as a way to automate configuration with Ansible. Ansible is run on the docker container on the Jumpbox to then provision the Web VMs. Ansible uses playbooks to configure the other VMs. The playbook we used is [a link](https://github.com/CaillaRose/ELK-Stack-Project/blob/main/Ansible/elk_playbook.yml.txt). Once this is run the DVWA site should be accessible from a web browser:

![alt_text](https://github.com/CaillaRose/ELK-Stack-Project/blob/main/images/dvwa.png?raw=true)

## Access Policies/Network Addresses
Here is a summary of the final setup:

| Server | Function | IP Address | Operating System | Publicly Accessible | 
| ------ | -------- | ---------- | ---------------- | ------------------- | 
| Jumpbox | Configure/Access Web Servers | 10.0.0.4 | Linux | Only with SSH key |
| Web VM 1 | Host DVWA Site | 10.0.0.5 | Linux | No |
| Web VM 2 | Host DVWA Site | 10.0.0.6 | Linux | No |
| ELK Server | Monitor Traffic on Web Servers | 10.1.0.4 | Linux | Yes (Port 5601) |


## Monitoring
I installed Kibana, Metricbeat and Filebeat onto the ELK Server to monitor traffic on the 2 Webservers.
I used more YAML playbooks to configure these from the jumpbox:
-  [a link](https://github.com/CaillaRose/ELK-Stack-Project/blob/main/Ansible/filebeat-playbook.yml.txt)
-  [a link](https://github.com/CaillaRose/ELK-Stack-Project/blob/main/Ansible/metricbeat-playbook.yml.txt)

### Kibana
![alt_text](https://github.com/CaillaRose/ELK-Stack-Project/blob/main/images/kibana.PNG?raw=true)

### Metricbeat Dashboard
Metricbeat is a lightweight agent that can be installed on target servers to periodically collect metric data from your target servers, this could be operating system metrics such as CPU or memory or data related to services running on the server. 
![alt_text](https://github.com/CaillaRose/ELK-Stack-Project/blob/main/images/metricbeat.PNG?raw=true)

### Filebeat Dashboard
Filebeat, ships log files. In an ELK-based logging pipeline, Filebeat plays the role of the logging agent—installed on the machine generating the log files, tailing them, and forwarding the data to either Logstash for more advanced processing or directly into Elasticsearch for indexing.
![alt_text](https://github.com/CaillaRose/ELK-Stack-Project/blob/main/images/filebeat.PNG?raw=true)


