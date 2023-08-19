# Lab Setup Instruction Guide

# Digital Ocean Setup

If you do not already have a Digital Ocean account, you can take advantage of my [referral link](https://m.do.co/c/110226b98241) and get a $200 credit, which should get you through the whole lab.

## VM Settings:
- Hostname: splunk-ms
  - Region: Choose the region closest to you.
  - Datacenter: Stick to the same datacenter for all machines.
  - Image: CentOS 9 Stream x64
  - Droplet Type: Shared(Basic) 
  - CPU:
    - Minimum: 4 GB / 2 CPUs / 80 GB SSD (This will be slow with the limited CPUs.)
	- Recommended: 8 GB / 4 CPUs / 160 GB SSD
- Hostname: splunk-uf
  - Region: Choose the region closest to you.
  - Datacenter: Stick to the same datacenter for all machines.
  - Image: CentOS 9 Stream x64
  - Droplet Type: Shared(Basic) 
  - CPU:
    - Minimum: 2 GB / 2 CPUs / 60 GB SSD
	- Recommended: 4 GB / 2 CPUs / 80 GB SSD
- Hostname: splunk-indx (Do not set up in Digital Ocean, until you proceed to that part of the class to limit cost towards your credit.)
  - Region: Choose the region closest to you.
  - Datacenter: Stick to the same datacenter for all machines.
  - Image: CentOS 9 Stream x64
  - Droplet Type: Shared(Basic) 
  - CPU:
    - Minimum: 4 GB / 2 CPUs / 80 GB SSD (This will be slow with the limited CPUs.)
	- Recommended: 8 GB / 4 CPUs / 160 GB SSD

## Setting up the instances:

### splunk-ms
Login as root through SSH or the Digital Ocean console and use the following commands:
```
mkdir /opt/splunk
yum install wget
wget -O splunk.tgz "https://download.splunk.com/products/splunk/releases/9.1.0.2/linux/splunk-9.1.0.2-b6436b649711-Linux-x86_64.tgz"
tar xvzf splunk.tgz -C /opt
```

You now have a copy of Splunk enterprise in the /opt/splunk directory. To make your life easier, add the splunk binary to your systems path and persist it with your bashrc file:
```
export PATH=$PATH:/opt/splunk/bin
echo 'export PATH=$PATH:/opt/splunk/bin' >> ~/.bashrc
```

Now you can call the splunk binary by simply typing `splunk` in the console. Initiate the Splunk Enterprise instance with some additional setting changes:
```
splunk start --accept-license --answer-yes
#Enter an admin username and password that you will use throughout the course
splunk stop
splunk enable web-ssl #turns on HTTPS
splunk enable listen 9997 #enables a listening port for the indexer
splunk enable boot-start -user root -systemd-managed 1 #enables splunk at boot
splunk start
splunk enable deploy-server #enables deployment server
```