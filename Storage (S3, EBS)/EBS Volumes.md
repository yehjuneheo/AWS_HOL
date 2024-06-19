#### This section covers about attaching EBS volumes to EC2 instances.

## EBS Volumes
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
* CloudFormation created 3 EC2 instances and 3 EBS volumves that I will be using in this lab.
![unnamed (20)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d1ff920b-115b-40cc-9bfd-a1a20b56275b)
![unnamed (21)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/77eef767-c07c-40b4-888c-bc569b1ada51)

* Then I created an EBS test volume with the default settings.
![unnamed (22)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/892e8d8e-aa91-4b8c-84b3-2511b67f5d71)

* When it is in the available state, I can attach it to an EC2 instance.
![unnamed (23)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/2c5c06a1-9f5b-4200-89a2-64ada00b5da1)

* Then I connected to the EC2 instance use EC2 Instance Connect.
* I used the lsblk command to list the block devices:
![unnamed (24)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/3d7c1171-7225-4e73-b1b1-226f9a8b6968)
* The xvdf is the EFS that we manually created and attached.

* To check if there is any file system on the device we can run: sudo file -s /dev/xvdf
![unnamed (25)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/27436cfc-d99a-413e-82d2-bca281dcebdf)
* Data means there is no file system on the device (we can only mount file systems under Linux so we need to create one)
* I used this command to create a new file system: sudo mkfs -t xfs /dev/xvdf
![unnamed (26)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/53d70cce-c3e6-412e-90bf-223570c951bf)

* Then I mounted the file system to ebstest folder and created a test file:
![unnamed (27)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/64437d1b-4c15-47f4-8709-548319170403)
![unnamed (28)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/090cfedd-cfd3-4570-a4d3-078752b98038)

* Now this file is in the folder, where it is now the mount point for the file system that I created on this EBS volume.
* Then I rebooted the EC2 instance and reconnected to the instance.

* However, I can’t see the file system because I manually mounted the file system, which doesn’t mount automatically on a restart.
![unnamed (29)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/8c1722ce-d734-4e29-ae0f-7eabbf813962)

* To configure auto mount, I first need the unique ID of the EBS volume, which can be found using the blkid command:
![unnamed (30)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b4856cee-37eb-4c20-8126-da8fc5960c98)
* Then I added the UUID to /etc/fstab which controls which file systems to be mounted on restart.

* After mounting it, I can see the ebstest folder now.
![unnamed (31)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/c6e7b238-02cc-4ee6-86ce-e74d65048de6)

* Then I stopped the instance and detached the EBS test volume.
* Then I attached the volume to another instance running in the same availability zone and connected to that instance.
* Now if I check the instance using df -k, I can see that the file system is not mounted:
![unnamed (32)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/363d9a07-0404-4dc9-9731-4d2ed15c3de4)

* However, if I run sudo file -s /dev/xvdf, it now shows a file system on the EBS volume because we already created it
![unnamed (33)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/51e5a6c8-ce94-4468-bb0e-40b217cb137f)

* I can view all the contents inside the file system because EBS are persistent and independent from the EC2 lifecycle.
![unnamed (34)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/a62f0852-a9e1-4d1b-811b-4a71e88f8c65)

* Now I stopped the instance and detached the EBS volume again. I want to attach the EBS volume for an instance in another availability zone, but I cannot do that right away since EBS volumes exist only in availability zones.
* For this, I need to create a snapshot and store it in S3 to replicate between multiple availability zones in that region.

* After creating the snapshot, I can create the volume from the snapshot into a different availability zone.
![unnamed (35)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e8653477-e626-4216-bb18-f32b1f3ce6a1)

* I attached the EBS volume once it was created to the EC2 instance running in that availability zone and connected to it.
* Now I can access the contents on the file system from a different availability zone.
![unnamed (36)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/777e2567-f1cc-4563-8816-0859f8b802c2)

* I can also copy snapshots to different regions if needed:
![unnamed (37)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d5b6203e-ee50-4c33-85a6-91dbf896e3c3)
