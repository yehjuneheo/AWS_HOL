#### In this lab, I am going to implement a hybrid directory solution using the AWS directory service.
![unnamed (22)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/40ac2dd5-1e54-4cff-b229-cebdee667e2d)

## AWS & On-Premises Setup
* First, I created a SSH key pair from the EC2 console and saved the .pem file locally.
* Then I used the Cloudformation template provided by learn.cantrill.io to automate the building of some necessary resources.
![unnamed (23)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/355d6a06-531b-4db0-8b64-488cdb1b18ff)
* The stack created multiple nested stacks, each for on-premises VPC, on-premises directory, on-premises compute, and the AWS side infrastructure.


## Connect to the On-Premises Jumpbox
* In this part, I am going configure the access to the on-premises network via the jumpbox.

* First, I navigated to the EC2 console, which I can see that there were 5 instances created.
* I noted down the public DNS IPv4 address of the jumpbox instance
![unnamed (24)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/bd65110a-7356-49f3-94ab-7580871eb871)

* Then I connected to the jumpbox instance using a RDP client and retrieved the password using the key file.
* I opened the remote desktop and connected using the retrieved password.
![unnamed (25)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/1f8e17d6-f65f-4800-bbcd-906ca98d7913)

* Then I copied the private address of the client instance and connected from the remote desktop, which allows me to connect to the simulated on-premises environment of the Animals For Life organization.
![unnamed (26)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6f68f52e-4702-4fb8-bcee-ac7a8194b2df)

* hen I navigated to the fileserver folder and a4life folder to create a demo test file for migration.
![unnamed (27)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b19ef13c-aa02-4495-ac67-6d9a2643f9a5)


## Create Managed Microsoft AD within AWS
* In this part, I am going to provision a managed Microsoft AD running in AWS, and then create a jumpbox that also runs inside the AWS environment thats domain joined to this managed Microsoft AD.

* First I navigated to the directory service console, and created a AWS managed Microsoft AD inside the AWS private subnets inside the AWS VPC.
![unnamed (28)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/0ecf8558-ff1c-4cb9-9cbd-b97d8de48ff4)
![unnamed (29)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9ec4dd61-fed4-49b5-8793-7e23c01acf63)

* Then I navigated to the EC2 console and created a jumpbox instance using the Windows Os and t3.medium as the instance type inside the AWS public subnet of the AWS VPC.
* I also configured an IAM instance profile and selected aws.animals4life.org as the domain join directory.
![unnamed (30)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/16e11242-2668-45d3-8ae8-490c893a5246)

* Then I created another remote desktop and connected to the jumpbox instance and installed the server administration tools using a powershell prompt.
````
Install-WindowsFeature -IncludeAllSubFeature RSAT
````

* Then I restarted the remote desktop and reconnected to it.
* If I go into the active directory users and computers, I can see the Microsft AD delivered by the directory service.
![unnamed (31)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d1568ce6-c671-4fab-b09e-c06d4e301c42)


## Create Two-Way Forest Trust
* In this part, I am going to begin the process of integrating both of these environments along with two-way forest trusts between the on-premises server and AWS server.

* First I checked the Kerberos pre-authentication settings of the client server by navigating to the client remote desktop and opening up the Active Directory Users and Computers application.
* Then I navigated to the user’s folder and opened up the properties of the admin, and confirmed that the Kerberos pre-authentication setting is disabled.
![unnamed (32)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/edf67ac0-eb07-46f7-b213-9d8acda39fa6)

* Then I opened the DNS Manager with the DC1 computer, and created a new conditional forwarder with the Microsoft AD DNS addresses.
![unnamed (33)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/49227ee4-8b55-444a-9170-9c4b3d1a695d)

* Then I navigated to the VPC console and edited the outbound rules for the directory service security group to allow all outbound traffic.
![unnamed (34)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/2486b81c-8936-4faf-8650-8f5c6ea49595)

* Then I navigated to the AWS side AD remote desktop and checked that the Kerberos pre-authentication setting is disabled.
* Then I opened the Windows Administrative Tools in the client side remote desktop and opened Active Directory domains and trusts, and created a new two-way forest trust.
![unnamed (35)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f0ca0423-c43a-49a9-b1f0-9823e7e9281d)

* Then I navigated to the directory service console and added a new trust relationship.
![unnamed (36)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/10aa1494-d480-4560-aff1-df24c5e24920)
![unnamed (37)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/8778f0ef-84f7-4df7-891e-f1f9c3b95ece)

* Then I navigated to the AWS side AD remote desktop and configured so that admins of the on-premises active directory domain are also administrators of the AWS Managed Active Directory domain.
![unnamed (38)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/4b27ae12-d328-4fa5-abf5-f2eb22919461)
![unnamed (39)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/3e005570-e939-4071-977a-962080a1bd3e)

* Now I can log in to the AWS jump box with the admin user of the on-premises domain, while having full permissions.


## Create FSx and Migrate Data
* In this part, I am going to implement the FSx managed file server product within AWS and configure the distributed file system between the file server on-premises and the implementation of FSx.

* First I navigated to the FSx console and created an Amazon FSx for windows file server.
![unnamed (40)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d0021d89-a1bf-45f2-85a2-5856c070f895)

* Then I navigated to the on-premises jumpbox and typed \\amznfsxx7nhwd1k.aws.animals4life.org (DNS name) on a new file explorer.
* I can see a file share named share inside the folder.
![unnamed (41)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/246dd303-9e31-4468-80ac-7ec6ea1192d2)

* Then I opened the DFS manager to configure a distributed file system for the environment.
* I created a new namespace and a new a4lfiles folder with the fileserver and FSx share folder as the targets.
![unnamed (42)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/176d48db-dae5-450c-aba6-65b5fb7daa69)

* Now \\ad.animals4life.org\private this takes me to the a4l fileshare, where I can be redirected to one of the file servers. I can also see the testfile configured inside.
![unnamed (43)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/2781a9b8-1ced-4a7f-8852-c9094828eca3)

* Then I disabled the on-premises folder target so that it only directs to the FSx file share.


## Create Workspace and Migrate Desktop
* In this part, I am going to create a workspace for the on-premises administrator.

* First I navigated to the AWS workspaces console and created a new workspace using the Microsoft AD directory created. I had to register the directory service to use it, so I did in the private subnets of AWS.
![unnamed (44)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/3816feea-488e-41ca-94f7-480df1f629ec)

* To create a user from the on-premises server, I navigated to the on-premises jumpbox and created a new user.
![unnamed (45)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/1f1efeb7-eacb-4681-8d15-4b267d6b7b46)

* Now when I go back to the AWS console, I can see the newly created admin.
![unnamed (46)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e9a24f22-831c-4bdb-af70-0cb5f2173552)

* The workspace was successfully created and then downloaded the windows virtual desktop
![unnamed (47)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/83a46b90-3728-448e-bfa1-a4ae9f66c9cc)

* Then I logged into the workspace and tried to connect to the DFS file space, which doesn’t work because I didn’t configure the security group settings yet.

* So I navigated to the VPC console and edited the hybrid directory security group to allow inbound traffic from the workspace’s security group.
![unnamed (48)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/94bfd879-7172-4f38-b436-a1384dba7f17)

* Now I can access the file share and view that the testfile.txt is inside the folder.

### What I Learned
* I learned how to implement a FSx system and the benefits of using such service.
* I learned about configuring complex security group inbound and outbound rules to allow only necessary traffic.
* I learned how to connect a Microsoft AD directory from the on-premises server to the AWS server so that they are synchronized.


