#### This section covers about protecting data on S3 using object lock and checking the integrity of data in S3 with additional checksums.

## Protect Data on Amazon S3 Using S3 Object Lock
* I created a new bucket with default settings except object versioning and object lock enabled.
* Object Lock cannot be set when uploading the file so I need to go in to the object properties section to enable it:
![unnamed (52)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9d130062-4632-41c7-a311-c4e448952499)

* I configured object lock retention with compliance mode for one day:
![unnamed (53)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/055affb9-a251-4a97-9bd1-4a8a007300a1)

* Now if I try permanently deleting the file it shows an error:
![unnamed (54)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/30f6ac7d-0bea-4716-84d3-9c3a8658a0f4)

* In compliance mode, nobody can delete the object until the expiration date is met.

* I can also use Storage Lens to see statistics relating to versioning, encryption, replication, and object lock.
![unnamed (55)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/69a151e7-905e-45ad-bf44-18d77a2094d5)


## Check the Integrity of Data in Amazon S3 with Additional Checksums
* I created a new bucket with default settings.
* I then uploaded a new file with the additional checksums to be on from the properties sections:
![unnamed (56)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/043b631a-a2ee-461b-8f4b-60881a6ebdf1)

* From the file properties section, I can get the checksum value from the additional checksums section.
![unnamed (57)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b57fa5bb-a8dd-45ca-af10-01c61ff00cbd)

### What I Learned
* I can use additional checksums to check if the file is the same to the original file.
* Nobody including the account owner can delete an object with compliance mode.
