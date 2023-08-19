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
yum install wget -y
wget -O splunk.tgz "https://download.splunk.com/products/splunk/releases/9.1.0.2/linux/splunk-9.1.0.2-b6436b649711-Linux-x86_64.tgz"
tar xvzf splunk.tgz -C /opt
```

You now have a copy of Splunk Enterprise in the /opt/splunk directory. To make your life easier, add the splunk binary to your systems path and persist it with your bashrc file:
```
export PATH=$PATH:/opt/splunk/bin
echo 'export PATH=$PATH:/opt/splunk/bin' >> ~/.bashrc
```

Now you can call the splunk binary by simply typing `splunk` in the console. Initiate the Splunk Enterprise instance with some additional setting changes:

1. `splunk start --accept-license
    #Enter an admin username and password that you will use throughout the course
1. `splunk stop`
1. `splunk enable web-ssl`
    #turns on HTTPS
1. `splunk enable listen 9997`
    #enables a listening port for the indexer
1. `splunk enable boot-start -user root -systemd-managed 1`
    #enables splunk at boot
1. `splunk start
1. `splunk enable deploy-server`
    #enables deployment server


At this point you should be able to sign into the Web Server by navigating to https://<splunk-ms IP>:8443

### splunk-uf
Login as root through SSH or the Digital Ocean console and use the following commands:
```
mkdir /opt/splunkforwarder
yum install wget -y
wget -O splunkforwarder.tgz "https://download.splunk.com/products/universalforwarder/releases/9.1.0.1/linux/splunkforwarder-9.1.0.1-77f73c9edb85-Linux-x86_64.tgz"
tar xvzf splunkforwarder.tgz -C /opt
```

You now have a copy of the Splunk Universal Forwarder in the /opt/splunkforwarder directory. To make your life easier, add the splunk binary to your systems path and persist it with your bashrc file:
```
export PATH=$PATH:/opt/splunkforwarder/bin
echo 'export PATH=$PATH:/opt/splunkforwarder/bin' >> ~/.bashrc
```

Now you can call the splunk binary by simply typing `splunk` in the console. Initiate the Splunk UF and point it to the splunk-ms instance as its deployment server and indexer:
```
splunk start --accept-license
# Create admin user and password
splunk set deploy-poll <splunk-ms IP>:8089
splunk add forward-server <splunk-ms IP>:9997
splunk restart
```

