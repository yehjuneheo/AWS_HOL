#### This section covers about using Route53 and Failover Routing

## Using R53 and Failover Routing
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
![unnamed (71)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b60c8a49-c1ed-4846-9645-05f114629827)

* If I go into the public IP address of the created EC2 instance, I can see that the website is working:
![unnamed (72)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/06e6250b-2bfb-4fcd-b3a1-fbade1549c1d)

* Then I allocated an elastic IP address and associated it with the EC2 instance:
![unnamed (73)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/7d6c59c2-bca0-43ee-9415-2d1cb433d9e4)

* Then I created a new bucket name www.yehjuneheotraining.com, and uploaded the index.html and minimal.jpg that will be used for static hosting.
![unnamed (74)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e1943794-9dee-4d77-a514-9496ba36a346)

* Then I enabled static hosting for the bucket and set the index document and error document both as index.html.
* I also edited the bucket policy to allow public reading.

* To configure failover routing, I first created a health check from Route53.
![unnamed (75)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ce72c0d5-0a7b-4f5e-8636-a6e370bb2d87)

* Then I changed the default request interval of standard (30 seconds) to fast (10 seconds) and created the health check.
![unnamed (76)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/4ee55f05-8a82-4e04-ad68-d94d73c6d6f7)

* Then I went to the R53 console and created a new record with a failover routing policy and two failover records.
![unnamed (77)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/58bec0db-9033-42e3-bf65-55147d282153)
![unnamed (78)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/dd093339-cf54-48c6-92aa-631ba3bd34c9)

* The records were successfully created:
![unnamed (79)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9433d727-98a7-4c58-a55d-dc5d55b27c64)

* Then I stopped the EC2 instance waited a few minutes until the health check moves to an unhealthy state.
![unnamed (80)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/a1335ca2-5f80-4ecd-9558-4aaa07ac6057)

* Now if I go to www.yehjuneheotraining.com, I can see the failover page:
![unnamed (81)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e215997c-0e80-4311-865e-facb6cce88d3)

* Then I created a private hosted zone in my default VPC, and this allows me to use a domain name that I don’t even own.
![unnamed (82)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/0e8a1843-1ece-4571-8c20-66e695bd86a6)

* Then I created a simple record:
![unnamed (83)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/65584381-3bfb-4daf-aaae-b9a9460a797a)

* Now if I connect to the EC2 instance and try pinging from www.privatewebsite.com, I can see it works:
![unnamed (84)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b13f4eb1-be6e-4047-aac7-7647b98e5cf3)

