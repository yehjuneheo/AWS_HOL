#### This section covers about using S3 versioning and object encryption for role separation.

## S3 Versioning
* First I created a new bucket with unchecked ‘Block all public access’ and enabled bucket versioning.
* I enabled static hosting, added the required bucket policy, and uploaded the index.html along with an image folder.
![unnamed (24)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/a1937dd9-702d-48ad-a763-55e343025aba)
![unnamed (25)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/4f8f62dd-4e0d-4d65-b1e6-5fec3495f269)

* I can toggle the show versions to view the version ids of the objects.
![unnamed (26)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/badaf2bf-7cab-4e91-a302-cf6a465e90da)

* I went into the images folder and uploaded another version of winkie.jpg but it only shows one file:
![unnamed (27)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/1de10da6-85d4-4322-8503-8d81b8d902e9)

* If I hit refresh, I see a new image for the website:
![unnamed (28)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/29506844-ae59-489f-ae9b-0b59de7a76b1)
![unnamed (29)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/60af6bc5-7810-459a-bcb0-bea4e21165e4)

* S3 always picks the latest object to be the default for the file when given multiple files of the same name.

* If I delete an object, it looks like it is deleted, but actually there is just a delete marker added.
![unnamed (30)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/8a568f25-3835-46d5-971c-901070645bf5)
![unnamed (31)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ad521e6a-efe3-402b-b5a8-4977c78cd581)

* If I delete the delete marker, it is actually undeleting the file which brings it back.
* If I delete a specific object version, it permanently deletes it and adds the next latest version to be the default for the file.

## S3 Object Encryption and Role Separation
* I first created a new bucket with default settings and a new key with no key administrative permissions selected.
![unnamed (32)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9e2e345b-cd2e-42f0-8b9f-b1263922fbf1)

* When uploading an image, I selected specify an encryption key from the properties section and chose SSE-S3 for the encryption type.
![unnamed (33)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/3c2b94a7-61c8-4767-85eb-4c15bfc9ce7f)

* For the second image, I selected SSE-KMS:
![unnamed (34)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/060e98f0-c7e2-4c37-b7b3-ddcc91e99f7f)

* I created the 2 objects and both can be viewed directly because I have full admin access in this account.
![unnamed (35)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d84e7fb0-4f8a-49c1-b218-4cef490006ee)

* However, if I add a KMS deny policy to my account from the IAM console, the KMS-encrypted image outputs an access error.
* This can be useful for role separation when accessing files.

### What I Learned
* Deleting a specific version when object versioning is toggled permenantly deletes the objects.
* Only users with the KMS key can view the object, even the account that uploaded the file needs the KMS key to view it.
