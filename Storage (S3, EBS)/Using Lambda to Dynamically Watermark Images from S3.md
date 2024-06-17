#### This section covers about using Amazon S3 Object Lambda to dynamically watermark images as they are downloaded.

## Using Amazon S3 Object Lambda to Dynamically Watermark Images
* I created a new bucket with default settings and uploaded an amazon logo file
* Then I went into the access point section to create an access point:
![unnamed (58)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9b5a619e-9ead-4c16-9edf-aafcec38aa3c)
![unnamed (59)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/c5944605-44fc-4ae3-864a-3497e23b4889)

* Now I used AWS CloudShell to create a Lambda function for S3 GET requests to be made through an S3 Object Lambda Access Point.
* The bash code I used is provided here: https://aws.amazon.com/getting-started/hands-on/amazon-s3-object-lambda-to-dynamically-watermark-images/

* Then I went into the object lambda access points section to create a new object lambda access point.
![unnamed (60)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e9dd0494-36a1-42b1-927d-610634630d1f)

* The access point was successfully created along with the lambda function
![unnamed (61)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/5fc020c9-6073-442c-922b-6c0389f0d4dc)
![unnamed (62)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e4b26490-2c8f-4d28-8250-2ab2858d6f13)

* Now when images are downloaded, there are automatic watermarks added:
![unnamed (63)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/c43709ea-67df-4ca1-86a2-5dfef34afa90)

### What I Learned
* I need to make an object lambda access point to use lambda functions when dealing with objects in S3.
