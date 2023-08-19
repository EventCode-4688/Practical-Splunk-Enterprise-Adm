# Lab Setup Instruction Guide

# Docker Setup
Docker is the preferred setup method if you have enough local resources for the lab.
## Recommended Host PC specs:
- RAM: 32 GB Preferred, 16 GB Minimum
- Storage: 100 GB
- CPUs: 10 cores recommended
  _I recommend having at least 10 cores on your system, the docker compose does not limit the resources for the containers._

## Install Docker Desktop and initialize containers
1. Install [Docker Desktop](https://www.docker.com/products/docker-desktop/) for your system.
1. If using Windows, you will need to install Windows Subsystem For Linux as well. You can read more about this [here](https://docs.docker.com/desktop/wsl/)
1. Start up Docker Desktop after the install is complete and then close the GUI after it finishes loading.
1. Download the [docker-compose.yml](../Lab_Setup/docker/docker-compose.yml).
1. Open a terminal and navigate to the directory containing the docker-compose.yml
1. Enter the following command: `docker-compose up -d` to start the docker containers.
   - If you get any error messages, run the `docker-compose down` command to tear down the failed containers before troubleshooting.
   - If docker fails to map the ports, run the following command: `netsh int ipv4 show excludedportrange protocol=tcp` and see if the failed ports are in the excluded range. If they are (i.e. 9997, 9998) then change them in the docker-compose file to a port not in range (i.e. 9901:9997, 9902:9997) and then re-run the `docker-compose up -d` command.
1. Check to make sure the containers are healthy using `docker ps` and checking the status column.
1. For now you can stop the splunk-indx1 container: `docker stop splunk-indx1`

### splunk-ms
1. Drop into the container's shell as root: `docker exec -u root -it splunk-ms bash`
1. To make your life easier, add the splunk binary to your systems path and persist it with your bashrc file:
```
export PATH=$PATH:/opt/splunk/bin
echo 'export PATH=$PATH:/opt/splunk/bin' >> ~/.bashrc

```
1. `splunk stop`
1. `splunk enable web-ssl`
    #turns on HTTPS
1. `splunk set web-port 8443`
    #sets web port to 8443
1. `splunk enable boot-start -user root`
    #enables splunk at boot
1. `splunk start`
1. `splunk enable deploy-server`
    #enables deployment server


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

1. `splunk start --accept-license`
    #Enter an admin username and password that you will use throughout the course
1. `splunk stop`
1. `splunk enable web-ssl`
    #turns on HTTPS
1. `splunk set web-port 8443`
    #sets web port to 8443
1. `splunk enable listen 9997`
    #enables a listening port for the indexer
1. `splunk enable boot-start -user root -systemd-managed 1`
    #enables splunk at boot
1. `splunk start`
1. `splunk enable deploy-server`
    #enables deployment server (use admin and password that you set up when starting Splunk.)


At this point you should be able to sign into the Web Server by navigating to https://_splunk-msIP_:8443

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

1. `splunk start --accept-license`
    #Create admin user and password
1. `splunk set deploy-poll <splunk-ms IP>:8089`
1. `splunk add forward-server <splunk-ms IP>:9997`
1. `splunk restart`

Once the UF restarts you should be able to see the forwarder on the splunk-ms webserver under Settings > Forwarder Management > Clients Tab

You can also make sure the forwarder is sending it's internal logs by running the following search:
`index=_internal host=splunk-uf`
