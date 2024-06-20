#### This section covers about migrating EC2 database into RDS.

## Splitting WordPress Monolith to Tiered APP & DB
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
![unnamed (34)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ce87da20-0f2c-45a8-9d54-4f89990db4cc)

* Then I went into the created WordPress instance and configured WordPress installation on it. I used the DBRootPassword for the admin password.
![unnamed (35)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ab229b32-8a2f-4b9d-b0f6-6e157e9ef41a)

* Then I created a new post with images and published it
![unnamed (36)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/fc9bd5ab-a495-41ae-8fa5-0b924691bd03)

* Then I created a subnet group within three subnets inside RDS:
![unnamed (37)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d3d0dbfc-200a-429b-9c99-46c98aa0615b)
![unnamed (38)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6f2ab135-cc26-44f1-a126-257efc742aed)

* Then I created a new MySQL database with default settings and a new security group.
![unnamed (39)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/7d7910d9-f530-4c97-a52b-3d354e70cc51)

* Currently, the RDS security group only allows access from my IP address. So I added a new inbound rule to allow access to MySQL from my EC2 instances security group:
![unnamed (40)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/410f7b88-2987-461c-a2c3-96a9baaa63d0)
![unnamed (41)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/63642cb2-26ae-4d89-81d0-e6365891651e)

* Then I connected to the WordPress instance using EC2 Instance Connect and back up the source database using this command:
````
mysqldump -h PRIVATEIPOFMARIADBINSTANCE -u a4lwordpress -p a4lwordpress > a4lwordpress.sql
````
![unnamed (42)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/db0ef99d-efd0-4d83-ad52-d392be601a7e)

* Then I restored the data to the RDS destination database and changed the WordPress config using these commands:
````
mysql -h CNAMEOFRDSINSTANCE -u a4lwordpress -p a4lwordpress < a4lwordpress.sql
cd /var/www/html
sudo nano wp-config.php
````
![unnamed (43)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/319e37d6-29e2-471c-b6a1-89c67f13e5a7)

* Now even if I stop the DB instance, I can see that the blog post is still showing:
![unnamed (44)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/750fc892-88b3-4000-9883-b9e7cdd5fa29)

