#### This section covers about using EC2 instance roles to access AWS resources.

## Using EC2 Instance Roles
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
![unnamed (13)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/5631024d-ef87-43ee-9381-fc0a56dea14b)

* Then I connected to the created EC2 instance using EC2 Instance Connect.
* Initially I don’t have any configurations on the session, so I can’t use it to access resources
![unnamed (14)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/60e19adb-5995-45ae-8800-bc763c06268c)
* Using aws configure isn’t the best practice to access the resources.

* I want to use an instance role from IAM so I created one in the IAM console with S3ReadOnlyAccess permission.
![unnamed (15)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f09777cd-5361-4e29-9138-83763236be25)
![unnamed (16)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/5379e586-bba4-4088-8ad6-a901c5ed9732)

* Then I went back to the EC2 console and modified the IAM Role for the EC2 instance.
![unnamed (17)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/0323149f-5dc0-4876-8ed2-f518f5f05bb5)

* Now if I reconnect and try to access S3, it works.
![unnamed (18)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b8437b77-22d3-439a-8275-8281dfea5453)

* I can also view data about the instance role:
![unnamed (19)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/801face5-c738-48bd-81d7-db5ec207e97a)

