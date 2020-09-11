# GADS-Practice-Project
  This repository shows all the labs and lab translations i did in fulfillment of the requirements for GADS practice project for the cloud track

  >(https://raw.github.com/Gbankz/GADS-Practice-Project/LabScreenshots/ConsoleandCloudShell.PNG)
  >Lab Screenshots/Console and Cloud Shell.PNG

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

