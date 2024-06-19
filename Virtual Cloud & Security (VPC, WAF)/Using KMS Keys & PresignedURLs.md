#### This section covers about using KMS keys to encrypt and decrypt data, and also using PresignedURLs.

## Encrypting and Decrypting using KMS Keys
* First I created a new KMS symmetric key:
![unnamed (44)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/606292c5-512e-450c-abbc-931259f7391b)
* I added an alias as ‘catrobot’ and added my iamadmin account for the key administrator.

![unnamed (45)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/a461c8d8-27b9-44b3-9c82-cb5e63f6c74e)

* I opened the CloudShell and entered the commands for encrypting and decrypting a text file:
![unnamed (46)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/a906ae9c-15c9-4b9c-abc7-a6ad2d0aba18)
* If I try to open the encrypted file, I get a binary encrypted code.

* Then I deleted the key with delete scheduling action and set up the waiting period for 7 days for fast deletion.

## Creating and using PresignedURLs
* I first created a new bucket and uploaded an image file.
![unnamed (47)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e020338a-3ffb-4bcd-bab7-e1d3c11e233a)
* Currently, I can’t access be accessed without authentication.

* From CloudShell, I entered: aws s3 presign s3_URI –expires-in 180
  * This creates a pre-signed URL that allows anyone to view the image
![unnamed (48)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/1e6a5311-625e-4338-aeff-d26998ca26de)

* The pre-signed URL contains permissions from the account that generated the link so if access to S3 gets blocked from that account, the pre-signed URL doesn’t work too.
