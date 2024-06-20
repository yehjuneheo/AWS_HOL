#### This section covers about splitting WordPress monolith architecure to a tiered architecture with separate application and database instances.

## Splitting WordPress Monolith to Tiered APP & DB
* Although it is really rare to use databases inside EC2 instances, this lab aims to understand bad monolith architecture and how to make it to a tiered architecture.
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
![unnamed (26)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6c24f131-8bf7-4bc2-a892-b2418949fde6)

* Then I went into the created WordPress instance and configured WordPress installation on it. I used the DBRootPassword for the admin password.
![unnamed (27)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/4f9693ce-675f-4065-aec0-cdb31b1c0c1d)

* Then I created a new post with images and published it
![unnamed (28)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b5519509-ae43-40fe-a373-529cc7356998)

* Currently, the local file system is stored in the A4L-WordPress and also the data for the block post is stored on MariaDB of that instnace
![unnamed (29)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/8272a45e-38ec-4c13-bfd5-47de19db4d60)

* To migrate the database to a separate instance, I first should backup the source database using this command: mysqldump -u root -p a4lwordpress > a4lwordpress.sql
![unnamed (30)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/833ec755-c917-4c0a-9339-d851c92faa1e)

* Now to create the database and inject the data, I ran this command: mysql -h privateipof_a4l-mariadb -u a4lwordpress -p a4lwordpress < a4lwordpress.sql
* Then I navigated to wp-config.php and changed the DB hostname to the private address of the database instance:
![unnamed (31)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6c54cc4b-cdb5-4218-b614-0a2464a029d8)
![unnamed (32)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/90f1bc20-1178-4c9d-96b4-db6640cee4ce)

* Now, even if I stop mariadb of the WordPress instance (sudo service mariadb stop), I can see that the blog is still working:
![unnamed (33)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/896bc4ac-434a-41bb-861f-5b36d3590ce6)

