#### This section covers about creating and using CloudFormation custom resources.

## CloudFormation Custom Resources
* This lab will use CloudFormation custom resources to use Lambda functions to download images and populate an empty bucket.
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
![unnamed (88)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ac3d65cf-d036-477c-a6c0-2b03e01f2b7a)

* It created a simple bucket, which I uploaded a few files into it. Then I tried deleting the stack.
![unnamed (89)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/41698668-69eb-4a91-9aea-f8f6def26718)

* Now the other template has a custom resource (lambda function), that will copy objects into the bucket when it is created, and delete objects when the bucket is being deleted.
![unnamed (90)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/24f24d09-3ed5-47cd-9be9-b0d071885eb2)

* The stack has been created and I navigated to the created S3 bucket.
![unnamed (91)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/a52c6d27-ecd3-4910-9032-a2be92cd7405)

* I can see that the bucket already has three image files, which were retrieved from another public source bucket.
![unnamed (92)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/37a0b46a-1a88-4c9a-90f6-175ad2fb849d)

* I can also see the logs created from Lambda inside the CloudWatch console.
![unnamed (93)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/34cafc6b-2b7c-417a-8c8b-5eb49af0ba08)

* I can also delete the stack without emptying the bucket
![unnamed (94)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/5f9b06cf-c33e-460f-aa61-a47c6974f717)

