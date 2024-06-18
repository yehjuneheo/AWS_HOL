### This section covers about manually installing WordPress on an EC2 instance.

## Creating EC2 Instances
* Although generally manually installing WordPress on EC2 is not recommended, the purpose of this lab is to understand the disadvantages of doing such action and the benefits of using automation.
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
![unnamed (28)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/efb7dac9-9629-4c83-b63f-7236265f99c2)

* Then I connected to the instance using Instance Connect and entered these variables:  
DBName='a4lwordpress'  
DBUser='a4lwordpress'  
DBPassword='4n1m4l$4L1f3'  
DBRootPassword='4n1m4l$4L1f3'  

* Then I installed the software packages needed to host a WordPress website using this command:  
sudo dnf install wget php-mysqlnd httpd php-fpm php-mysqli mariadb105-server php-json php php-devel -y

* Then I started both the web server and database server:
![unnamed (35)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/5a788051-08d3-4a24-8320-465562f73bce)

* Then I added DBPassword as the root password for MySQL and downloaded WordPress
![unnamed (36)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/16d9ad8a-452f-4e4d-a652-47eb07d7951f)

* Then I extracted the files and moved the downloaded files to the respective folder using these commands:  
sudo wget http://wordpress.org/latest.tar.gz -P /var/www/html  
cd /var/www/html  
sudo tar -zxvf latest.tar.gz  
sudo cp -rvf wordpress/* .  
sudo rm -R wordpress  
sudo rm latest.tar.gz  

* Then I configured the correct DBName, DBUsername, and DBPassword, along with setting up the ownership of all of the files in this folder to be the Apache user
![unnamed (37)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/1f80a686-38fe-4672-ad64-740e6da189d1)

* Then I created the WordPress Database using these commands:  
echo "CREATE DATABASE $DBName;" >> /tmp/db.setup  
echo "CREATE USER '$DBUser'@'localhost' IDENTIFIED BY '$DBPassword';" >> /tmp/db.setup  
echo "GRANT ALL ON $DBName.* TO '$DBUser'@'localhost';" >> /tmp/db.setup  
echo "FLUSH PRIVILEGES;" >> /tmp/db.setup  
mysql -u root --password=$DBRootPassword < /tmp/db.setup  
sudo rm /tmp/db.setup  

* Now if I copy the IPv4 address and open it, I can see that the website is working successfully.
![unnamed (38)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/570f5b5f-493a-41bd-9647-bdc4d2121995)
![unnamed (39)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/3fa37b53-6a5e-45b8-8e7d-a5a0d816c03c)


### What I Learned
* I need to manually enter any parameters needed to connect to the WordPress website if I want EC2 to be able to connect to it.
* When checking if a website works or not, I need to use the public IP address without https since it is generally configured initially.

