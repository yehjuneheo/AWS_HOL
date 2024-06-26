#### This section covers about implementing a CloudTrail to store logging data to S3 and also CloudWatch Logs.

## Implementing an Organization Trail
* First I navigated to the CloudTrail console and created a trail with CloudWatch Logs enabled.
![unnamed (42)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/cd136e70-dc2d-49b6-b1e8-c416a0eab840)

* For the log events I only chose the management events with read and write API activity.
![unnamed (43)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6eb1b914-e4ac-40d1-a205-5633ef679797)

* The trail was successfully created.
![unnamed (44)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/4915b01b-c6cb-424c-9081-ffc9c297d109)

* After a few minutes, there was a JSON file generated in the bucket.
![unnamed (45)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/1cc94d80-6b47-4b8c-bc41-10fdf9ea34d6)

* I can also see a CloudWatch log generated, where I can view the log stream.
![unnamed (46)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/db66a74c-2cd0-45f6-afdd-50335b94ee46)
