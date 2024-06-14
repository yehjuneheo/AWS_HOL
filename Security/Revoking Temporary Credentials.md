#### This section covers about revoking temporary credentials for security purposes

## Revoking Temporary Credentials
* I want to revoke temporary credentials in case of security attacks when those credentials are leaked.
* I created a stack using CloudFormation from learn.cantrill.io
![unnamed (21)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/fb3456ce-27f8-4aaf-b783-f57cc5c29045)

* I went to the EC2 console and connected to the session manager for one of the instances.
* I used curl http://169.254.169.254/latest/meta-data/iam/security-credentials/ to get the instance role and retrieved the temporary credentials using curl http://169.254.169.254/latest/meta-data/iam/security-credentials/retrieved_instance_name
![unnamed (22)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/301ce425-8232-4a12-9189-c000c77c8fd2)

* I copied the access key ID, secret key, and temporary credentials and used those to log in via my Windows command prompt.
![unnamed (23)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/fc11f5ea-9650-4f98-9324-b3818148ab6b)
* I was able to access all of the resources from my command prompt using those credentials
* I need to find a way to prevent the credentials from being used, but the credentials have an expiration date that makes it unavailable to expire them.

![unnamed (24)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/68a95d2f-8f8e-4fc1-b3ed-5c31d9a56dfe)
* When I go back to the IAM Role console, I can find the role being used for the credential
* If I just delete the role, it will impact all of the EC2 instances running based on that role and I need to recreate the role with the right configurations. (admin overhead)
* I can also adjust the permission policies, which also is bad because it affects other EC2 instances.
* I want to invalidate only the temporary credentials that have been leaked
* For this, I can revoke the session:
![unnamed (25)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/c7940bef-4631-406c-a3b6-e5bbee5e212d)

* This adds another permission policy:
![unnamed (26)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/dad5640e-3b89-4049-b859-98bc15ecd6ec)

* Now my cmd doesn’t have access:
![unnamed (27)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/7f4b8595-f0a5-43ae-a7db-299d6754e630)
* This means my EC2 instances don’t have access as well. So I stopped the instances and started.
* Now the EC2 instances still have access to the AWS Resources, while the attacker doesn’t.

### What I Learned
* Instead of deleting or changing permissions to the IAM Role when it is leaked, I should revoke the temporary credentials.
* I need to stop and start the instances related to the IAM Role so that they can use the new credentials for operating.

