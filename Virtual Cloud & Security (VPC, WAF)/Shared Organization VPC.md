#### This section covers about setting up a shared VPC within an organization using Resource Access Manager.

## Shared Organization VPC
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
![unnamed (18)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/c5edb625-fffa-4dcc-b3cb-8640fce57ebb)

* The stack created a VPC with several subnets inside.
* Then I navigated to the resource access manager console and enabled sharing with AWS Organizations.
![unnamed (19)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/4f2da60f-8f6f-47e6-a403-e7662e49d008)

* Then I created a resource share and selected the subnet resources to be shared.
![unnamed (20)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b59a4f77-d7f4-43d7-b43f-56eeb121696a)

* Then I granted access to the PROD and DEV accounts in my AWS organization
![unnamed (21)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f8067b84-fb77-4a30-9cfa-9b82fc30f140)

* The resource share has been successfully created.
![unnamed (22)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/1c201cc0-58ca-466b-9214-89accd2a7eb0)

* Now if I switch to the PROD account, I can see the resource share shared with me:
![unnamed (23)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d7bd1951-254c-42fa-a881-101ed720d9cb)

* I can also see the shared subnets from the VPC console of the PROD account.
![unnamed (24)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ff04f00e-f02c-4eca-8526-a6bd27e2d6d1)
* There are no names configured, which means I can independently tag the shared resource

* Then I navigated to the EC2 console and launched an EC2 instance inside the public web subnet of the VPC using default settings and with this user data:
````
#!/bin/bash -xe

# System Updates
yum -y update
yum -y upgrade

yum -y install httpd wget
systemctl enable httpd
systemctl start httpd

# Install Wordpress
wget https://cl-sharedmedia.s3.amazonaws.com/genericcatpicwebsite/index.html -P /var/www/html
wget https://cl-sharedmedia.s3.amazonaws.com/genericcatpicwebsite/milky.jpeg -P /var/www/html
wget https://cl-sharedmedia.s3.amazonaws.com/genericcatpicwebsite/nori.jpeg -P /var/www/html
wget https://cl-sharedmedia.s3.amazonaws.com/genericcatpicwebsite/sushi.jpeg -P /var/www/html
cd /var/www/html

# permissions 
usermod -a -G apache ec2-user   
chown -R ec2-user:apache /var/www
chmod 2775 /var/www
find /var/www -type d -exec chmod 2775 {} \;
find /var/www -type f -exec chmod 0664 {} \;
````

* I opened the public IP address of the running instance and I can see that the instance is successfully working:
![unnamed (25)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/dd583718-7bbb-45a6-bb0f-c5cec79a2f1f)

* If I switch to a DEV account, I can view the shared resources, but I can’t see the running instance because resources created in particular accounts aren’t visible from other accounts.
