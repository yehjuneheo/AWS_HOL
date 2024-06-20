#### In this project, I will start with a monolithic web application, migrate the database to RDS, provision an EFS file system, and finally provision an auto-scaling group with an elastic load balancer.

## Monolithic Application
* In this section, I will first create a monolithic web application, where the application and database is running on one EC2 instance.
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.

* Then I launched an EC2 Instance for WordPress with default settings inside the A4LVPC sn-pub-A subnet.
![unnamed (13)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d0d1c9e2-ce48-43b8-9700-ec9fa57debe9)
* In the advanced settings, I configured A4LVPC-WordpressInstanceProfile for the IAM instance profile

* While the instance was launching, I went into the Parameter Store and created 5 parameters: DBUser, DBName, DBEndpoint, DBPassword, and DBRootPassword
* All the parameters have string types except for passwords that are encrypted using a KMS key.
![unnamed (14)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e4cdbc14-9052-4d48-83dc-0878a9854e0e)

* Then I connected to the EC2 instance using a session manager and configured the parameters as variables using these commands:
````
DBPassword=$(aws ssm get-parameters --region us-east-1 --names /A4L/Wordpress/DBPassword --with-decryption --query Parameters[0].Value)
DBPassword=`echo $DBPassword | sed -e 's/^"//' -e 's/"$//'`

DBRootPassword=$(aws ssm get-parameters --region us-east-1 --names /A4L/Wordpress/DBRootPassword --with-decryption --query Parameters[0].Value)
DBRootPassword=`echo $DBRootPassword | sed -e 's/^"//' -e 's/"$//'`

DBUser=$(aws ssm get-parameters --region us-east-1 --names /A4L/Wordpress/DBUser --query Parameters[0].Value)
DBUser=`echo $DBUser | sed -e 's/^"//' -e 's/"$//'`

DBName=$(aws ssm get-parameters --region us-east-1 --names /A4L/Wordpress/DBName --query Parameters[0].Value)
DBName=`echo $DBName | sed -e 's/^"//' -e 's/"$//'`

DBEndpoint=$(aws ssm get-parameters --region us-east-1 --names /A4L/Wordpress/DBEndpoint --query Parameters[0].Value)
DBEndpoint=`echo $DBEndpoint | sed -e 's/^"//' -e 's/"$//'`
````

* Then I installed the updates:
````
sudo dnf -y update
````

* Then I installed the pre-requisite packages and web server
````
sudo dnf install wget php-mysqlnd httpd php-fpm php-mysqli mariadb105-server php-json php php-devel stress -y
````

* Then I set the DB and HTTP server to run and start by default
````
sudo systemctl enable httpd
sudo systemctl enable mariadb
sudo systemctl start httpd
sudo systemctl start mariadb
````

* Then I set the MariaDB root password
````
sudo mysqladmin -u root password $DBRootPassword
````

* Then I downloaded and extracted WordPress
````
sudo wget http://wordpress.org/latest.tar.gz -P /var/www/html
cd /var/www/html
sudo tar -zxvf latest.tar.gz
sudo cp -rvf wordpress/* .
sudo rm -R wordpress
sudo rm latest.tar.gz
````

* Then I configured the wordpress wp-config.php file
````
sudo cp ./wp-config-sample.php ./wp-config.php
sudo sed -i "s/'database_name_here'/'$DBName'/g" wp-config.php
sudo sed -i "s/'username_here'/'$DBUser'/g" wp-config.php
sudo sed -i "s/'password_here'/'$DBPassword'/g" wp-config.php
````

* Then I fixed the permissions on the file system.
````
sudo usermod -a -G apache ec2-user   
sudo chown -R ec2-user:apache /var/www
sudo chmod 2775 /var/www
sudo find /var/www -type d -exec chmod 2775 {} \;
sudo find /var/www -type f -exec chmod 0664 {} \;
````

* Then I created the WordPress User, set its password, create the database and configure permissions
````
sudo echo "CREATE DATABASE $DBName;" >> /tmp/db.setup
sudo echo "CREATE USER '$DBUser'@'localhost' IDENTIFIED BY '$DBPassword';" >> /tmp/db.setup
sudo echo "GRANT ALL ON $DBName.* TO '$DBUser'@'localhost';" >> /tmp/db.setup
sudo echo "FLUSH PRIVILEGES;" >> /tmp/db.setup
sudo mysql -u root --password=$DBRootPassword < /tmp/db.setup
sudo rm /tmp/db.setup
````

* Now the configuration is complete, I can view the WordPress hosting on the public IPv4 address:
![unnamed (15)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/389a152a-fb86-41f9-9ad0-22fee92c564f)
![unnamed (16)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f660d159-451f-4e6e-850f-d128bb979fba)

* Now I created a new post with images and published it, so that the images are uploaded into a local image store and the metadata for the post is stored locally in MariaDB database.
![unnamed (17)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/fb521882-78de-40e0-8ea8-3906e773c31d)

* Currently, the website and database are inside one EC2 instance, which doesn’t support automatic scaling or high availability for both application and database. 
* Also, EC2 instances don’t support static IPs by default and the IP address is hardcoded inside the instance, which means if the instance is stopped and started, the IP address changes and I can’t access the WordPress website.
* This is strongly not recommended for a service in production mode.


## Launch Templates
* In this section, I will improve the auto scaling ability of the previous architecture, by creating a launch template to automate the process of launching new instances.
* First I created a launch template with Amazon Linux AMI and t2.micro as the instance type
![unnamed (18)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f904c2de-b720-4d5d-ba5e-b58e1235d2bb)
* I also added the A4LVPC-SGWordpress for the security group and added the A4LWPC-WordpressInstanceProfile as the IAM instance profile

* Then for the user data, I used the commands provided by learn.cantrill.io, which are the same ones in the previous section:
````
#!/bin/bash -xe

DBPassword=$(aws ssm get-parameters --region us-east-1 --names /A4L/Wordpress/DBPassword --with-decryption --query Parameters[0].Value)
DBPassword=`echo $DBPassword | sed -e 's/^"//' -e 's/"$//'`

DBRootPassword=$(aws ssm get-parameters --region us-east-1 --names /A4L/Wordpress/DBRootPassword --with-decryption --query Parameters[0].Value)
DBRootPassword=`echo $DBRootPassword | sed -e 's/^"//' -e 's/"$//'`

DBUser=$(aws ssm get-parameters --region us-east-1 --names /A4L/Wordpress/DBUser --query Parameters[0].Value)
DBUser=`echo $DBUser | sed -e 's/^"//' -e 's/"$//'`

DBName=$(aws ssm get-parameters --region us-east-1 --names /A4L/Wordpress/DBName --query Parameters[0].Value)
DBName=`echo $DBName | sed -e 's/^"//' -e 's/"$//'`

DBEndpoint=$(aws ssm get-parameters --region us-east-1 --names /A4L/Wordpress/DBEndpoint --query Parameters[0].Value)
DBEndpoint=`echo $DBEndpoint | sed -e 's/^"//' -e 's/"$//'`

dnf -y update

dnf install wget php-mysqlnd httpd php-fpm php-mysqli mariadb105-server php-json php php-devel stress -y

systemctl enable httpd
systemctl enable mariadb
systemctl start httpd
systemctl start mariadb

mysqladmin -u root password $DBRootPassword

wget http://wordpress.org/latest.tar.gz -P /var/www/html
cd /var/www/html
tar -zxvf latest.tar.gz
cp -rvf wordpress/* .
rm -R wordpress
rm latest.tar.gz

sudo cp ./wp-config-sample.php ./wp-config.php
sed -i "s/'database_name_here'/'$DBName'/g" wp-config.php
sed -i "s/'username_here'/'$DBUser'/g" wp-config.php
sed -i "s/'password_here'/'$DBPassword'/g" wp-config.php
sed -i "s/'localhost'/'$DBEndpoint'/g" wp-config.php

usermod -a -G apache ec2-user   
chown -R ec2-user:apache /var/www
chmod 2775 /var/www
find /var/www -type d -exec chmod 2775 {} \;
find /var/www -type f -exec chmod 0664 {} \;

echo "CREATE DATABASE $DBName;" >> /tmp/db.setup
echo "CREATE USER '$DBUser'@'localhost' IDENTIFIED BY '$DBPassword';" >> /tmp/db.setup
echo "GRANT ALL ON $DBName.* TO '$DBUser'@'localhost';" >> /tmp/db.setup
echo "FLUSH PRIVILEGES;" >> /tmp/db.setup
mysql -u root --password=$DBRootPassword < /tmp/db.setup
rm /tmp/db.setup
````

* The launch template was successfully created and launched an instance from it with only configuring the subnet to be sn-pub-A:
![unnamed (19)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f7d84994-17ab-48a2-a8fb-edc7cc1bb84c)
![unnamed (20)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e380b3fa-3735-410f-a201-a0ebe5448cc6)

* Then I went into the public IPv4 address and installed WordPress, and created a post and published it.
![unnamed (22)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/a58fa49f-5912-422f-901b-59a52e982b30)

* Although the EC2 launching process is automated, the architecture still lacks in high availability and auto scaling issues. Also, the IP address is still hardcoded into the instances.


## Database Migration to RDS
* In this section, I will be migrating the database from the EC2 instance into a separate RDS instance, which means each of these can scale independently. Also, this allows data in the database to live past the lifecycle of the EC2 instance.

* First, from the RDS console, I created a subnet group so that RDS instances can select between multiple availability zones to be deployed to.
* I made the subnet group inside the A4L VPC and selected us-east-1a, us-east-1b, us-east-1c as the availability zones.
![unnamed (23)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/004675d0-5adf-4546-8a5c-3d47fd9287ef)

* Then I created a new MySQL database inside the A4L VPC and A4LVPC-SGDatabase security group attached.
* For the database master user, master password, and database name, I used the values stored in the parameter store.
![unnamed (48)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/a62bd7b1-36c1-44b8-9eb2-4082c4457931)

* After the database is created, I connected to the EC2 instance using session manager to migrate the database.
* Then I populated the environment variables using these commands:
````
DBPassword=$(aws ssm get-parameters --region us-east-1 --names /A4L/Wordpress/DBPassword --with-decryption --query Parameters[0].Value)
DBPassword=`echo $DBPassword | sed -e 's/^"//' -e 's/"$//'`

DBRootPassword=$(aws ssm get-parameters --region us-east-1 --names /A4L/Wordpress/DBRootPassword --with-decryption --query Parameters[0].Value)
DBRootPassword=`echo $DBRootPassword | sed -e 's/^"//' -e 's/"$//'`

DBUser=$(aws ssm get-parameters --region us-east-1 --names /A4L/Wordpress/DBUser --query Parameters[0].Value)
DBUser=`echo $DBUser | sed -e 's/^"//' -e 's/"$//'`

DBName=$(aws ssm get-parameters --region us-east-1 --names /A4L/Wordpress/DBName --query Parameters[0].Value)
DBName=`echo $DBName | sed -e 's/^"//' -e 's/"$//'`

DBEndpoint=$(aws ssm get-parameters --region us-east-1 --names /A4L/Wordpress/DBEndpoint --query Parameters[0].Value)
DBEndpoint=`echo $DBEndpoint | sed -e 's/^"//' -e 's/"$//'`
````

* Then I took a backup of the current database.
````
mysqldump -h $DBEndpoint -u $DBUser -p$DBPassword $DBName > a4lWordPress.sql
````

* Then I went back to the RDS console and copied the RDS instance endpoint: a4lwordpress.c7sqc8wqs8hx.us-east-1.rds.amazonaws.com
* Then I deleted the DBEndpoint and created the same one with the new endpoint as the value.
* Then I went back to the session manager and updated the DBEnpoint variable:
````
DBEndpoint=$(aws ssm get-parameters --region us-east-1 --names /A4L/Wordpress/DBEndpoint --query Parameters[0].Value)
DBEndpoint=`echo $DBEndpoint | sed -e 's/^"//' -e 's/"$//'`
````

* Then I restored the database export into RDS.
````
mysql -h $DBEndpoint -u $DBUser -p$DBPassword $DBName < a4lWordPress.sql 
````

* Then changed the WordPress config file to use RDS and stopped the MariraDB service.
````
sudo sed -i "s/'localhost'/'$DBEndpoint'/g" /var/www/html/wp-config.php
sudo systemctl disable mariadb
sudo systemctl stop mariadb
````

* Now if I refresh the blog post, I can still see it, but the data is coming from the RDS instance.
![unnamed (24)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/93789ece-e268-412e-bb72-098b8e2fae69)

* Now I need to update the launch template to launch additional EC2 instances using this new configuration.
* Since I am modifying an existing launch template, all of the settings are pre-populated so I just need to modify the user data to use RDS instance.
* I removed the following lines from the user data:
````
systemctl enable mariadb
systemctl start mariadb
mysqladmin -u root password $DBRootPassword


echo "CREATE DATABASE $DBName;" >> /tmp/db.setup
echo "CREATE USER '$DBUser'@'localhost' IDENTIFIED BY '$DBPassword';" >> /tmp/db.setup
echo "GRANT ALL ON $DBName.* TO '$DBUser'@'localhost';" >> /tmp/db.setup
echo "FLUSH PRIVILEGES;" >> /tmp/db.setup
mysql -u root --password=$DBRootPassword < /tmp/db.setup
rm /tmp/db.setup
````

* Then I set version 2 as the default version so that his version is used by default when creating new instances.

* Now this architecture allows me to scale the application and the database separately, which is a better configuration in terms of scaling than using both of them in a single EC2 instance.
* However, the WordPress content still resides in a folder local to the EC2 instance so we need to migrate this out to scale the instances in and out without risking the data, and also the customers are still connecting directly to the instance so we need to resolve it by using a load balancer. Also the IP address is still hardcoded into the database.


## Migrate WordPress File System into EFS
* In this section, I will split out the local WordPress file system inside the EC2 instance into EFS, so that the files are independent from the instance lifecycles.

* First I navigated to the EFS console and created a new customized file system with bursting throughput mode and disabled encryption at rest.
* For the network settings, I chose the A4L VPC and the application subnets within each availability zones. I also added A4LVPC-SGEFS to each.
![unnamed (25)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/3e72f8e6-db55-44b0-962d-fcbfb609c472)

* The file system has been created successfully along with the mount targets.
![unnamed (26)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/1594dd2c-dbfb-4151-aa05-762e4af75fd3)
![unnamed (27)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/fd2f4c29-ac99-4c0a-bddc-a6ddce05d582)

* Then I created new parameter for the EFS ID: fs-0a3dd6fcf69f4594e

* Then I went to the EC2 console and connected to the instance using session manager.
* In order to allow EC2 to use EFS, I need to install an additional package:
````
sudo dnf -y install amazon-efs-utils
````

* Then I am going to move the contents in the wp-content folder to a temporary location.
````
cd /var/www/html
sudo mv wp-content/ /tmp
sudo mkdir wp-content
````

* Then I stored the EFS file system ID from parameter store.
````
EFSFSID=$(aws ssm get-parameters --region us-east-1 --names /A4L/Wordpress/EFSFSID --query Parameters[0].Value)
EFSFSID=`echo $EFSFSID | sed -e 's/^"//' -e 's/"$//'`
````

* Then I added a line to /etc/fstab to configure the EFS file system to mount as /var/www/html/wp-content/
````
echo -e "$EFSFSID:/ /var/www/html/wp-content efs _netdev,tls,iam 0 0" >> /etc/fstab
mount -a -t efs defaults
````

* Then I copied the origin content data back in and fixed the permissions.
````
mv /tmp/wp-content/* /var/www/html/wp-content/
chown -R ec2-user:apache /var/www/
````

* Then I rebooted the instance to test if the WordPress instance can load the media, and I still the EFS file system mounted in to the directory:
![unnamed (28)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/70eb04e3-f660-46df-9671-be3d1e09e8c0)

* Also even if I refresh the website, the website displays the correct post and images:
![unnamed (29)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/0dc435c2-fe3a-4dc9-bf08-07d406b003da)

* Then I updated the launch template to include these commands:
````
EFSFSID=$(aws ssm get-parameters --region us-east-1 --names /A4L/Wordpress/EFSFSID --query Parameters[0].Value)
EFSFSID=`echo $EFSFSID | sed -e 's/^"//' -e 's/"$//'`

dnf install wget php-mysqlnd httpd php-fpm php-mysqli mariadb105-server php-json php php-devel stress amazon-efs-utils -y

mkdir -p /var/www/html/wp-content
chown -R ec2-user:apache /var/www/
echo -e "$EFSFSID:/ /var/www/html/wp-content efs _netdev,tls,iam 0 0" >> /etc/fstab
mount -a -t efs defaults
````

* Then I made this version to be the default verion of the launch template.

* Now I have separated the database and the file system separated from the EC2 instance, which allows it to freely scale out or in
* However, the customers are still connecting to the instance directly, and we don’t have any health checks or auto healing capabilities.
* Also the IP address is stilled hardcoded into the database, which neds improvements.


## Enable Elasticity via ASG & ALB
* This section creates an auto scaling group and an application load balancer to allow elastic scaling and self-healing of the EC2 instances.

* First I created an application load balancer that is internet-facing 
![unnamed (30)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/c335fcc9-fef3-4739-8280-410969008990)

* For the network settings, I chose the three availability zones and selected the public subnet for each.
![unnamed (31)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b80c8bb0-8fd0-45d5-975a-a76e5b5e5193)

* Then I selected the A4LVPC-SGLoadBalancer and created a new instance target group
* I didn’t add any instances to the target group, as it can be integrated with an auto scaling group.
![unnamed (32)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/c84df43f-aecb-4822-aa88-c9543d5c647c)

* Then I copied the DNS name of the load balancer to create a parameter in the parameter store
![unnamed (33)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/879a6583-b768-44f7-b477-e31db62fb291)

* Now I need to update the launch template to use the DNS name of the load balancer instead of IP addresses, since new EC2 instances have new IP addresses, which can’t be used from the launch template without hardcoding it.
* So I modified the launch template to include these lines:
````
ALBDNSNAME=$(aws ssm get-parameters --region us-east-1 --names /A4L/Wordpress/ALBDNSNAME --query Parameters[0].Value)
ALBDNSNAME=`echo $ALBDNSNAME | sed -e 's/^"//' -e 's/"$//'`

cat >> /home/ec2-user/update_wp_ip.sh<< 'EOF'
#!/bin/bash
source <(php -r 'require("/var/www/html/wp-config.php"); echo("DB_NAME=".DB_NAME."; DB_USER=".DB_USER."; DB_PASSWORD=".DB_PASSWORD."; DB_HOST=".DB_HOST); ')
SQL_COMMAND="mysql -u $DB_USER -h $DB_HOST -p$DB_PASSWORD $DB_NAME -e"
OLD_URL=$(mysql -u $DB_USER -h $DB_HOST -p$DB_PASSWORD $DB_NAME -e 'select option_value from wp_options where option_id = 1;' | grep http)

ALBDNSNAME=$(aws ssm get-parameters --region us-east-1 --names /A4L/Wordpress/ALBDNSNAME --query Parameters[0].Value)
ALBDNSNAME=`echo $ALBDNSNAME | sed -e 's/^"//' -e 's/"$//'`

$SQL_COMMAND "UPDATE wp_options SET option_value = replace(option_value, '$OLD_URL', 'http://$ALBDNSNAME') WHERE option_name = 'home' OR option_name = 'siteurl';"
$SQL_COMMAND "UPDATE wp_posts SET guid = replace(guid, '$OLD_URL','http://$ALBDNSNAME');"
$SQL_COMMAND "UPDATE wp_posts SET post_content = replace(post_content, '$OLD_URL', 'http://$ALBDNSNAME');"
$SQL_COMMAND "UPDATE wp_postmeta SET meta_value = replace(meta_value,'$OLD_URL','http://$ALBDNSNAME');"
EOF

chmod 755 /home/ec2-user/update_wp_ip.sh
echo "/home/ec2-user/update_wp_ip.sh" >> /etc/rc.local
/home/ec2-user/update_wp_ip.sh
````

* Then I set this vererion to be the default version.

* Then I created a new auto scaling group for the launch template
![unnamed (34)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/55cd4d40-b3c5-46ca-8f50-ce6b56cd31a9)

* I chose the A4L VPC and selected the corresponding subnets.
![unnamed (35)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b7f12780-e02a-476f-8cf7-657db6cc6a56)

* Then I attached it to my application load balancer and turned on ELB health checks.
* I chose the desired, minimum, and maximum capacity to be 1 and didn’t configure any scaling policies.
* Then I added a name tag with Wordpress-ASG to know what instances were created from this ASG.
![unnamed (36)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9210ee7a-dffc-4f11-ba44-59752f184ba8)

* Then I terminated the Wordpress-LT instance, and I can see that the Wordpress-ASG has been successfully created.
![unnamed (37)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/78b6dbb2-e9cd-42f9-9014-d2eae988366b)

* Then I added two dynamic scaling policies that add instances when average CPU utilization is above 40% and remove instances when it is below.
* For this, I created a CloudWatch alarm with the A4LWORDPRESSASG CPUUtilization metric and set it up to be in alarm state when CPUUtilization is above 40%.
![unnamed (38)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d7b9edfd-9cf9-44ad-9dfb-e215a755c8cc)

* Then I navigated back to the ASG console, selected the CloudWatch alarm and set up to add 1 unit for the alarm and created the policy.
![unnamed (39)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/3d3c5470-da0f-43be-a998-d33ca18f225d)

* I did the same process for the LOWCPU policy and created it:
![unnamed (40)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/2d848c32-636a-4be4-94d6-f8c80458a7f2)

* Now I have two scaling policies.
![unnamed (41)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ecf22881-4061-4bb2-9bc8-b554fd42757d)

* Then I updated the maximum capacity to be 3.
* Now I can test this by connecting to the instance using session manager and stressing the instance using this command:
````
stress -c 2 -v -t 3000
````
![unnamed (42)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d5cdef21-8f8d-49cf-b524-7a201d1ef257)

* I can see the spike in CPU Utilization and from the activity history, I can see that a new instance is created:
![unnamed (43)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/5248bda9-09a9-412c-b27d-bc9b4fe3b8ef)
![unnamed (44)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/a39d83e7-f8c9-4255-86ef-c371941b5057)

* This instance is being provisioned in a different availability zone
![unnamed (45)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/2788ec72-278d-4bcb-ae94-2d7ea6d5bc23)

* Now if I manually terminate the starting instance, the ASG will automatically detect that there is only one running instance and provisioning another one. This implements a self-healing architecture.
![unnamed (46)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f96027eb-4dfc-42b9-9e80-4e480736c953)

* Also, now I can use the ALB DNS name to connect to the WordPress website.
![unnamed (47)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b380abd2-5a8a-43f6-a158-18c61d5f5b12)

* Now this is a fully scalable, elastic, and self-healing architecture. I removed all of the limitations of the monolithic WordPress application.


### What I Learned
* I learned the limitations and method of overcoming such limitations for each architecture so that websites in production mode can fully scale and self-heal.
* I also learned that I can direct connections to the ALB so that no direct connections can be made to specific EC2 instances.
* I also learned that ASG policies are based on CloudWatch alarms so I need to make such alarms for ASG to take action.
