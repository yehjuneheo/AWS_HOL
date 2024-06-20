#### This section covers about implementing an Elastic File System for an EC2 instance.

## Implementing EFS
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
![unnamed (45)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9be8c078-21cf-4f8b-9097-43275a74db44)

* This created two instances, where I will mount my EFS file systems.
* Then I customized a new EFS file system from the EFS console where I chose the regional file system type and bursting throughput mode.
![unnamed (46)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/cad93017-ad6c-4ced-9d1c-dfaf39c79990)

* From the network settings, I chose the InstanceSecrutiyGroup for SG in each subnet to allow access from EC2 to the EFS.
![unnamed (47)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/dc779ea6-f5ae-4c37-89a5-d75e61ca654e)

* I can also see that the mount targets are created and available:
![unnamed (48)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f76a8144-9ecd-4881-88fe-5792d9faa800)

* Then I connected to one of the EC2 instance using EC2 Instance Connect, and installed the necessary EFS file system tools and created a new folder:
````
sudo mkdir -p /efs/wp-content
sudo dnf -y install amazon-efs-utils
````

* To make the EFS file system mount automatically on restart, I added this line to /etc/fstab:
````
file-system-id:/ /efs/wp-content efs _netdev,tls,iam 0 0
````

* Then I mounted the file system using this command: sudo mount /efs/wp-content
![unnamed (49)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/2ac40120-6bd9-4ee0-a807-2c4d77185e28)

* Then I created a test file inside /efs/wp-content using:
````
cd /efs/wp-content
sudo touch amazingtestfile.txt
````

* Now if I do the same process above for a separate EC2 instance, I can see the testfile is already inside the /efs/wp-content folder, because EFS is a multi-user network based file system.
![unnamed (50)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/493db80d-792d-4ba2-a230-dc816a6e44f4)


