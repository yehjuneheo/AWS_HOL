#### This section covers about creating a static website in S3.

## Creating a Static Website with S3
* First I created a new bucket with the name as the registered domain: yehjuneheotraining.com
* I unchecked ‘Block all public access’ because I will be hosting the website and created the bucket with the fault settings for the rest.
![unnamed (13)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/4fd99567-bb2d-40c7-81bd-8359f83fccb7)

* If I go in to the bucket and go to properties, I can the see static website hosting as disabled.
![unnamed (14)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/2b649618-7505-4ae4-8eba-8dbfcfb91ac3)
![unnamed (15)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f94a59a2-7fce-44e3-8655-c5e2ce350e7b)

* Then I uploaded the index.html and error.html files along with an image folder
![unnamed (16)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/80e0ac70-3c6b-41c8-be04-002ea5a74f5e)

* If I go into the website url, I get this error:
![unnamed (17)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/884d31a4-fc0c-49e8-8267-fa25e577d14b)

* So I added a bucket policy to allow all read object access to the bucket:
![unnamed (18)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/92ba9db9-c12c-4803-aca0-43afa7618f06)

* Now if I refresh the website, it successfully works:
![unnamed (19)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/26576bb6-e0d6-4ee3-ac9b-883a53aecc60)

* To host the website one my domain, I need to create a record for it from R53.
![unnamed (20)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/3f25e25d-5d52-4025-8e22-b53f92a275db)

* I chose the simple routing policy and defined a simple record.
![unnamed (21)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/c7f4cee4-8ec8-47aa-adc3-dd15c937f6b5)
* To match the bucket name, I didn’t specify any subdomain because the bucket name needs to match the record name exactly.

![unnamed (22)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/23c86939-452f-4e0f-9fb7-67ae236b25b1)
![unnamed (23)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/fe71459b-6a32-4e48-85ec-babd4f4b5ac6)

* Now I can see the website from the registered domain.

### What I Learned
* Routing type can be configured for S3 static website hosting too.
* I need to allow both public access from the console and the bucket policy for the static hosting to work
