#### In this lab, I am going to migrate a simple web application from an on-premises environment into AWS usng the Database Migration Service.

## Provision the Environment
* First, I used the Cloudformation template provided by learn.cantrill.io to automate the building of some necessary resources.
![unnamed](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f86aa85e-be9d-4f09-aa60-2a36b6465837)

* The template created an EC2 hosting a web application, which I can view through its public IPv4 address.
![unnamed (1)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/a0e58012-c2ad-419d-95e7-c64ba4b2e6d3)


## Establish Private Connectivity
* In this part, I am going to establish a VPC peering connection between the AWS server and on-premises server.
Then I navigated to the VPC console and created a peering connection for the two VPCs, AWS and on-premises, that were generated using the template.
![unnamed (2)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/2401b592-7aee-4cdb-a1c2-9c88592eb595)

* Then I configured the on-premises public route table to be able to route traffic to AWS.
![unnamed (3)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/05b5dd82-6266-41c7-9eaa-606b284e4e35)

* Then I did the same process for the AWS public route table and AWS private route table.
![unnamed (4)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f947f344-ccf8-408f-980f-88fb8ec2856a)


## Create & Configure the AWS Side Infrastructure
* First I navigated to the RDS console and created a new subnet group inside the private subnets of the A4L VPC.
![unnamed (5)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d8ec0034-a3ed-4cf1-aea2-2790345ba11f)

* Then I created a new MariaDB database with the given master username and password.
![unnamed (6)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/5ebdc5c8-a900-4ece-9a31-761b4fc774e2)

* Then I navgiated to the EC2 console to create the instance that will be used for the web server.
* I created the instance inside the aws-publicA subnet of the awsVPC
![unnamed (7)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/669e9161-9b9a-4246-94fe-601edfcfc551)

* Then I connected to the instance using session manager installed the necessary packages.
````
sudo bash
yum -y install httpd mariadb
amazon-linux-extras install -y lamp-mariadb10.2-php7.2 php7.2 
````

* Then I started the httpd service
````
systemctl enable httpd
systemctl start httpd
````

* To use Secure copy for the data migration, I need to allow logins to this EC2 instance using SSH key pairs.
* So I changed the PasswordAuthentication to yes from no inside this file: 
````
nano /etc/ssh/sshd_config
````

* Then I changed the password and restarted the service:
````
passwd ec2-user
cats-dogs-rabbits-chickens

service sshd restart
````

* Then I connected to the on-premises instance and navigated to the webroot folder, and copied the wordpress local files from on-premises to AWS.
````
sudo bash
cd /var/www/
scp -rp html ec2-user@privateIPofawsCatWeb:/home/ec2-user
````

* Then I connected back to the AWS instance and copied the files inside the html folder to the webroot of awsCatWeb.
````
cd /home/ec2-user/html
cp * -R /var/www/html/
````
* Then I enforced the correct permissions on the files I just copied.
````
usermod -a -G apache ec2-user   
chown -R ec2-user:apache /var/www
chmod 2775 /var/www
find /var/www -type d -exec chmod 2775 {} \;
find /var/www -type f -exec chmod 0664 {} \;
sudo systemctl restart httpd
````

* Now if I connect to the aws instance, I can see that the page loads successfully, while the database is pointing to the on-premises database.


## Migrate Database & Cutover
* In this part, I will be migrating the database from the on-premises server into the RDS MariaDB instance using the database migration service.

* First I navigated to the DMS console, and created a subnet group inside the private subnets of awsVPC
![unnamed (8)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f5885457-d274-45af-a19b-fda92d06eb00)

* Then I created a replication instance with the default settings using the created subnet group inside the awsVPC.
![unnamed (9)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/3476f82d-12f7-4089-a1a6-6d45c47d50f2)

* Then I created a source endpoint for the on-premises database and used the private IP address of the CatDB instance for the server name.
* Then I also created the destination endpoint for the created RDS instance.
![unnamed (10)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/33497d69-fbbb-449c-8333-2b95e746bd70)

* Then I created a database migration task with a wizard schema and %a4lwordpress as the source name.
![unnamed (11)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6c6c99f9-efb5-419b-b46b-c5d6173cf812)

* Then I navigated back to the EC2 console and connected to the awsCatWeb instance using session manager, and ran these commands to cutover the application instance.
````
sudo bash
cd /var/www/html
nano wp-config.php
````
![unnamed (12)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/84d2dd1b-eba4-46ab-97f4-27847635a89a)

````
#!/bin/bash
source <(php -r 'require("/var/www/html/wp-config.php"); echo("DB_NAME=".DB_NAME."; DB_USER=".DB_USER."; DB_PASSWORD=".DB_PASSWORD."; DB_HOST=".DB_HOST); ')
SQL_COMMAND="mysql -u $DB_USER -h $DB_HOST -p$DB_PASSWORD $DB_NAME -e"
OLD_URL=$(mysql -u $DB_USER -h $DB_HOST -p$DB_PASSWORD $DB_NAME -e 'select option_value from wp_options where option_id = 1;' | grep http)
TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")
HOST=$(curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/public-ipv4)
$SQL_COMMAND "UPDATE wp_options SET option_value = replace(option_value, '$OLD_URL', 'http://$HOST') WHERE option_name = 'home' OR option_name = 'siteurl';"
$SQL_COMMAND "UPDATE wp_posts SET guid = replace(guid, '$OLD_URL','http://$HOST');"
$SQL_COMMAND "UPDATE wp_posts SET post_content = replace(post_content, '$OLD_URL', 'http://$HOST');"
$SQL_COMMAND "UPDATE wp_postmeta SET meta_value = replace(meta_value,'$OLD_URL','http://$HOST');"
````

* Then I stopped the on-premises instances and tried connecting to the public IP address of the AWS instance.
![unnamed (13)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/c31c1394-604d-4871-9d52-75e6ac7f4f7d)
* I can successfully connect to the instance, which means I successfully migrated the data from the on-premises server to the AWS instance.


### What I Learned
* When performing data migration, I need to create replication instances, source and target endpoints, and a task.
* I can simulate an on-premises using EC2 instances, and simulate migrating databases from those.
* I need to create subnet groups for the DMS too when migrating data.



