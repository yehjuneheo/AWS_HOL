#### This section covers about setting up a root account, adding IAMADMIN to the root account, and setting up CLI using access keys.

## Setting up the Root Account
* I first went to the AWS website and set up a root account. 

  * You can make multiple accounts using same root account using +, ex) heoyehjune+general@gmail.com, heoyehjune+prod@gmail.com
  * You should always set up virtual MFAs for root accounts and root IAM accounts

* To create the budget
  1. I went to Billing and Cost Management and went into Budgets
![unnamed (2)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/01d640aa-bbfe-42ea-bf92-12ab58dba054)
  2. I picked the monthly cost budget but you can choose a template or create your own custom one

## Setting up IAMADMIN Account
* It's a good practice to not use account root user for anything
* So I created a normal admin user to give full control over the account and I’ll use it for day to day operations
![unnamed (3)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/38a8e9a7-f666-4253-aed4-0711fbecd5d1)

* I want to give the IAMADMIN to have full administrator access so I gave the ‘AdministratorAccess’ permissions policies from attach policies directly
![unnamed (5)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d259d7f6-08e4-41ef-9d80-b5d3cae2e7aa)
* I copied down the sign-in url for IAM users and saved it locally
  * ex) https://yehjuneheo-training-aws-general.signin.aws.amazon.com/console

* I also set up the MFA since it is admin account
* I also created the root production account using heoyehjune+prod@gmail.com and I added MFA, budget, and set up IAMADMIN for the production account

## Creating Access Keys and Using CLI
* I want to be able to use the CLI to interact with AWS

* For this I need to create an access key:
  1. I went to IAM → Security Credentials → Create access key
  2. I am going to use it for the CLI so I picked that
![unnamed (6)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/2ad618b8-8f79-4086-83e3-3aebc751357e)
I got the Access Key ID and the Secret Access Key
  * Note that individual IDs can have max 2 sets of access keys

* I also created the same access key for the IAM production account

* Then I downloaded the AWS CLI V2 for Windows from  
![unnamed (7)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/06103889-ba0b-430b-b1c4-7733e19e73e3)  
This shows it is downloaded correctly  

* To configure the profile so run this line and past access key ID and secret access key: aws configure –profile iamadmin-general
* We need to use the named profile to access resources
  * ex) aws s3 ls –profile iamadmin-general
* If the secret access key is leaked, you can regenerate it
* I did the same configuration settings for the IAMADMIN production account

### What I Learned
* For every admin-level account, MFA should be enabled
* You should always avoid using the root account and use IAMADMIN instead
* Secret keys can be regenerated and you need to change the configuration after changing it



