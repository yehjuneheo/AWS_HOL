#### This section covers about configuring private S3 buckets so they can be only accessible from within a VPC.

## Private S3 Buckets
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
* The Cloud Template generated 3 buckets, where the objects are all publicly accessible.
![unnamed (38)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ba16713b-97ee-445d-8509-11f1b0449d96)

* Then I navigated to the EC2 console and connected to a private EC2 instance using session manager
* The template already created a gateway endpoint for S3 in the VPC, I can list all the S3 buckets:
![unnamed (39)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e0748c16-f3f7-4b8d-bbe4-12d8beab8af4)

* Now I will be configuring the private EC2 instance to be able to only access the catpics bucket, not the dogpics bucket.
* So I navigated to the VPC console and selected the S3 gateway endpoint.
![unnamed (40)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/cea89d3d-197b-4efe-9eb2-954c2bc832ed)

* Then I the edited a custom policy:
````
{
    "Version":"2012-10-17",
    "Statement":[
    {
        "Effect":"Allow",
        "Principal": "*",
        "Action": "s3:*",
        "Resource":[ 
        "arn:aws:s3:::PRIVATECATSBUCKETNAME/*",
        "arn:aws:s3:::PUBLICCATSBUCKETNAME/*"
        ]
    },
    {
        "Effect" : "Allow",
        "Principal" : "*",
        "Action" : "s3:ListBucket",
        "Resource" : [
        "arn:aws:s3:::PRIVATECATSBUCKETNAME",
        "arn:aws:s3:::PUBLICCATSBUCKETNAME"
        ]
    },
    {
        "Effect" : "Allow",
        "Principal": "*",
        "Action":[
        "s3:ListAllMyBuckets",
        "s3:GetBucketLocation"
        ],
        "Resource" : "*"
    }
    ]
}
````

* Now if I go back to the session manager and try accessing the buckets, I can’t access the dog bucket.
![unnamed (41)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ebe06683-9c64-4713-94cf-09e281b43dc0)

* I can also edit the bucket policy to allow access from the same VPC so that it can’t be reached from my command line.
