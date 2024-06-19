#### This section covers about configuring multi-region access points

## Multi-Region Access Points
* First I created two buckets, one in region us-east-1 and one in region ap-northeast-2
![unnamed (91)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/97218382-6400-4b9c-b647-03078a8f5eee)

* Then I created a multi-region access point for the two buckets
![unnamed (92)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/8334d27b-7aa2-48ae-b439-bad05e63dbd6)
![unnamed (93)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/76c45f9c-66bf-47e4-a736-18c74d5ea5a3)

* I can also view the replication and failover overview
![unnamed (94)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/cdec62bf-9b92-4636-a3d4-5ad01f5c3e43)

* Then I created a replication rule for the access point, which will overwrite existing replication rules for specific buckets.
![unnamed (95)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/732bb3a0-98a1-4dcd-b336-c8b23afdfbc7)

* Now I will test the access point from CloudShell
  * I first created a test file using: dd if=/dev/urandom of=test1.file bs=1M count=10
  * Then copied the file to the multi-region access point: aws s3 cp test1.file s3://arn:aws:s3::992382454895:accesspoint/mcr786pshkhs7.mrap

* Now I can see the files from both buckets
![unnamed (96)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/3b164782-48f1-4b65-921e-3bed94caa29f)
![unnamed (97)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/49928c7b-9159-4374-bf07-7e2c9cb7a1e4)

* This works from any region and replicates the test files onto both buckets

