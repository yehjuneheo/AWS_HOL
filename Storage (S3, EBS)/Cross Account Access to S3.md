#### This section covers about setting up cross account access to S3.

## Cross Account Access to S3
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
![unnamed (13)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/69683c10-71e6-4a6f-ac3f-43be8e2475d0)

* I used my PROD account to create the resources, and the template created 3 bucket URLs.
* Then I switched back to my general account and tried accessing the bucket, which didn’t work.
![unnamed (14)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/dacea308-4fe7-4264-a0f3-24d8cbb4433c)

* To allow bucket access for the general account, I am first going to try editting the bucket ACL.
* For this I first copied the Canonical ID of the general account
![unnamed (15)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/677c7fd8-0b46-45f5-808c-d343f0ee79d7)

* Then I moved back to the S3 console from the PROD account and edited the ACL for the bucket by adding an external account:
![unnamed (16)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d407a667-8326-4c04-84cd-e9e83949ce21)

* Now if I go back to the general account, I can upload files to the bucket.
* However, if I go back to the PROD account and try opening or editing permissions of the file, I don’t have access to it because the general account owns the file.
![unnamed (17)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/8b12fec0-05ab-47d4-bb65-d591ae3d9656)

* For the next bucket, I will use a bucket policy to allow cross account access.
* I editted the bucket policy to include this:
````
{
  "Version": "2012-10-17",
  "Statement": [
      {
          "Effect": "Allow",
          "Principal": {
              "AWS": "arn:aws:iam::REPLACEMEMANAGEMENTACCOUNTID:user/iamadmin"
          },
          "Action": [
              "s3:GetObject",
              "s3:PutObject",
              "s3:PutObjectAcl",
              "s3:ListBucket"
          ],
          "Resource": [
              "arn:aws:s3:::REPLACEME_BUCKETNAME/*",
              "arn:aws:s3:::REPLACEME_BUCKETNAME"
          ]
      }
  ]
}
````

* Then I switched back to the general account and I was able to upload a file from it. I don’t have all the access from the general account because the bucket policy granted a certain level of access.
* I can still not open the uploaded file from the PROD account. I can change the object ownership to be the same as the buket owner in the settings if I want to.

