#### This lab covers about implementing VPC interface endpoints, gateway endpoints, and egress-only internet gateways.

## VPC Endpoints - Interface
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
![unnamed (56)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/2d313525-969a-4597-b3d0-1ac23ec63673)

* The stack created an EC2 instance without a public IP address, which means I can’t use EC2 instance connect from the console UI.
![unnamed (57)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9af6c8b5-b247-4b97-8bea-3c9e596ae022)

* However I can connect to the private instance using an EC2 Instance Connect Endpoint.
![unnamed (58)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e367f24f-59a1-4c8a-a859-5e82d4ee3708)

* For this, I need to create an endpoint inside the secret A4L VPC n-app-A subnet with the correct security groups:
![unnamed (59)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d3d0a450-aa2f-4418-90b6-2ba500d99acd)
![unnamed (60)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/2732b4b6-bdaf-4765-ae6d-498cbeb811ee)

* Then I connected the create EC2 Instance Connect Endpoint to my private EC2 Instance.
![unnamed (61)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/2f5eeedd-bad4-43a9-820f-d079321dcdcf)
![unnamed (62)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/c6d07e38-8467-4c5e-b261-9b5c2b79b57b)


## VPC Endpoints - Gateway
* First I uploaded a test textfile to a created bucket:
![unnamed (63)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/096e788e-0f4d-4e97-a502-4c1f2c1d27a8)

* Then I connected to the privat instance using EC2 Instacne Connect Endpoint and I checked it doesn’t have any internect connectivity by pinging 1.1.1.1:
![unnamed (64)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d1196bb3-de40-491d-9ff9-c01f542c1c9c)

* So I navigated to the VPC console to create a gateway endpoint for private S3 access inside the a4l secret VPC.
![unnamed (65)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/bd9e7bef-d1a5-4362-8c7e-ab8529820a42)
![unnamed (66)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/2b47aa86-8a68-4303-aaf8-7d416e1fb3df)

* Now if I go back to the EC2 console and run aws s3 ls, I can see that the private EC2 instance can access S3.
![unnamed (67)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/8c202843-aecc-44b9-86e0-9f3a8517596c)

* I can also copy down the file inside the bucket and read it using these commands:
![unnamed (68)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/0d45ebcf-bc8c-45c3-9218-712399b38f7c)


## Egress-Only Internet Gateway
* The current interface endpoint doesn’t support IPv6 so if I try to ping an IPv6 address, it won’t work:
![unnamed (69)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/eafcfad3-4dad-46d3-adfd-892a1a11b9db)

* To allow IPv6 address access from the instance, I need to create and egress-only internet gateway.
* So I navigated to the VPC console and Egress-only internet gateways section and created one inside the a4l secret VPC.
![unnamed (70)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6980ad4c-e391-4642-a607-985cddbaccf3)

* Then I had to configure the routing to allow the VPC router to direct traffic flow toward it.
* I selcted the main route table for the a4l secret VPC and edited the routes to include ::/0 which is the default IPv6 address.
![unnamed (71)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/c0bd10f6-42df-4c8a-b730-cc43a6f106d4)

* Now if I try pinging again, I can see I am getting a response:
![unnamed (72)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/edc82518-6344-41e2-ace8-6554f1b1147c)


