#### This section covers about creating a custom vpc and associating multi-tier VPC subnets. Then I will configure public subnets and a jumpbox.

## Creating Custom VPC
* First I went to the VPC console and created a new VPC:
![unnamed (13)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6e495436-c470-4c2e-bd7b-4ed53ad2e083)
![unnamed (14)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/762398a4-3be4-43e3-b5c9-9464e0ef30d6)

* Also from the VPC settings, I made sure that the DNS resolution and DNS hostnames are enabled.
![unnamed (15)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/701b46f9-e7b2-4e01-bce7-212b8c30a784)

## Implementing Multi-tier VPC Subnets
* From the subnets section, I am going to create a subnet in each availability zone.
* This is the subnet CIDR range, AZ, and IPv6 value configured from learn.cantrill.io that I will use for this lab.
NAME CIDR AZ CustomIPv6Value

sn-reserved-A 10.16.0.0/20 AZA IPv6 00  
sn-db-A 10.16.16.0/20 AZA IPv6 01  
sn-app-A 10.16.32.0/20 AZA IPv6 02  
sn-web-A 10.16.48.0/20 AZA IPv6 03  

sn-reserved-B 10.16.64.0/20 AZB IPv6 04  
sn-db-B 10.16.80.0/20 AZB IPv6 05  
sn-app-B 10.16.96.0/20 AZB IPv6 06  
sn-web-B 10.16.112.0/20 AZB IPv6 07  

sn-reserved-C 10.16.128.0/20 AZC IPv6 08  
sn-db-C 10.16.144.0/20 AZC IPv6 09  
sn-app-C 10.16.160.0/20 AZC IPv6 0A  
sn-web-C 10.16.176.0/20 AZC IPv6 0B  

* I chose the VPC created above for the VPC and adjusted the IPv4 subnet CIDR block for each subnet defined above.
* I manually created all subnets:
![unnamed (16)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d6f88545-96a6-4c9b-a836-95d1e9f01e5c)
* Then I went through each subnet setting and enabled auto assign IPv6 address.

## Configuring Public Subnets and Jumpbox
* In this section, I will make the three web subnets to be publicly accessible. For this, I need to configure an IGW and attach it to my VPC.

* I created the internet gateway but I need to manually attach them to my VPC.
![unnamed (17)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d57bcdc0-9724-4701-9890-658d30bd201d)
![unnamed (18)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/8851d161-c8f6-45bc-bcc1-942bed9b0134)

* To configure the web subnets to successfully communicate with the public internet, we need to first configure the route table.
![unnamed (19)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/7f339f62-c07b-4ac9-a9f4-829dc2b5c8ab)
![unnamed (20)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/5d6c0634-bd17-4a39-9c5a-468b966af7c1)

* Now I need to edit the subnet associations for the route table. If I don’t edit it, the subnets are routed to the main route table by default.
![unnamed (21)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/05a216bd-b0cf-4ecf-802d-953bc376b13a)
![unnamed (22)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ec67d9fd-a4e2-4dec-aba0-5e000e6efded)

* I also edited routes so that any IP that doesn’t destine to any specific CIDR range routes to the internet gateway.
![unnamed (23)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/7cfa95a4-0a56-412c-9ff2-a14757647bd6)
![unnamed (24)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/14ecf5ae-16d7-4b85-a948-ea0bb37396e3)
* Then for each web subnet, I enabled auto-assign public IPv4 address

* Now to test the configuration, I am going to launch an EC2 instance inside the web subnets.
* I created a new EC2 instance with everything default except network settings to be A4L VPC and to be in the sn-web-A subnet.
![unnamed (25)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/a6aec819-9c93-46c4-8b45-5fed553642c1)

* Now I can connect to the EC2 instance using SSH from my local computer.
![unnamed (26)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9dbffce8-228a-4811-8c51-83ba16adea0b)

### What I Learned
* I need to enable auto-assign public IPv4 address so that IP addresses can be routed to the public through the internet gateway.
* I can manually enter the CIDR range that I want to use for each subnet.

