#### This section covers about using accessing a private elastic file system using Lambda.

## VPC Lambda & Elastic File System
* This is the architecture that I will be implementing in this lab:
![unnamed (47)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/5e7a7f84-6775-413a-9a27-739fabde3fed)

* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
![unnamed (48)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e5ea1544-137a-4a21-92c9-718631679e1c)

* The template created an EFS with two mount targets and two EC2 instances.
![unnamed (49)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e90175d8-92ca-4064-8643-9c2f0502b775)
![unnamed (50)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/1218ed26-0872-49e4-bd4e-db635727afca)

* I opened the public IPv4 DNS address of the WEBA instance to verify that its working.
![unnamed (51)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/5bad5889-1428-4fec-8a45-fa2b5dab3753)

* The template also created a Lambda role that has permissions access the VPC and EFS.
![unnamed (52)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/943445bb-39a6-4cc8-bac6-321e488b738d)

* Then I navigated back to the EFS console and created an access point for the EFS.
![unnamed (53)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/2be43652-fb5f-4571-8056-80cbd0134066)

* Then I navigated to the Lambda console and created a new function with the existing Lambda execution role
* Then I edited the general configuration to timeout for 1 minute.
* Then I edited the VPC settings of the function to be inside the A4L VPC and selected the private subnets.
![unnamed (54)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/a93d6e58-94d7-4e06-9b22-4f29ba61c61c)
![unnamed (55)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/7d196421-4eef-4a47-8f75-d5869efe45a0)

* Then I added the EFS file system with the created access point
![unnamed (56)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9ae1f9cd-e701-4f80-94b4-a703aad12da3)

* Then I added this code:
````
import base64
import logging
import os
import json
import boto3
import urllib3
import uuid

logging.basicConfig()
log = logging.getLogger() 
log.setLevel(logging.INFO)


def lambda_handler(event, context):
    urls = []
    http = urllib3.PoolManager()
    
    for i in range(10):
        r = http.request('GET', 'http://thecatapi.com/api/images/get?size=med&format=src&type=png&api_key=8f7dc437-0b9b-47b8-a2c0-65925d593acf')
        with open('/mnt/efs/'+str(uuid.uuid1())+".png", "wb" ) as png:
            png.write(r.data)
    
        
    return {
        'statusCode': 200,
    }
````

* Then I ran the test, which added cat images to the website:
![unnamed (57)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/93f8c821-473b-45f1-8220-e0c5268d33c3)

* Then I navigated to the EventBridge console and and created a new rule to invoke the lambda function every 2 minutes.
* Now for every 2 minutes, there are new images added to the website:
![unnamed (58)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6ab28a5f-533f-447f-a3c1-cc7f96be0131)

