# Load Balancer and Backend EC2 Instances Setup

## Overview

This project demonstrates how to set up a simple architecture using AWS EC2 instances, where one EC2 instance acts as a load balancer, distributing incoming traffic to two backend EC2 instances.

## Architecture

- **Load Balancer EC2 Instance**: This instance receives all incoming requests and distributes them evenly to the backend instances.
- **Backend EC2 Instances**: These instances handle the actual processing of requests and respond to the load balancer.

## Prerequisites

- AWS account
- AWS CLI configured with the appropriate permissions
- Basic knowledge of AWS EC2 and networking

## Setup Instructions

### 1. Launch EC2 Instances

1. **Launch an EC2 instance**:
   - Configure security groups to allow HTTP (port 80) and SSH (port 22) access.
   - Choose an Amazon Machine Image (AMI), such as Ubuntu.
   - Choose an instance type (e.g., `t2.micro`).
   - Launch the EC2 instances.
   - SSH into them using the EC2 Instance Connect

2. **Install Nginx On all the three EC2 instances**:

   - **Execute the following commands 1 by 1**
   
   	```bash
	sudo su 
	```
	
	```bash
	apt update
	```
	 
   	```bash
	apt-get install nginx -y
	```
	
   	```bash
	systemctl enable nginx
	```
	
	```bash
	systemctl start nginx
	```
	
## Configure Nginx on both of the EC2 instances backend servers:

**Execute the following steps**

 1. **Execute the following Commands 1 by 1**
	
	```bash
	cd/
	```
	
	```bash
	cd var/www/html
	```
	
	```bash
	nano index.html
	```
	
 2. **Paste the following code in the index.html** 
 
 	```bash
 	<!DOCTYPE html>
		<html lang="en">
		<head>
		    <meta charset="UTF-8">
		    <meta name="viewport" content="width=device-width, initial-scale=1.0">
		    <title>Backend Server 1</title>
		</head>
		<body>
		    <h1>Backend Server 1</h1>
		</body>
	</html>
 	```
 	
 	press CRTL+x and then y
 	
 	**Note: for the backend server 2 change the h1 to Backend Server 2**
 	
 	```bash
	systemctl reload nginx
	```
 	
 	
  3. **Configure the Nginx server and run the following commands 1 by 1**

	cd

	cd/
	
	cd etc/nginx/sites-available
	 
	nano lb.conf
	
  Now write the following code in the lb.conf
  
	server {
	    listen 80;
	    listen [::]:80;

	    root /var/www/html;
	    index index.html;

	    server_name _;

	    location / {
		try_files $uri $uri/ =404;
	    }
	}

	
	press CRTL+x and then y
	
  4. **Execute the commands**
 
	ln -s /etc/nginx/sites-available/website.conf /etc/nginx/sites-enabled/
	
	systemctl reload nginx	
	
## Configure Nginx to distribute requests to the backend instances (add your backend instance IP addresses):

 1. **Execute the following Commands step by step
  
	```bash
	sudo su
	```
	
	```bash
	cd/
	```
	
	```bash
	cd etc/nginx/conf.d
	```
	 
	```bash
	nano lb.conf
	```

 Now write the following code in the lb.conf
	
	upstream backend {
		server <IP_ADDRESS_1>;
		server <IP_ADDRESS_2>;
		# add more backend servers as needed
	    }

	    server {
		listen 80;
		server_name http://<LOAD_BALANCER_IP_ADDRESS>;

		location / {
		    proxy_pass http://backend;
		}
	    }
	    
	    
 2. **Execute the command**

	```bash
	systemctl restart nginx
	```  

## Security Groups

Ensure that the security group attached to the load balancer allows inbound traffic on port 80 and allows outbound traffic to the backend instances. The backend instances should allow inbound traffic on port 80 from the load balancer's security group.

## Testing the Setup

Access the load balancer instance's public IP address in a web browser.
You should see responses alternating between the two backend instances, confirming that the load balancer is functioning correctly.

## Cleanup

To avoid incurring charges, remember to terminate all EC2 instances after testing.


