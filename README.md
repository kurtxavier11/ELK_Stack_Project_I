# UCLA Cybersecurity Project I - ELK Deployment with Microsoft Azure
##by Kurt Xavier Anderson

## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted in the diagram below.

![KA-NetworkDiagram](https://user-images.githubusercontent.com/85702906/140440104-c3701f7d-9863-41bf-b611-1fe17773bf55.PNG)


These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the .YML file may be used to install only certain pieces of it, such as Filebeat.

  - [Ansible Playbook](https://github.com/kurtxavier11/First_ELK_Stack/blob/main/Linux/pentest.yml)
  - [Anisible Hosts](https://github.com/kurtxavier11/First_ELK_Stack/blob/main/Ansible/hosts)
  - [Ansible Configuration](https://github.com/kurtxavier11/First_ELK_Stack/blob/main/Ansible/ansible.cfg)
  - [ELK Installation and VM Configuration](https://github.com/kurtxavier11/First_ELK_Stack/blob/main/Linux/install-elk.yml)
  - [Filebeat Playbook](https://github.com/kurtxavier11/First_ELK_Stack/blob/main/Linux/filebeat-playbook.yml)
  - [Filebeat Configuration File](https://github.com/kurtxavier11/First_ELK_Stack/blob/main/Linux/filebeat-config.yml)
  - [Metricbeat Playbook](https://github.com/kurtxavier11/First_ELK_Stack/blob/main/Linux/metricbeat-playbook.yml)
  - [Metricbeat Configuration File](https://github.com/kurtxavier11/First_ELK_Stack/blob/main/Linux/metricbeat-config.yml)


This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly reliable, in addition to restricting traffic to the network.
- What aspect of security do load balancers protect?
    
    Load balancers distribute network traffic evenly and protect the availability of data.
 
- What is the advantage of a jump box?    
    
    The advantage of a jump box is that it is automated, more secure, and redundant.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the data and system logs.
- What does Filebeat watch for?
  
  Filebeat watches for logs or locations, and collects log events for analysis at a later time in Elasticsearch or Logstash.

- What does Metricbeat record? 
 
  Metricbeat records metrics and statistics and outputs them to where you choose. 

The configuration details of each machine may be found below:
| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump-Box-Provisioner| Gateway  | 10.0.0.4/20.69.100.146| Linux |
| Web-1    |Web Server| 10.0.0.5   | Linux            |
| Web-2    |Web Server| 10.0.0.6   | Linux            |
| Web-3    |Web Server| 10.0.0.7   | Linux            |
| KA-ELK-Server1| ELK Server | 10.1.0.4/20.38.170.77| Linux        |

### Access Policies

The machines on the internal network are not exposed to the public Internet. Only the KA-ELK-Server1 machine can accept connections from the Internet. 
Access to this machine is only allowed from the following IP addresses:
- Local Machine Public IP through TCP 5601

Machines within the network can only be accessed by the Local Machine.
- Machines allowed to access my ELK VM:
  The Jump-Box-Provisioner IP: 10.0.0.4 via SSH port 22
  Local Machine Public IP from port TCP 5601


A summary of the access policies in place can be found in the table below:

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump-Box-Provisioner | No      | Local Machine IP via SSH port 22    |
| Web-1    | No                  | 10.0.0.4 via SSH port 22                  |
| Web-2    |  No                 | 10.0.0.4 via SSH port 22                     |
| Web-3    | No                  | 10.0.0.4 via SSH port 22|
| KA-ELK-Server1| No             | Local Machine IP using TCP 5601|

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because Ansible allows you to deploy multiple containers, creating redundancy in the network and the ability to deploy applications with mulitiple tiers. Ansible playbooks are modular and can be customized to the needs of the server your are building.
- What is the main advantage of automating configuration with Ansible?
  The main advantage of automating configuration with Ansible is to create a stable environment for developers and cyber security professionals.

The playbook implements the following tasks:
- Specifying a new VM and remote user
```
- name: Configure ELK VM with Docker
  hosts: elk
  remote_user: KurtRT
  become: true
  tasks:
```
- Install required services docker.io and python3-pip
```
    - name: Install docker.io
      apt:
        update_cache: yes
        force_apt_get: yes
        name: docker.io
        state: present
    - name: Install python3-pip
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present
```
- Install Docker module
```
    - name: Install Docker module
      pip:
        name: docker
        state: present
```
- Increase the virtual memory for usage
```
    - name: Increase Virtual Memory
      command: sysctl -w vm.max_map_count=262144


    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes
```
- Downloading and launching the docker elk container through published ports
```
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044
```

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![docker_ps_output](https://user-images.githubusercontent.com/85702906/140440031-597011e3-4380-41e3-ad63-64722388b396.PNG)


### Target Machines & Beats
This ELK server is configured to monitor the following machines:

- Web-1 : 10.0.0.5
- Web-2 : 10.0.0.6
- Web-3 : 10.0.0.7

We have installed the following Beats on these machines:

- KA-ELK-Server1, Web-1, Web-2, and Web-3
- ELK Stack installed : FileBeat and MetricBeat

These Beats allow us to collect the following information from each machine:

- FileBeat collects and logs events for analysis. Depending on the operating system, FileBeat combines automatic default paths with Elasticsearch and Kibana rules to keep it simple for analysis. 

- MetricBeat takes metrics and statistics to monitor server activity like Apache or MySQL. MetricBeat can then insert these metrics directly into Elasticsearch or send them to Logstash. 

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the '/etc/ansible/files/filebeat-config.yml' file to '/etc/filebeat/filebeat-playbook.yml'.
- Update the ```/etc/hosts``` file to include the name and IP address where ELK will be installed.
- Run the playbook, and navigate to http://[ELKServerIP]:5601/app/kibana to check that the installation worked as expected.

