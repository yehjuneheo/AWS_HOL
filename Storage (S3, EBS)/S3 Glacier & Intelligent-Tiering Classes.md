#### This section covers about retrieving from S3 Glaicer Deep Archive storage class and using the S3 Intellient-Tiering class.

## S3 Intelligent-Tiering Configurations
* I created a new bucket with default settings and added a file with Intelligent-Tiering as the storage class
![unnamed (47)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/bca6331d-61fb-4ccc-973c-91ca5f629605)

* Then from the bucket management console, I created a lifecycle rule:
![unnamed (48)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e36ad727-58ba-41d5-b26a-491977e25e53)

* I created the rule to transition all objects to the intelligent-tiering class after 0 days of creation
![unnamed (49)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/c5469138-a265-4439-ac71-bc77b6c1d7a2)

* I can activate the optional asynchronous Archive Access and Deep Archive Access tiers too:
![unnamed (50)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/03382060-1dd9-400d-91bd-2adda5cfeb87)
* For this to work, I need to add a tag which is opt-in-archive for me

* Now if I upload a file I can select that tag so that the file can be moved to the deep archive storage after 180 days:
![unnamed (51)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/c7f21863-2f5f-4a00-933a-a3f2173eba81)

## Retrieving from S3 Glacier storage classes
* I created a new bucket with default settings and added a file with Glacier Deep Archive as the storage class.
* After selecting the file, I got an initiate restore button on the information banner.
![unnamed (45)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/1153fff8-0d8f-49e4-a889-c284451878d3)

* I can set options for the number of days that the restored copy is available and select the retrieval tier
![unnamed (46)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/7ce520b8-f18b-474b-b50c-935352431aa6)

* After 12 hours, the object will be restored to the Standard-IA tier which will allow me to download it.

### What I Learned
* Tags are used to move objects between different tiers in Intelligent-Tiering storage.
* I need to initiate restore from the information banner to be able to download the object which are in the Glacier Deep Archive storage.
