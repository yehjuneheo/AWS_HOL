### This section covers about creating and sharing an AMI.

## Creating an AMI
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
* Then I connected to the EC2 instance using Instance Connect and manually installed WordPress, the same as the process above.

* Now I want to customize the AMI to make sure that everybody knows that this is an A4L EC2 instance.
* I installed an animal-themed utility called Cowsay using this command:  
sudo dnf install -y cowsay

* Then I added the Cowsay theme to be displayed when the user is logged in:
![unnamed (40)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/cdfed4fd-1c9a-4829-88ee-1ef5d288d812)

* Now if I reboot and reconnect to the instance, I can see the Cowsay theme being displayed.
![unnamed (41)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d71b3779-b4d0-4382-b9c9-8880294b6d47)

* Now to create the AMI, I am first going to stop the instance to prevent consistency issues.
![unnamed (42)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/cf378a0b-dbfe-488e-bca1-92ffe9cbd442)

* When I create the image, the snapshot of EBS volumes attached to the instance is created automatically along with the AMI.
![unnamed (43)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/db992b75-b40d-4c9b-a67d-d0dcbddb83a2)
![unnamed (44)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/00adbfc6-69f4-4900-bc0a-e2865f31321b)

* Then I launched an instance using the AMI:
![unnamed (45)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/fde32d7f-ce28-4618-908f-aba3f4e905a5)
![unnamed (46)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/1de2cc45-9b86-4ea3-a782-1cb65ab9adeb)

* I changed the username from root to ec2-user and connected to the instance.
![unnamed (47)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/0cde0d10-b00b-474b-ad1e-033a8613b6aa)


## Copying and Sharing an AMI
* For the AMI above, I am going to copy it to the Seoul region.
![unnamed (48)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b35fbdc8-b6d1-40b1-a167-955eda843d01)

* Now if I go to the Seoul region, I can see that the AMI and snapshot is being created:
![unnamed (49)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/2983c051-bcd0-4e9c-9e43-e92f9d3ef547)
![unnamed (50)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/63f3edd4-a7c5-458c-87f0-d5fbf0e9ea37)
* Note that this AMI is completely separate in a different region.

* Now I want to change the permissions of the AMI, which is private by default.
![unnamed (51)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/bb1954d0-2c24-4a1a-8e62-e033134e3c0d)

* I can share the AMI to other accounts or organizations, which is recommended over making it public.
![unnamed (52)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/36d1c28d-e4ad-4d4a-8139-83a3cd88ff2d)


### What I Learned
* I didn't know I was able to customize the AMI to display a certain message to indicate that the user is using the AMI.
* When I create an AMI, a snaphot is automatically created, and if an AMI is copied to another region, it is not linked to the original one.

