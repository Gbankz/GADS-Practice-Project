# GADS-Practice-Project
  This repository shows all the labs and lab translations i did in fulfillment of the requirements for GADS practice project for the cloud track

  >LabScreenshots/Console and CloudShell.PNG
  
  >Lab Screenshots/Create a Streaming Data Pipeline for a Real-Time Dashboard with Cloud Dataflow.PNG

  >Lab Screenshots/Creating Virtual Machines.PNG

  >Lab Screenshots/Getting started with GKE.PNG

  >Lab Screenshots/Getting started with app engine.PNG

  >Lab Screenshots/Getting started with cloud marketplace.PNG

  >Lab Screenshots/Getting started with cloud storage and cloud SQL.PNG

  >Lab Screenshots/Getting started with compute engine.PNG

  >Lab Screenshots/Implement Private Google Access and Cloud NAT.PNG

  >Lab Screenshots/Infrastructure Preview.PNG

  >Lab Screenshots/VPC Networking.PNG
  

## LAB TRANSLATIONS

# Lab 1: Essential Google Cloud Infrastructure Foundation: Creating Virtual Machines

## Objectives

    In this lab, you explore the available options for VMs and see the differences between locations.

    In this lab, you learn how to perform the following tasks:

 * Create several standard VMs

 * Create advanced VMs
        
## Steps:

1. **Create a utility virtual machine**

        gcloud compute instances create banks-vm --zone=us-central1-c --machine-type n1-standard-1 \
        --image-project debian-cloud --image debian-9-stretch-v20200902 --subnet default --no-address

2. **Create a Windows virtual machine**
      > To allow HTTP traffic and Allow HTTPS traffic, firewall rules are created along side the virtual machine instance    

        gcloud beta compute instances create bankswin-vm --zone=europe-west2-a --machine-type=n1-standard-2 \
        --subnet=default --image=windows-server-2016-dc-core-v20200813 \
        --image-project=windows-cloud --boot-disk-size=100GB --boot-disk-type=pd-standard \
  
        gcloud compute firewall-rules create default-allow-http \
        --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server \
    
        gcloud compute firewall-rules create default-allow-https --direction=INGRESS \
        --priority=1000 --network=default --action=ALLOW --rules=tcp:443 --source-ranges=0.0.0.0/0 --target-tags=https-server

 3. **Create a custom Virtual Machines**
 
        gcloud beta compute instances create custom-vm --zone=us-west1-b --machine-type=custom-6-32768 --subnet=default \
        --image=debian-9-stretch-v20200902 --image-project=debian-cloud --boot-disk-size=10GB 

       **Connect via SSH to your custom VM**
       
       >In this lab, the external ip of the custom-vm cannot be accessed publicly, because http and https traffic isn't enabled.
        ssh into custom-vm by clicking ssh on the console

       - To view information on unused and used memory and swap space on your custom-vm
         
         __free__

       - To view details of RAM installed on your VM, run the following command:
         
         __sudo dmidecode -t 17__

       - check the number of processors, run the following command:
        
         __nproc__

       - To see information of CPUs installed on your VM, run the following command:
         
         __lscpu__
        
       - To exit the SSH terminal, run the following command: 
       
         __exit__ 

# Lab 2: Essential Google Cloud Infrastructure Foundation: VPC Networking
## Objectives

In this lab, you learn how to perform the following tasks:

- Explore the default VPC network
- Create an auto mode network with firewall rules
- Convert an auto mode network to a custom mode network
- Create custom mode VPC networks with firewall rules
- Create VM instances using Compute Engine
- Explore the connectivity for VM instances across VPC networks

## Steps:

1. **Explore the default network**

    - View the Subnets
        gcloud compute networks subnets list --network=default

    - View the routes
        gcloud compute routes list

    - View the firewall rules
        gcloud compute firewall-rules list

    - Delete the Firewall rules
        gcloud compute firewall-rules delete \
        default-allow-icmp \
        default-allow-internal \
        default-allow-rdp \
        default-allow-ssh

    - Delete the default network
        gcloud compute networks delete default

        To confirm VPC network deletion, check to see if there are any routes. Notice that there are no routes.
        gcloud compute routes list
        
        Also check the Firewall rules. Notice that there are no firewall rules.
        gcloud compute firewall-rules list

    - Try to create a VM instance
        gcloud compute instances create "test-vm" \
        --machine-type "n1-standard-1" \
        --image-project "debian-cloud" \
        --image "debian-9-stretch-v20190213" \
        --subnet "default" --tags http 
        
        An error message is displayed
        As expected, you cannot create a VM instance without a VPC network!


2. **Create an auto mode network**
    - Create VPC network
        gcloud compute networks create mynetwork --subnet-mode=auto
    - Create Firewalls
        gcloud compute --project=spheric-handler-289112 firewall-rules create mynetwork-icmp-rdp-ssh-all \
        --direction=INGRESS --priority=1000 --network=mynetwork --action=ALLOW --rules=tcp:22,tcp:3389,icmp \
        --source-ranges=0.0.0.0/24,10.128.0.0/9

    - Create a VM instance in us-central1
        gcloud compute instances create mynet-us-vm --zone=us-central1-c --machine-type n1-standard-1 \
        --image-project debian-cloud --image debian-9-stretch-v20200902 --subnet mynetwork

    - Create a VM instance in europe-west1
        gcloud compute instances create mynet-eu-vm --zone=europe-west1-c --machine-type n1-standard-1 \
        --image-project debian-cloud --image debian-9-stretch-v20200902 --subnet mynetwork
        
    - Verify connectivity for the VM instances
        Use the ping command to confirm that mynet-us-vm can reach mynet-eu-vm over the network:

        * Connect to mynet-us-vm, via ssh:
        gcloud beta compute ssh --zone "us-central1-c" "mynet-us-vm" 

        * To test connectivity to mynet-eu-vm's internal IP, run the following command, replacing mynet-eu-vm's internal IP:
        ping -c 3 10.132.0.2

        * Repeat the same test by running the following:
        ping -c 3 mynet-eu-vm

        * To test connectivity to mynet-eu-vm's external IP, run the following command, replacing mynet-eu-vm's external IP:
        ping -c 3 

    - Convert the network to a custom mode network
        gcloud compute networks update mynetwork --switch-to-custom-subnet-mode

3. **Create custom mode networks**
    - Create the managementnet network
        gcloud compute networks create managementnet --subnet-mode=custom \
        gcloud compute networks subnets create managementsubnet-us --range=10.130.0.0/20 --network=managementnet --region=us-central1 \

    - Create the privatenet network
        * To create the privatenet network, run the following command:
            gcloud compute networks create privatenet --subnet-mode=custom

        * To create the privatesubnet-us subnet, run the following command:
            gcloud compute networks subnets create privatesubnet-us --network=privatenet --region=us-central1 --range=172.16.0.0/24

        * To create the privatesubnet-eu subnet, run the following command:
            gcloud compute networks subnets create privatesubnet-eu --network=privatenet --region=europe-west1 --range=172.20.0.0/20

        * To list the available VPC networks, run the following command:
            gcloud compute networks list

        * To list the available VPC subnets (sorted by VPC network), run the following command:
            gcloud compute networks subnets list --sort-by=NETWORK

    - Create the firewall rules for managementnet
        gcloud compute firewall-rules create managementnet-allow-icmp-ssh-rdp --direction=INGRESS --priority=1000 \
        --network=managementnet --action=ALLOW --rules=tcp:22,tcp:3389,icmp --source-ranges=0.0.0.0/0

    - Create the firewall rules for privatenet
        * To create the privatenet-allow-icmp-ssh-rdp firewall rule, run the following command:
        gcloud compute firewall-rules create privatenet-allow-icmp-ssh-rdp \
        --direction=INGRESS --priority=1000 --network=privatenet --action=ALLOW --rules=icmp,tcp:22,tcp:3389 --source-ranges=0.0.0.0/0

        * To list all the firewall rules (sorted by VPC network), run the following command:
        gcloud compute firewall-rules list --sort-by=NETWORK

    - Create two VM instances:

        1. managementnet-us-vm in managementsubnet-us

        2. privatenet-us-vm in privatesubnet-us

        * Create the managementnet-us-vm instance
        gcloud compute instances create managementnet-us-vm --zone=us-central1-c --machine-type=f1-micro --subnet=managementsubnet-us

        * Create the privatenet-us-vm instance
        gcloud compute instances create privatenet-us-vm --zone=us-central1-c --machine-type=f1-micro --subnet=privatesubnet-us

        * To list all the VM instances (sorted by zone), run the following command:
        gcloud compute instances list --sort-by=ZONE

4. **Explore the connectivity across networks**

    - Ping the external IP addresses
        * To test connectivity to mynet-eu-vm's external IP, run the following command, replacing mynet-eu-vm's external IP:
        ping -c 3 35.205.75.88
       
        * To test connectivity to managementnet-us-vm's external IP, run the following command, replacing managementnet-us-vm's external IP:
        ping -c 3 34.66.128.117
        
        * To test connectivity to privatenet-us-vm's external IP, run the following command, replacing privatenet-us-vm's external IP:
        ping -c 3 35.226.74.236

    - Return to the SSH terminal for mynet-us-vm.

        * To test connectivity to mynet-eu-vm's internal IP, run the following command, replacing mynet-eu-vm's internal IP:
        ping -c 3 10.132.0.2

        * To test connectivity to managementnet-us-vm's internal IP, run the following command, replacing managementnet-us-vm's internal IP:
        ping -c 3 35.226.74.236

        * To test connectivity to privatenet-us-vm's internal IP, run the following command, replacing privatenet-us-vm's internal IP:
        ping -c 3 172.16.0.2

        gcloud compute ssh 10.128.0.2 --zone=us-central1-c
