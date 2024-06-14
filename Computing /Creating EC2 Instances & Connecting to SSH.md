#### This section covers about creating EC2 instances and connecting it to SSH

## Creating EC2 Instances
* First, we need to create an SSH key pair, for accessing the EC2 instances
![unnamed (28)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/998872f3-4b2a-4e5a-a961-6a32a0c48013)

* I went to the launch console and used Amazon Linux 2023 AMI with t2.micro instance type and used the key pair that I generated.
![unnamed (29)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/2fa0eea0-c732-4021-aca0-f6f10a63bd45)

* I didn’t change any default network settings except for creating a new SG
![unnamed (30)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9c9a975f-cd3c-4539-a030-8112b2b56455)

* I created the instance and it is successfully running.
![unnamed (33)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d3ddc604-dcf5-4753-bcf6-52f10b73112d)

* To connect to the SSH, I downloaded OpenSSH for windows
![unnamed (31)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9010df4b-1d4b-4005-9336-bbfd7c9037a7)

* I had to move the .pem file to the local directory where I am running the cmd to successfully connect to the instance.
![unnamed (32)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/7011f35c-489c-442f-87c5-2e2f760dbd65)

