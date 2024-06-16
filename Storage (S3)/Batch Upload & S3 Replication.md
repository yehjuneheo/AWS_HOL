#### This section covers about batch uploading files to S3 using AWS CLI and replicating existing objects with S3 batch replication

## Batch Upload Files to S3 Using the AWS CLI
* I can batch-upload files from AWS CLI
* I created a new bucket using: aws s3 mb s3://my-first-backup-bucket-yehjune2
* I uploaded using aws s3 cp "ReadMe.txt" s3://my-first-backup-bucket-yehjune2
![unnamed (36)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/78650acf-6e9a-450a-849d-241edd909288)

* This looks useful when I am trying to upload a large number of files without manually uploading them one by one using the portal

## Replicate Existing Objects with S3 Batch Replication
* In this section, I will try replicating objects across two buckets that are located in different regions

* First I created two buckets in different regions with bucket versioning enabled.
![unnamed (38)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d2cd1eb9-7f13-4ea2-a828-845773ca6e2c)

* From the source bucket management, I created a new replication rule:
![unnamed (37)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b9bd3352-c13a-478c-81a5-990eee720cd2)

* I configured to limit the scope to only objects that have the prefix ‘Finance’
* Then I chose the destination bucket to be the other bucket that I created.
![unnamed (39)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ab96a1cd-595d-4c25-bc73-8a17c86b14d8)

* I enabled Replicate objects encrypted with AWS Key Management Service (AWS KMS)
![unnamed (40)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/3c9c1ea5-6b82-4432-9f9d-3f6bdcf877dc)

* I didn’t change the destination storage class and successfully created the replication rule:
![unnamed (41)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/a42b3d70-2ceb-4ee7-b0c0-810e4f9cb266)

* Then I created a batch operations job to automatically run when its ready.
![unnamed (42)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/aa05859d-eec5-49f5-8bd6-964bce5f8b0b)

*  You can also create S3 Batch Replication jobs for existing replication rules in S3 buckets from the S3 Batch Operations console.

*  After, when I created an S3 Folder with the Finance prefix from the source bucket, I found out that it was replicated to the other bucket too.
![unnamed (43)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/bb871e3c-f1ab-4581-93c7-0ba849fbd088)
![unnamed (44)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6aade4d6-efc4-4ffa-b3ef-2d6bf869fe6f)

### What I Learned
* Replication rules are created from bucket properties and it needs to be enabled.
* It is easier to use batch uploads when you have larger number of files instead of using the portal.
