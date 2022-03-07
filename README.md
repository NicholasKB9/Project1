# Project1
Contents for Project 1, Cybersecurity Bootcamp 2022.
## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.
![image](https://user-images.githubusercontent.com/94084235/156966726-9e1a5c9f-0f6c-4e1a-b4e3-b986c96440e4.png)




These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the file may be used to install only certain pieces of it, such as Filebeat.

filebeat-playbook.yml

- name: installing and launching filebeat
  hosts: webservers
  become: yes
  tasks:

  - name: download filebeat deb
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.6.1-amd64.deb

  - name: install filebeat deb
    command: dpkg -i filebeat-7.6.1-amd64.deb

  - name: drop in filebeat.yml 
    copy:
      src: /etc/ansible/filebeat-config.yml
      dest: /etc/filebeat/filebeat.yml

  - name: enable and configure system module
    command: filebeat modules enable system

  - name: setup filebeat
    command: filebeat setup

  - name: start filebeat service
    command: service filebeat start

  - name: enable service filebeat on boot
    systemd:
      name: filebeat
      enabled: yes

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly accessible, in addition to restricting high volume to the network. Load balancers mitigate the possibility of network traffic overloading a server by distributing the traffic to different servers on the backend. 

The advantage of having a jump box is that it allows the user to have a central point on the network. Additionally, when setting up the network, the jump box served as an entrance point into the network when the web servers did not have an assigned public IP address when they were being built. 

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the data and system logs. On the ELK server, Filebeat and Metricbeat are installed. Filebeat monitors logs and collects log events. Metricbeat monitors servers by collecting metrics from the servers.

The configuration details of each machine may be found below.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.1.0.7   | Linux            |
| Web1     | Server   | 10.1.0.4   | Linux            |
| Web2     | Server   | 10.1.0.5   | Linux            |
| ELK.     | Server   | 10.0.0.4   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the load balancer machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
-	10.1.0.4
-	10.1.0.5
-	10.0.0.4
Machines within the network can only be accessed by SSH. I adjusted the network security rules to allow SSH into the aforementioned machines from my personal workstation at 24.30.49.60.

A summary of the access policies in place can be found in the table below:

| Name     | Publicly Accessible | Allowed IP Addresses     |
|----------|---------------------|--------------------------|
| Jump Box |Yes                  | 24.30.49.60  	    |
| Web1     |Yes                  | 20.124.213.1, 24.30.49.60|
| Web2     |Yes                  | 20.124.213.1, 24.30.49.60|


### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because it sets up the machine in a shorter time compared to the time it would take to manually set up the system. Furthermore, automation allows the set up to be easily replicated on another system. Automation also allows a user to see what is installed since the automation playbooks, such as Ansible, lists installed programs in an executable file.

The playbook implements the following tasks:
•	Install Docker.io
•	Install python-pip3
•	Install Docker
•	Increase virtual memory 
•	Download a docker web container image from sebp/elk:761 and assign it to use ports 5601, 9200, 5044


The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.
 
![image](https://user-images.githubusercontent.com/94084235/156966188-ddd073f4-d360-4f4a-8f59-71cb89b8058c.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
-	10.1.0.4
-	10.1.0.5

We have installed the following Beats on these machines:
-	Filebeat
-	Metricbeat

These Beats allow us to collect the following information from each machine:
Filebeat monitors the logs on the system and provides details such as the source of the traffic. Metricbeat provides multiple visual representation of data such as graphs.










---
  - name: Config web vm with docker
    hosts: webservers
    become: true
    tasks:

    - name: Uninstall apache2  (state=present is optional)
      apt: 
        name: apache2
        state: absent

    - name: docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present 

    - name: Install pip3
      apt:
        name: python3-pip
        state: present

    - name: Install Python Docker Module 
      pip:
        name: docker
        state: present
 
    - name: download and launch a docker web container
      docker_container:
        name: dvwa
        image: cyberxsecurity/dvwa
        state: started
        restart_policy: always
        published_ports: 80:80

    - name: Enable docker service
      systemd:
        name: docker
        enabled: yes






### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the install-elk.yml file to /etc/ansible.
	- nano /etc/ansible/install-elk.yml
- Update the /etc/ansible/hosts file to include the name of the host you intend to install
	- nano /etc/ansible/hosts
- Run the playbook and navigate to the public IP address in your preferred web browser to check that the installation worked as expected.
	- ansible-playbook /etc/ansible/install-elk.yml
