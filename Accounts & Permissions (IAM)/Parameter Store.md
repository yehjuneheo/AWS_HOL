#### This section covers about using the Parameter Store to store sensitive information.

## Using Parameter Store
* The parameter store is a service within AWS Systems Manager, so I went into the systems manager console and created a parameter.
![unnamed (38)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/8181affd-a06f-4697-a7a2-50c647fda9b6)
* I used db.allthecats.com:3306 for the value of the parameter

* Then I created another parameter for dbuser and dbpassword, with dbpassword to be a secure string with KMS key encrypted
![unnamed (39)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/7d94ccf4-58a8-4439-89e1-d6bfae2ad367)
![unnamed (40)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e0ae4aac-0363-46f2-a1c1-6ca7c74244dc)

* Now I can use the AWS CloudShell to get the parameters from SSM using this command: aws ssm get-parameters --names /my-cat-app/dbstring
![unnamed (41)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/43ea6cf1-d618-41f5-b4f7-dd64284553aa)

* I can also use this command to get all the parameters inside the my-cat-app folder: aws ssm get-parameters-by-path --path /my-cat-app/
![unnamed (42)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/21a6cae2-df5b-4fe3-8b60-d5c955a29958)

* To perform decryption for the password I can use this command: aws ssm get-parameters-by-path --path /my-cat-app/ --with-decryption
  * This needs permissions to interact with the parameter store and the KMS key used for encryption
![unnamed (43)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/712bc602-5c00-43d8-bd0d-fd1045d48bb3)

