---
title: "Beowulf Cluster Setup"
date: 2025-06-06
categories:
  - blog
tags:
  - it support
  - beowulf cluster
  - HPC cluster
  - slurm
  - munge
  - ssh
  - nfs
---

# Beowulf Cluster Setup

After looking around on the internet for a long time and numerous trial and error attempts (with my last attempt in October 2024 being hugely unsuccessful due to heterogenous computing systems in the lab) I was finally able to setup a small HPC cluster in the lab using off-the-shelf PCs running on Ubuntu. The primary goal was to achieve worry free computing across multiple devices and being able to access it via my lab PC from anywhere.  
I am no expert, but what I have written here is a no-shit general guide and *probably* the fastest way to setup and run a small HPC cluster using Ubuntu PCs. It also serves as a recollective guide for when I eventually try to do this all over again somewhere else (knowing myself, very likely that I'll do it all over again). The core setup consists of the following tools:
- [SSH](https://en.wikipedia.org/wiki/Secure_Shell) - for easy access to various nodes on the cluster
- [SLURM](https://en.wikipedia.org/wiki/Slurm_Workload_Manager) - for workload management and job submission
- [NFS](https://en.wikipedia.org/wiki/Network_File_System) - for a shared file system (currently, the implementation involves mounting a hard disk on one node and sharing it over the network)

The following versions of different softwares were used in this setup:
- Ubuntu 24.04.2 LTS (GNU/Linux 6.8.0-60-generic x86_64)
- slurm-wlm (23.11.4-1.2ubuntu5)
- openssh-server (1:9.6p1-3ubuntu13.11)
- openssh-client (1:9.6p1-3ubuntu13.11)
- munge (0.5.15-4build1)

*NOTE: If you have different versions of the operating system or other packages and face any issues, make sure you go through the respective documentation for any clarifying details that might have been missed here.*

## Table of Contents
- [Overview](#overview)
- [Install Ubuntu](#install-ubuntu)
- [Setup a private local network](#setup-a-private-local-network)
- [Setup SSH](#setup-ssh)
- [Setup Munge](#setup-munge)
- [Setup Slurm](#setup-slurm)
- [Setup NFS](#setup-nfs)
- [References](#references)

## Overview
The following steps were followed to setup the full cluster and shared storage system. It is important that you follow the steps in sequence in which they are written and note the versions of different softwares/OS that are used and take note of any differences you may have while setting up your cluster. In case you face any issues, as mentioned before, do go through the updated documentations for any softwares that might not be matching the version numbers mentioned above.

**IMPORTANT:** Any steps marked by square brackets, e.g., [Slurm Controller Node] or [NFS Server Node] are just to be performed on your slurm controller node or the nfs server node. Steps that aren't marked explicitly by square brackets are to be performed on all computers.

Here are the list of steps, in the particular order, to follow for the setup:
1. Install Ubuntu on all computers
2. Setup a private local network
3. Setup SSH 
4. Setup munge 
5. Setup slurm 
6. Setup nfs server and clients

## Install Ubuntu

You can easily follow the instructions [here](https://ubuntu.com/tutorials/install-ubuntu-desktop) to install a fresh copy of Ubuntu on each system (recommended).  

Make sure you setup the same user across all machines. We'll call this user `clusteruser` from now on. It is recommended that you name each computer on the cluster as `node0`, `node1`, ...  
However, if you give it custom names, make sure you remember the PC hostname and their corresponding static IP addresses (mentioned in the next step).

## Setup a private local network

### Brief Overview

To make sure that all the computers can talk to each other, it is important to setup a private local network where all the computers are linked. You can read more about private local networks [here](https://en.wikipedia.org/wiki/Private_network). This step ensures that our computers are all connected to each other and they are not exposed via public IP addresses to the internet. 

### Setup Instructions

Create a private network using a router or switch and update the IP address of all the machines manually to some static value, generally of the kind 192.168.x.x or 10.x.x.x  
*NOTE: You need to remember the IP address of each node and their corresponding hostnames, as it will come in handy later during setup.*

To ensure that all the computers "know" each other, make sure you update the `/etc/hosts` file on each computer to something like this:

```
# /etc/hosts

# Format
# IP_Address HostName
127.0.0.1 localhost
192.168.1.0 node0
192.168.1.1 node1
192.168.1.2 node2
```

Make sure you substitute the correct IP address and node hostnames in the above file.

## Setup SSH

### Brief Overview
The [SSH (or Secure Shell Protocol)](https://en.wikipedia.org/wiki/Secure_Shell) provides us with a cryptographically secure method to login and execute commands on nodes/computers on our private network from other PC(s) on the network. It is recommended to set this up for nodes that you would require to access online from anywhere on the network such as the Slurm controller node, so that you can check for any issues on these critical systems from any other PC on the network. I also have the setup to access the PCs on our cluster from a lab PC outside the cluster (since, it makes it easier to configure everything from a single PC and offers me remote login from physically distant places). However, you can skip doing this if you intend to be physically available while running jobs on the cluster that might fail.  

The following guide also deals with [setting up passwordless login](#advanced-setup-for-passwordless-access) to the nodes via storing ssh generated keys. This makes it easier to access the ssh nodes from other PCs on the network without having to enter the password everytime. Feel free to skip this step if physical access to any of these nodes are not restricted to trusted individuals/groups. You can easily ssh without setting up the passwordless login, but you do have to enter the credentials everytime you login. Ideally, if you are just logging in to these PCs rarely, it is suggested to skip setting up the passwordless login.  

### Setup Instructions

Install the ssh server and client packages:

```bash
$ sudo apt install openssh-server openssh-client
```

The `openssh-server` package needs to be installed in PCs where you intend to login remotely, i.e., SSH Server Nodes, and the `openssh-client` packages need to be installed in PCs from where you want to login other PCs, i.e., SSH Client Nodes. Ideally, the server should be setup for the slurm controller node (more on this later) so that you can easily configure things from one or more client nodes (including your own PC, if needed).

That's it! You can see if the ssh server and client processes are running and if not restart them. It is also recommended to enable ssh on startup. You can use the following commands to enable ssh for startup and restart the ssh server and clients:

```bash
$ sudo systemctl enable ssh
$ sudo systemctl restart ssh
```

You may need to allow the required port (default 22) over the firewall:

```bash
$ sudo ufw allow 22
```

**[SSH Server Nodes]** Make any changes needed to the port number in the config file (`/etc/ssh/sshd_config`) on the ssh server if you want to use any port other than the default port 22 (recommended so that people don't send in random requests over the private network, you can skip this if this is on a home network or only accessible to few trusted individuals/groups).  
*NOTE: Allow the corresponding port over firewall and block firewall for port 22 if not in use*

You can test the setup by running the following command from the client PC (assuming the ssh server is setup on node1 and you have made changes to the `/etc/hosts` file mentioned above):

```bash
$ ssh clusteruser@node1
```

If this is your first time connecting to the server it will ask you to add the server to the list of trusted hosts, when prompted type in `yes` or `y`.  
It will ask you for password for the user `clusteruser`, provide the password and enter and you should see changes to your commandline once you are logged in, for example:

```bash
# Initial commandline
clusteruser@node0:~$ ssh clusteruser@node1

# After logging in, it should reflect the change
clusteruser@node1:~$
```

### Advanced Setup for Passwordless Access

**[SSH Client Nodes]** On the PC with the ssh client (the PC from which you want to remote login to other nodes), generate a key pair consisting of a public key and a private key (skip if already done, or you can generate a new key specific for the cluster):

```bash
$ ssh-keygen -t rsa
```

When prompted, enter a filename (defaults to `id_rsa` and a passphrase - it is important to set a secure passphrase and remember it, you will need it later when using the key).

**[SSH Server Nodes]** On the ssh server, make sure you have a `~/.ssh` folder, if not you can create one using the following command:

```bash
$ mkdir -p ~/.ssh
```

**[SSH Client Nodes]** Upload your public key from the client nodes to the server nodes (assuming node1 is the ssh server here):

```bash
$ ssh-copy-id clusteruser@node1
```

The first time you will be asked for the login credentials and you should see a message that the key is uploaded/stored. Voila! You have just setup passwordless access to your ssh server from the ssh client.  
*NOTE: It is recommended to disable passworld based login on the nodes and change the port for ssh to make the connections more secure. You can just google how to do that as we will not be covering this on the guide here.*

### [SSH Client Nodes] Setup SSH Config

To make it easier to ssh in and out of systems, you can setup hostnames and user details in the `/etc/ssh/ssh_config` file on the client nodes, like so:

```bash
# /etc/ssh/ssh_config on node0

Host node1
    HostName 192.168.1.1
    User clusteruser
    Port 2220

Host node2
    HostName 192.168.1.2
    User clusteruser
    Port 2220
```

Now you should be able to ssh from `node0` to `node1` using the following command (without password if you have setup passwordless authentication):

```bash
$ ssh node1
```

## Setup Munge

### Brief Overview

Here you need to make the important choice of deciding which of the systems will be the controller node for your HPC cluster. The [slurm](https://slurm.schedmd.com/quickstart.html) controller node basically determines things like job scheduling, resource allocation, and cluster monitoring. For large scale clusters, it is recommended to have a separate controller node and worker nodes/compute nodes which provide computing power. However, for our use case, since it is a small cluster and I wanted to get the most of the compute, I have setup a single PC as both the controller and worker and we have two additional worker nodes to provide computer power.   *NOTE: In large systems, this might affect performance. It is recommended to setup a single controller node and other nodes as the worker nodes.*

[Munge](https://dun.github.io/munge/) is basically an authentication service that lets a process authenticate the UID (User ID) and GID (Group ID) of another local or remote process within a group of hosts having common users and groups.  
*NOTE: we have a single common user across our systems called `clusteruser` and we will have other users such as `munge` and `slurm` which will be common across the systems.*

Installing munge is pretty straightforward once you know what you are doing. However, the file permissions can get a bit tricky, so make sure you follow all the steps in order for this section. Also, it is recommended to configure the Slurm Controller Node first and then the Worker Nodes.

### Munge Basic Setup

On all computers, install the munge packages:

```bash
$ sudo apt install munge libmunge2 libmunge-dev
```

To test your installation, run the following command:

```bash
$ munge -n | unmunge | grep STATUS
```

You should see a message like `STATUS: SUCCESS(0)`. This indicates that munge is setup correctly.


### [Controller Node] Munge setup

After the [basic setup](#munge-basic-setup), you should note the presence of a munge key at `/etc/munge/munge.key`. If there is no munge key, create one manually:

```bash
$ sudo /user/sbin/mungekey
```

Now, the tricky part where we set permissions for the munge files. When munge was installed, a new user called `munge` would have been created on the system. It is necessary to give proper permissions to the user over the munge files, like so:

```bash
$ sudo chown -R munge: /etc/munge/ /var/log/munge/ /var/lib/munge/ /run/munge/
$ sudo chmod 0700 /etc/munge/ /var/log/munge/ /var/lib/munge/
$ sudo chmod 0755 /run/munge/
$ sudo chmod 0700 /etc/munge/munge.key
$ sudo chown -R munge: /etc/munge/munge.key
```

Enable the munge service to run at startup:

```bash
$ sudo systemctl enable munge
```

Restart the munge service:

```bash
$ sudo systemctl restart munge
```

If there are any errors in restarting the service, you can investigate them as so:

```bash
# Check status of process (should be active/running in case of no errors)
$ sudo systemctl status munge

# Check logs
$ sudo cat /var/log/munge/munged.log
```

That's it, munge has been setup for the Slurm Controller Node. Next, we move on to the Slurm Worker Nodes.

### [Worker Nodes] Munge Setup

After the [basic setup](#munge-basic-setup), what we need to do is copy the key off the controller node to the worker nodes. This can be done in several ways, this is how I did it:

```bash
# From the Controller Node
# First copy the key to an accessible location
clusteruser@node1:~$ sudo scp /etc/munge/munge.key clusteruser@node0:

# On the Worker Node
# Copy the key to the /etc/munge directory
clusteruser@node0:~$ sudo mv munge.key /etc/munge/
```

Once this is done, we set permission on the worker nodes and configure it to run at startup and restart the munge service:

```bash
$ sudo chown -R munge: /etc/munge/ /var/log/munge/ /var/lib/munge/ /run/munge/
$ sudo chmod 0700 /etc/munge/ /var/log/munge/ /var/lib/munge/
$ sudo chmod 0755 /run/munge/
$ sudo chmod 0700 /etc/munge/munge.key
$ sudo chown -R munge: /etc/munge/munge.key
$ systemctl enable munge
$ systemctl restart munge
$ systemctl status munge
$ sudo cat /var/log/munge/munged.log
```

Now, we need to test the munge connection on the Controller Node, like so:

```bash
# On the Controller Node
clusteruser@node1:~$ munge -n | ssh node0 unmunge
```

You should see another SUCCESS message here. If this fails, try restarting the munge service on the Controller and Worker Nodes and check the logs.

*NOTE: We are assuming throughout the guide that the Slurm Controller Node is node1 and other nodes are the worker nodes. Make sure you make any required changes to these commands while running according to your system hostnames.*

## Setup Slurm

### Brief Overview

[Slurm](https://slurm.schedmd.com/quickstart.html) is an open-source framework for job scheduling and resource management across multiple nodes on a cluster. At the time of creation of this guide, the most recent version is v25.05. However, note that we used v23.11.4-1.2ubuntu5 for the setup.

The setup and configurations for Slurm will be mostly the same across the Controller and Worker Nodes. However, the only thing that changes is which service you run in which computer. 

### Install Slurm Packages

On all computers, install the required packages:

```bash
$ sudo apt install slurm-wlm
```

### [Controller Node] Slurm Setup

Use slurm's handy configuration file generator located at `/usr/share/doc/slurmctld/slurm-wlm-configurator.html` to create your configuration file. You can open the configurator file with your browser. 

*NOTE: Slurm configuration files are complicated and you can find a more detailed guide [here]() for the various options it offers.*  

*NOTE2 (Important): It is essential to use the configurator file provided with your version of slurm. I have failed numerous times with obscure errors from using a configuration file generated by a configurator for different version of slurm. DO NOT REPEAT MY MISTAKES.*

You don't have to fill out all the fields as you can leave out most on their defaults. The following fields are recommended to edit manually:
- ClusterName: `<YOUR-CLUSTER-NAME>`
- SlurmctldHost: `<CONTROLLER-NODE-HOSTNAME>`
- NodeName: `<WORKER-NODE-HOSTNAME>`
- Values for CPUs, Sockets, CoresPerSocket, and ThreadsPerCore according to `slurmd -C` on each system
- ProctrackType: `LinuxProc`

Once you are done, you can press `submit` and a configuration file will appear in your browser that you can copy-paste. Copy-paste this into a new `/etc/slurm/slurm.conf` file:

```bash
# On Controller Node
# Open the file in nano editor and copy the text
clusteruser@node1:~$ sudo nano /etc/slurm/slurm.conf
```

*NOTE: If you are using a heterogenous system, it is important to separate your Nodes according to the computing power they provide. It took me a while to understand this and make the corresponding changes to the `slurm.conf` file. Here is an example to get you started (assuming you have two different types of Nodes with dissimilar computing powers):*  

```bash
# /etc/slurm/slurm.conf

# Towards the end, define heterogenous types of PCs on the system

# COMPUTE NODES 
NodeName=node[0-1] CPUs=16 RealMemory=32019 Sockets=1 CoresPerSocket=4 ThreadsPerCore=1 State=UNKNOWN
NodeName=node2 CPUs=8 RealMemory=31834 Sockets=1 CoresPerSocket=4 ThreadsPerCore=2 State=UNKNOWN
# Partition details (just needs to be mentioned once)
PartitionName=debug Nodes=ALL Default=YES MaxTime=INFINITE State=UP
```

At this point, it is important to copy this file onto each worker node. This can be achieved in many ways, for me the simplest option was to again choose `scp`:

```bash
# On controller node

clusteruser@node1:~$ sudo scp /etc/slurm/slurm.conf clusteruser@node0:

# On worker node

# Remove any old config file if present
clusteruser@node0:~$ sudo rm /etc/slurm/slurm.conf
# Move new configuration
clusteruser@node0:~$ sudo mv slurm.conf /etc/slurm/
```

### [Worker Node] Slurm Setup

After [installing slurm packages](#install-slurm-packages) and copying the `slurm.conf` file to the Worker Nodes, we are basically done with the setup. Now we need to restart the services and test our setup.  

### Testing Slurm across Computers

We first enable the slurm worker daemon on the Worker Nodes and enable it to run at startup:

```bash
# On Worker Nodes
$ sudo systemctl enable slurmd
$ sudo systemctl restart slurmd
```

As long as you don't encounter any errors, this should be working. Now, we configure the slurm controller daemon to start at startup and restart the service:

```bash
# On Controller Node
$ sudo systemctl enable slurmctld
$ sudo systemctl restart slurmctld
```

We can now test the installation by using the following commands:

```bash
$ sinfo
$ srun hostname
```

These commands return information on the nodes in the cluster and their hostnames and should be executable from anywhere on the cluster. You can test if all your systems are up at this point else restart the `slurmd (Worker Node)` or `slurmctld (Controller Node)` services.

Any errors you encounter will be logged here:

```bash
$ sudo cat /var/log/slurm.slurmd.log [Worker Node]
$ sudo cat /var/log/slurm.slurmctld.log [Controller Node]
```

If all goes well, congratulation you're almost there in setting up your own shared storage HPC. If you choose not to include shared storage and just want to run simulations on it and not so much data analysis, you're done!

## Setup NFS

### Brief Overview

[NFS or Network File System](https://en.wikipedia.org/wiki/Network_File_System) is a distibuted file system protocol allowing client PCs, i.e., [NFS Client Nodes], on the network to access files over the network from a server PC, i.e., [NFS Server Node], like local storage is accessed. We can easily setup a NFS system using linux packages like `nfs-kernel-server` and `nfs-common`.

In this guide, we are going to mount a hard disk on the NFS Server Node and share it over the network so that it can be accessed from the NFS Client Nodes. If you have more memory on a computer, you can share a specific folder on the computer over the network. You can also setup multiple NFS Servers to share data from multiple nodes on the network. Proceed with caution and follow the steps outlined below to setup the server and clients to access files over the network.

### [NFS Server Node] NFS Server Setup

Install the required packages:

```bash
$ sudo apt install nfs-kernel-server
```

Mount the hard disk that you want to share:

```bash
# First, create a mount point
$ sudo mkdir -p /mnt/shared_drive

# Mount the hard drive to the created location
# Assuming /dev/sdX1 is the drive you want to mount
$ sudo mount /dev/sdX1 /mnt/shared_drive
```

You can enquire about devices to be mounted using `lslbk` command and check the filesystem type and other details using the command `sudo lslbk -f` or `sudo blkid`

Export the directory by modifying the `/etc/exports` file:

```bash
# /etc/exports

# Add a line to share the mount point or directory

# To share over the whole network
/mnt/shared_drive 192.168.1.0/24(rw,sync,no_subtree_check)

# To share to specific nodes on the network
/mnt/shared_drive 192.168.1.0(rw,sync,no_subtree_check) 192.168.1.2(rw,sync,no_subtree_check)
```

Apply the changes and reload the server:

```bash
sudo exportfs -ra
sudo systemctl restart nfs-kernel-server
```

You can make the mount permanent by adding to the `/etc/fstab` file which is executed at each startup. Since we are using a hard drive which might not be always available, we'll keep it temporary.  
*NOTE: This means we have to mount the drive whenever we plug in the drive or if we switch drives.*

### [NFS Client Nodes] NFS Client Setup

Install the required packages:

```bash
$ sudo apt install nfs-common
```

Create a mount point:

```bash
$ sudo mkdir -p /mnt/shared_drive
```

Mount the export:

```bash
sudo mount node1:/mnt/shared_drive /mnt/shared_drive
```

To setup auto-mount on startup, edit the `/etc/fstab` file:

```bash
# /etc/fstab

node1:/mnt/shared_drive /mnt/shared_drive nfs defaults 0 0
```

Test the setup:

Try creating a file from one client and accessing it on other clients and the server

```bash
# On one client node
clusteruser@node2:~$ touch /mnt/shared_drive/testfile.txt

# On the server or any other client node
$ ls -la /mnt/shared_drive/
```

You may need to adjust permissions. Since we are using it in a private network behind a firewall and the data is not very confidential, we can have relaxed permissions like this:

```bash
$ sudo chmod 777 /mnt/shared_drive
```

To make it available to certain users/groups you can do this:

```bash
$ sudo chown clusteruser:clusteruser /mnt/shared_drive
```

That's it! We are setup to use our very own HPC cluster using a common network shared file system. Congratulations if this is your first time setting up a system like this and hope you have a great time using it!

## References

The above guide would not have been possible if not for these very well-detailed and well-written webpages,
- [Network File System - Wikipedia](https://en.wikipedia.org/wiki/Network_File_System)
- [Secure Shell - Wikipedia](https://en.wikipedia.org/wiki/Secure_Shell)
- [Slurm Workload Manager - Wikipedia](https://en.wikipedia.org/wiki/Slurm_Workload_Manager)
- [Private Network - Wikipedia](https://en.wikipedia.org/wiki/Private_network)
- [SSH Passwordless Setup Blogpost](https://www.strongdm.com/blog/ssh-passwordless-login)
- [Slurm for Dummies](http://github.com/SergioMEV/slurm-for-dummies)
- [Munge Documentation](https://dun.github.io/munge/)
- [Slurm Documentation](https://slurm.schedmd.com/documentation.html)
- [Slurm Quickstart Guide](https://slurm.schedmd.com/quickstart.html)

and countless google searches on these different topics