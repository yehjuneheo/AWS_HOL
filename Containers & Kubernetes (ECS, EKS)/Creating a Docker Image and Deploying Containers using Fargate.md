### This section covers about creating a docker image.

## Creating a Docker Image
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
![unnamed (56)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/428f54c6-c3b7-415e-9ec8-b4de4ed16ccb)

* Then I connected to the EC2 Instance using a session manager and entered this commands to install and configure user:
sudo dnf install docker  
sudo service docker start  
sudo usermod -a -G docker ec2-user  

* Then I reconnected to the session manager for the instance. Since I am using session manger, I can directly login without ssh keys using: sudo su - ec2-user
* Then I built the docker image and ran it:
![unnamed (57)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6c549f5f-3c75-4011-b484-21575ad14a91)
![unnamed (58)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b3380868-60a1-4c0e-896e-35f99b305c9a)

* Now if I go to the public IPv4 address, I can see that the website it successfully working.
![unnamed (59)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/dcd382e7-368d-4caa-9ac4-36ea95c9dd8c)

## Deploying Containers using Fargate
* First I created a new Fargate cluster inside the default VPC with default settings.
![unnamed (60)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/4b8b911a-8f5e-4cb0-bec3-29ef5e108623)
![unnamed (61)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d4f4e6e5-6164-4151-ac17-efe8d0f962ff)

* Then I created a task definition that will deploy my container into the Fargate cluster
![unnamed (62)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/1c0af485-375b-4b77-8ee8-e1af456593c5)

* I configured it to use .5 vCPU and 1GB of memory.
![unnamed (63)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6c37bf56-abf3-4f36-9cb8-49a03653d958)

* From the clusters tab, I ran a new task for the new cluster with the Fargate launchtype
![unnamed (64)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/55948b79-688b-46cc-be9f-67d628dfef5e)
![unnamed (65)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6fb1d22f-d983-4920-b289-310608785cd5)

* Then I created a new security group to allow HTTP access from anywhere:
![unnamed (66)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/deccf4dc-1286-454f-80a6-d16ad0e8dc7c)

* After a few minutes, the task was running:
![unnamed (67)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e8a580f0-50c3-42e5-8106-372405aab957)

* Now if I go into the task, I can see that there is a public and private IPv4 address.
![unnamed (68)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/0500536d-fcc7-4d4f-b99c-ef99e0e353e9)
![unnamed (69)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/701e1dfc-40f1-45ad-93c8-eb2c19a9bb6a)

* If I go into the the IP address, I can see that the website it working.
![unnamed (70)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b8ba4d4c-f6ec-4dc4-af6f-45e08f19fecd)


### What I Learned
* When running a website using ECS, I need to enable the security group to allow access from the HTTP port.
* Task definitions are the ones that deploy the containers into the Fargate cluster.

