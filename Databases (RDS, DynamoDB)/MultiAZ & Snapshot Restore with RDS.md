#### This section covers about configuring MultiAZ RDS instances and creating snapshots for restoration.

## MultiAZ & Snapshot Restore with RDS
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
![unnamed (80)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/80403eba-a884-4afe-b55c-627823911e6f)

* I did the same initial WordPress configuration as the above section, and created a new blog post.
* Then I went to the RDS console and took a snapshot of the created RDS instance.
![unnamed (81)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/c2130bf4-b467-44f7-a546-5fff6f74a306)
![unnamed (82)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6065bc75-fb24-45f2-a340-6efeeddccc65)

* Then I modified the DB instance create a standby replica in a separate availability zone that allows synchronous replication.
* To test if the standby replica is successfully created, I can try rebooting with failover:
![unnamed (83)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9af1b657-e4d6-4e86-badc-2f9411f56105)

* After a delay, I can see that the blog post is still working:
![unnamed (84)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9db95179-5737-4787-8d4f-80c90de02139)

* Now I wil simulate data corruption and try restoring the databse using a snapshot.
* I changed the blog post name to a different name and updated it:
![unnamed (85)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e10713e3-e2a5-4b05-9b61-2ac4823b5d6e)

* Now I will restore the snapshot created earlier with default settings but my own RDS security group:
![unnamed (86)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/24b36578-48e5-4878-9bbd-f26203ec4561)

* It is crucial to understand that restoring from a snapshot will create a brand new database instance with a different endpoint, so I need to manually adjust to point to that endpoint.

* So I connected to the WordPress instance using EC2 Instance Connect  and changed the wp-config.php to point to the new endpoint.
* Now if I refresh the website I can see that the website is pointing to the restored version of the database:
![unnamed (87)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/686e41e1-b53a-4d4b-9a82-5ced4e536d32)

