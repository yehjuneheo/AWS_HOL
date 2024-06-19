#### This section covers about creating bootstrapping WordPress installation on an EC2 instance.

## Bootstrapping WordPress Installation
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
![unnamed (98)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f14ca1bb-4d19-4564-8de7-ad6b353ae561)

* Then I created an EC2 instance within the A4L VPC Web Tier subnet (Availability Zone A) and default settings
* I configured the user data from additional settings and pasted the data provided by learn.cantrill.io:
![unnamed (99)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/5e063227-b349-4045-acb1-6b284a71d5ca)

* I successfully created the instance and I connected to it using EC2 instance connect.
![unnamed (100)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/18f1be7e-b9de-440b-9052-f144b5dbc63f)

* I can use this command to view the user-data: curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/user-data/

* Now I am going to launch another bootstrapped cloud formation that has everything configured inside the template.
* I can change the parameters if needed:
![unnamed - 2024-06-20T000156 568](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/c06619e5-b045-4e2c-ab9d-7f4febd45d14)

* The user-data is already base64 encoded inside the template, which allows simpler deployment of the instance.
