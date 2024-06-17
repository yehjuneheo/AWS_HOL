#### This section covers about creating a custom vpc and associating multi-tier VPC subnets. Then I will configure public subnets and a jumpbox along with private internet access using NAT Gateway.

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





