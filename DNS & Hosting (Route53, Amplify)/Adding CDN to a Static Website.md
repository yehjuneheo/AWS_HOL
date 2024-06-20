#### This section covers about adding a Content Delivery Network to a static website and also adding an alternate CNAME and SSL.

## Adding a CDN to a Static Website
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
![unnamed (13)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b66c24ed-86ba-4d3c-aa89-caa09a56b92b)

* The CloudFormation template outputted a website endpoint: http://cfands3-top10cats-2iyd9i4c6ezx.s3-website-us-east-1.amazonaws.com/
* This website is hosted using static hosting from a created S3 bucket:
![unnamed (14)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/1b5b1a87-477a-42e4-9c9d-23dd8e7c6f6f)

* Then I navigated to the CloudFront console and created a new distribution with CachingOptimized as the Cache policy.
![unnamed (15)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d417fd95-f581-4ae1-829b-09c812a7f99a)
![unnamed (16)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/0998e9ca-bfca-4dd9-a8b4-bded1e923bd6)

* I configured everything to be the default settings and created the distribution.
![unnamed (17)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/8e2f6357-00ac-458e-ab67-20acbfa1fc07)

* Now if I refresh the website, the contents will be cached in my nearest edge location to make faster delivery.
![unnamed (18)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b0b8bc44-2fb8-4220-81d8-4b4c13c36633)

## Adding an Alternate CNAME and SSL
* In this part, I will associated the above website to a registered domain.
* I first edited the CloudFront distribution to have a CNAME:
![unnamed (19)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/fbaf2425-c18e-45c2-b589-2a2e3024fdfb)

* Then I had to register a certificate to use for this process so I requested a public certificate with domain name as yehjuneheotraining.com
![unnamed (20)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/49bb84aa-d76f-4896-b78f-16b278d0b7ed)
![unnamed (21)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f4dd0d8e-24f7-4549-bf76-9ba3f5116fb3)

* Then I created a record for the domain hosted in Route53:
![unnamed (22)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6cb73219-fb79-46ee-9a4a-01f5cd04ab89)

* If I go into the Route53 console and go into my yehjuneheotraining.com hosted zone, I can see that the record is created:
![unnamed (23)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/774bc628-1c27-4fbf-b7d0-4b9bdabdcca2)

* Then I chose the newly created certificate for my CloudFront distribution and saved the changes.
* Then I created a new simple routing record for yehjuneheotraining.com hosted zone:
![unnamed (24)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b9ea5c76-0524-4e1b-93e6-df9a1a61e05a)

* Now I can see the website hosted from a CloudFront distribution:
![unnamed (25)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ea1c7f17-f8b9-42c6-a6f6-7214f09deb40)

