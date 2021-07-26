## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

[ELK-Project Topology Diagram](https://drive.google.com/file/d/1Z9SffD1OD_YTDUqbtxnq_-u-iKf0_sfs/view?usp=sharing )




These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the Ansible-Playbook file may be used to install only certain pieces of it, such as Filebeat.

  - Kibana Programs:  filebeat-playbook.yml
                      metricbeat-playbook.yml

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the Damn Vulnerable Web Application.

Load balancing ensures that the application will be highly highly, in addition to restricting access to the network.
- Load balancers protect the system from DDoS attacks by shifting attack traffic. The advantage of a jumpbox is to give access to the user from a single node that can be secured and monitored. 

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the jumpbox and system.
- Filebeat is a lightweight transport for forwarding and centralizing log data. Installed as an agent on your servers, Filebeat monitors the log files or locations that you specify, collects log events, and forwards them either to Elasticsearch or Logstash for indexing. 
- Metricbeat takes the metrics and statistics that it collects and transports them to the output that you specify, such as  Elasticsearch or Logstash. Metricbeat helps you monitor your servers by collecting metrics from the system and services running on a server.

The configuration details of each machine may be found below.

| Name     | Function                | IP Address      | Operating System |
|----------|-------------------------|-----------------|------------------|
| Jump Box | Gateway                 | 10.0.0.6        |      Linux       |
|ELK Stack | ElasticSearch Stack     | 10.4.0.4        |      Linux       |
| Web-1    | Web Server              | 10.0.0.4        |      Linux       |
| Web-2    | Web Server              | 10.0.0.7        |      Linux       |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:

- 104.42.15.91 

Machines within the network can only be accessed by private IP addresses.
- The only machine that is allowed to connect to the Elk VM is through the Jump Box. The IP address to connect from the Jump box to the ELK VM is through 10.4.0.4, which is a private IP address.

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box |       SSH-22 - Yes  | 104.42.15.91         |
| Elk-Stack|       No            | 52.255.201.85        |
| Web-1    |       No            | Web LB 13.91.218.12  |
| Web-2    |       No            | Web LB 13.91.218.12  |
| Web LB   | HTTP - 80 - Yes     |       *              |
| ELK      | Kibana - 5601 - Yes |       *              |
| ELK      | HTTP API-9200-Yes   |  10.0.0.0/16         |
### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- The main advantage using Ansible as an automatic system it allows programs to run on a continous basis without interruption or interaction from employees. It saves time and money by not having to continually writing scripts.

The playbook implements the following tasks:
--- To setup an ELK VM on an Ansible container, please expand script below. To write a play to configure the ELK server, should only run on the VM in the [elk] group. Then use the hosts file as an option in Ansible to specify which machines to run a set of tasks against.

```
- name: Configure Elk VM with Docker
  hosts: elk
  remote_user: azadmin
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
        name:  docker
        state: present

      # Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144

      # Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
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
          -  5601:5601
          -  9200:9200
          -  5044:5044

      # Use systemd module
    - name: Enable service docker on boot
      systemd:
        name: docker
        enabled: yes
```

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![docker ps output](https://drive.google.com/file/d/1Hg4cwxU6QasqwdnWFM5a15p_sGXWy8v3/view?usp=sharing )

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web-1               10.0.0.4
- Web-2               10.0.0.7
- JackBox Provisioner 10.0.0.6
- Kabana              10.4.0.4

We have installed the following Beats on these machines:
- Filebeat
- Metricbeat
- ELK

These Beats allow us to collect the following information from each machine:
- Filebeat collects data on filtered protocols that are set by the user. An example of such data collection can be exemplified by being able to manipulate the time duration of collecting the data, what type of data can be captured, such as 404 and 505 errors, and being able to filter out data derived from specific geographical locations. Also filebeat captures such events as logins to see who is actively logging into the system. An example of a Filebeat is as follows (Click on the arrow next to Filebeat at the beginning of this paragraph to see an example of a filebeat readout.):
-Filebeat example:
Jul 24, 2021 @ 21:22:59.000Web-2
metricbeat
2021-07-25T04:22:59.391Z#011INFO#011[monitoring]#011log/log.go:145#011Non-zero metrics in the last 30s#011{"monitoring": {"metrics": {"beat":{"cpu":{"system":{"ticks":125340,"time":{"ms":108}},"total":{"ticks":328990,"time":{"ms":255},"value":328990},"user":{"ticks":203650,"time":{"ms":147}}},"handles":{"limit":{"hard":524288,"soft":1024},"open":15},"info":{"ephemeral_id":"e8fb99de-6f6b-4e2b-a919-f89ccc6ef369","uptime":{"ms":42720082}},"memstats":{"gc_next":17133424,"memory_alloc":9808072,"memory_total":58174225344},"runtime":{"goroutines":67}},"libbeat":{"config":{"module":{"running":0}},"output":{"events":{"acked":69,"batches":9,"total":69},"read":{"bytes":3484},"write":{"bytes":127185}},"pipeline":{"clients":4,"events":{"active":0,"published":69,"total":69},"queue":{"acked":69}}},"metricbeat":{"docker":{"container":{"events":3,"success":3},"cpu":{"events":3,"success":3},"diskio":{"events":3,"success":3},"info":{"events":3,"success":3},"memory":{"events":3,"success":3},"network":{"events":3,"success":3}},"system":{"cpu":{"events":3,"success":3},"load":{"events":3,"success":3},"memory":{"events":3,"success":3},"network":{"events":12,"success":12},"process":{"events":24,"success":24},"process_summary":{"events":3,"success":3},"socket_summary":{"events":3,"success":3}}},"system":{"load":{"1":0,"15":0,"5":0,"norm":{"1":0,"15":0,"5":0}}}}}}
- Metricbeat is a lightweight shippper that you can install on your servers to periodically collect metrics/data from the operating system and from services running on the server. Metricbeat take the metrics and statistics that is collects. An example of the data collected during a specific day is as follows:
- Metricbeat example data
2021-07-25T04:19:29.391Z#011INFO#011[monitoring]#011log/log.go:145#011Non-zero metrics in the last 30s#011{"monitoring": {"metrics": {"beat":{"cpu":{"system":{"ticks":124780,"time":{"ms":112}},"total":{"ticks":327360,"time":{"ms":269},"value":327360},"user":{"ticks":202580,"time":{"ms":157}}},"handles":{"limit":{"hard":524288,"soft":1024},"open":15},"info":{"ephemeral_id":"e8fb99de-6f6b-4e2b-a919-f89ccc6ef369","uptime":{"ms":42510082}},"memstats":{"gc_next":15685824,"memory_alloc":8925032,"memory_total":57888791112},"runtime":{"goroutines":67}},"libbeat":{"config":{"module":{"running":0}},"output":{"events":{"acked":79,"batches":9,"total":79},"read":{"bytes":3549},"write":{"bytes":137897}},"pipeline":{"clients":4,"events":{"active":0,"published":79,"total":79},"queue":{"acked":79}}},"metricbeat":{"docker":{"container":{"events":3,"success":3},"cpu":{"events":3,"success":3},"diskio":{"events":3,"success":3},"info":{"events":3,"success":3},"memory":{"events":3,"success":3},"network":{"events":3,"success":3}},"system":{"cpu":{"events":3,"success":3},"filesystem":{"events":11,"success":11},"fsstat":{"events":1,"success":1},"load":{"events":3,"success":3},"memory":{"events":3,"success":3},"network":{"events":12,"success":12},"process":{"events":22,"success":22},"process_summary":{"events":3,"success":3},"socket_summary":{"events":3,"success":3}}},"system":{"load":{"1":0,"15":0,"5":0,"norm":{"1":0,"15":0,"5":0}}}}}}

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the elk_install.yml file to /etc/ansible/roles/elk_install.yml.
- Update the hosts file to include attribute, such as [elk], then include your destination IP of the ELK server to directly below.
[elk]
10.4.0.4 ansible_python_interpreter=/usr/bin/python3
- Example of host file 
# This is the default ansible 'hosts' file.
#
# It should live in /etc/ansible/hosts
#
#   - Comments begin with the '#' character
#   - Blank lines are ignored
#   - Groups of hosts are delimited by [header] elements
#   - You can enter hostnames or ip addresses
#   - A hostname/ip can be a member of multiple groups

# Ex 1: Ungrouped hosts, specify before any group headers.

#green.example.com
#blue.example.com
#192.168.100.1
#192.168.100.10

# Ex 2: A collection of hosts belonging to the 'webservers' group

[webservers]
#alpha.example.org
#beta.example.org
#192.168.1.100
#192.168.1.110
10.0.0.4 ansible_python_interpreter=/usr/bin/python3
10.0.0.7 ansible_python_interpreter=/usr/bin/python3


# If you have multiple hosts following a pattern you can specify
# them like this:

#www[001:006].example.com

# Ex 3: A collection of database servers in the 'dbservers' group

#[dbservers]
#
[elk]
10.4.0.4 ansible_python_interpreter=/usr/bin/python3


#db01.intranet.mydomain.net
#db02.intranet.mydomain.net
#10.25.1.56
#10.25.1.57

# Here's another example of host ranges, this time there are no
# leading 0s:

#db-[99:101]-node.example.com
 

- Run the playbook, and navigate to http://your_elk_server_ip]:5601/app/kibana to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it?_
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- _Which URL do you navigate to in order to check that the ELK server is running?

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
