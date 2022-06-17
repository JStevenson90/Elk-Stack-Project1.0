## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![https://app.diagrams.net/#G1BBytBF4byFc0dddj4vzsh96pjGzyoHHx]

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the _____ file may be used to install only certain pieces of it, such as Filebeat.

  ---
- name: Installing and Launch Filebeat
  hosts: webservers
  become: yes
  tasks:
    # Use command module to install filebeat
  - name: Download filebeat .deb file
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.6.1-amd64.deb

    # Use command module to unpackage filebeat
  - name: Install filebeat .deb
    command: dpkg -i filebeat-7.6.1-amd64.deb

    # Use copy module to move my edited filebeat.yml
  - name: Drop in filebeat.yml
    copy:
      src: /home/azureuser/BeatConfigs/filebeat.yml
      dest: /etc/filebeat/filebeat.yml

    # Use command module to enable filebeat modules
  - name: Enable and Configure System Module
    command: filebeat modules enable system

    # Use command module
  - name: Setup filebeat
    command: filebeat setup

    # Use command module
  - name: Start filebeat service
    command: service filebeat start

    # Use systemd module
  - name: Enable service filebeat on boot
    systemd:
      name: filebeat
      enabled: yes

    # Use command module
  - name: Download metricbeat
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.6.1-amd64.deb

    # Use command module
  - name: install metricbeat
    command: dpkg -i metricbeat-7.6.1-amd64.deb

    # Use copy module
  - name: drop in metricbeat config
    copy:
      src: /home/azureuser/BeatConfigs/metricbeat.yml
      dest: /etc/metricbeat/metricbeat.yml

    # Use command module
  - name: enable and configure docker module for metric beat
    command: metricbeat modules enable docker

    # Use command module
  - name: setup metric beat
    command: metricbeat setup

    # Use command module
  - name: start metric beat
    command: service metricbeat start

    # Use systemd module enable metricbeat on boot
  - name: Enable service metricbeat on boot
    systemd:
      name: metricbeat
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

Load balancing ensures that the application will be highly responsive, in addition to restricting access to the network.
- What aspect of security do load balancers protect? Load balancers protects against redundancy and attacks such as DDoS, by analyzing incoming traffic and determining where to send the taffic. This even distribution of traffic prevents one server from being overloaded with traffic.  What is the advantage of a jump box? They add a security layer to the web servers, thus preventing them from being exposed to the public. (i.e., mandate that private IP's of virtual machines are known to gain access). 

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the configuration files and system logs.
- What does Filebeat watch for? File watches for log files as well as log events. 
- What does Metricbeat record? Metricbeat records metrics from the operating system and services running on the server. 

The configuration details of each machine may be found below.
Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name        | Function   | IP Address  | Operating System |
|-------------|------------|-------------|------------------|
| JumpBoxVM   | Gateway    | 10.77.0.4   | Linux            |
| Web-1 (DWVA)| Web Server | 10.77.0.5   | Linux            |
| Web-2 (DWVA)| Web Server | 10.77.0.6   | Linux            |
| ElkVM       | Monitoring | 10.88.0.4   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jumpbox machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- 20.248.204.243 (Jumpbox Public IP)

Machines within the network can only be accessed by JumpboxVM.
- Which machine did you allow to access your ELK VM? JumpBoxVM.
- What was its IP address? 20.248.204.243

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| JumpBoxVM| Yes                 | 10.77.0.5 / 10.77.0.6|
| Web-1    | No                  | 20.248.204.243       |
| Web-2    | No                  | 20.248.204.243       |
| ElkVM    | No                  | 20.248.204.243       |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- What is the main advantage of automating configuration with Ansible? The main advantage of Ansible is that it allows you to write scripts and execute listed tasks in a playbook to automate your systems. Ansible has the ability to control what's being performed and/or installed on a machine. 

The playbook implements the following tasks:
- The first steps in configuring the Elk VM is to: Install docker.io onto the Elk VM. 
- Then, install python and the docker module onto to the Elk VM. 
- Elk VM requires more memory, this task increases the memory to 262144.
- The docker Elk container is then downloaded and installed onto the Elk VM, and is tasked with always starting to avoid a manual start each time. 
- Docker services are then enabled up run upon boot, so they automatically start when the VM is turned on. 

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![TODO: Update the path with the name of your screenshot of docker ps output](Images/docker_ps_output.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web-1: 10.77.0.5
- Web-2: 10.77.0.6

We have installed the following Beats on these machines:
- Filebeat 
- Metricbeat

These Beats allow us to collect the following information from each machine:
- Filebeat: Log data and log events
- Metricbeat: Metrics and system statistics

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the configuration file from your Ansible container to your Web VM's.
- Update the /etc/ansible/hosts file to include the IP addresses of the Elk Server VM and the webservers.
- Run the playbook, and navigate to http://13.70.189.176:5601/app/kibana to check that the installation worked as expected.

- Which file is the playbook? Filebeat-config
- Where do you copy it? copy /etc/ansible/files/filebeat-config.yml to /etc/filebeat/filebeat.yml
- Which file do you update to make Ansible run the playbook on a specific machine? Update filebeat-config.yml 
- How do I specify which machine to install the ELK server on versus which to install Filebeat on? Specify which machine to install Filebeat by updating the host files  with the IP addresses of your web and elk servers, and selecting which group to run on in ansible.
- Which URL do you navigate to in order to check that the ELK server is running? http://13.70.189.176:5601/app/kibana 

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
- Downlaod Filebeat playbook with: curl -L -O https://gist.githubusercontent.com/slape/5cc350109583af6cbe577bbcc0710c93/raw/eca603b72586fbe148c11f9c87bf96a63cb25760/Filebeat > /etc/ansible/files/filebeat-config.yml
- Copy the /etc/ansible/files/filebeat-config.yml --> /etc/filebeat/filebeat-playbook.yml
- Update the filebeat-playbook.yml file to include installer with: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.6.1-amd64.deb
- Sudo su to gain super user root privileges and update filebeat.config.yml file with: nano filebeat-config.yml
- Run: ansible-playbook filebeat-playbook.yml

- Download Metricbeat file with: curl -L -O https://gist.githubusercontent.com/slape/58541585cc1886d2e26cd8be557ce04c/raw/0ce2c7e744c54513616966affb5e9d96f5e12f73/metricbeat > /etc/ansible/files/metricbeat-config.yml
- Copy the /etc/ansible/files/metricbeat --> /etc/metricbeat/metricbeat-playbook.yml
- Update the metricbeat-playbook.yml file with: curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.6.1-amd64.deb
- Sudo su to gain super user root privileges and update metricbeat.config.yml file with: nano metricbeat-config.yml
- Run: ansible-playbook metricbeat-playbook.yml
