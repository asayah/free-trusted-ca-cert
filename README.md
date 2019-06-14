# Introduction
This page illustrate how to create a reusable trusted letsencrypt CA certificate for NoIP.com DNS.

# Prerequisites
In this tutorial we will need a server, for allowing letsencrypt to issue a certificate for us, We will use a free tier AWS EC2 instance, start by creating a account on aws (we will use only the free tier, no worries) 

# Create an EC2 Instance
- On your AWS Console, go to EC2, then click on **Launch Instance**
- Create an EC2 instance of type **Amazon Linux 2 AMI (HVM)**
- Select **t2.micro** for the instace size (pick the free tier one) 
- Select a key (or create it)  then launch the instance (save the key locally, it is important for the rest of the tutorial). 
- Go back to EC2, on the list of the instances, identify the instance have just created, in its description, identify the **security group** attached to it and update it to **allow all inbound traffic** on port 80. 

![list of ec2 instances, identify security group in the instance description](http://i68.tinypic.com/a88yf.png)
- Edit the security group

![edit security group](http://i65.tinypic.com/2e1cd8n.png)

- allow all traffic 

![allow all inbound traffic](http://i67.tinypic.com/2cikadi.png)

- click on the instance you have created, and locate its Public DNS

![public DNS](http://i65.tinypic.com/10zxndg.png)


# Setting UP DNS
- create a free https://www.noip.com/ . 
- On your management console, go to MY Services -> DNS Records, and setup your dns to point to the public DNS of the ec2 instance we have created in the previous step (Manage your noIp DNS and create a CNAME that point to the EC2 Public DNS)

# Generating the CA Certificate
- Connect to your EC2 instance, you can find the istructions by clicking on connect in AWS console (EC2)

![connect](http://i63.tinypic.com/213otnq.png)

- Install Certbot

```Shell
yum -y install yum-utils
yum-config-manager --enable rhui-REGION-rhel-server-extras rhui-REGION-rhel-server-optional
sudo yum install certbot
```
- Now lets generate the certrificate

```Shell
sudo certbot certonly --standalone -d example.com --config-dir `pwd` #replace example.com by your noIp DNS

# Because we want to download the certificate, change their permissions
sudo cp /home/ubuntu/live/exemple.com/fullchain.pem .
sudo cp /home/ubuntu/live/exemple.com/privkey.pem .
sudo chmod 777 fullchain.pem privkey.pem
```

# Download the certificates
```Shell
scp -i YOUR_AWS_KEY ec2-user@YOUR_DNS_NAME:privkey.pem .
scp -i YOUR_AWS_KEY ec2-user@YOUR_DNS_NAME:fullchain.pem .
```
You can delete your EC2 instance now if you don't need it. 

Enjoy your CA !!!

Learn more about APIC Central and service Mesh governance: https://github.com/Axway/Setup-Amplify-Mesh-Governance/wiki/ 
