#### This section covers about setting up AWS Organizations & configuring service control policies

## Using AWS Organizations
* I want to make an environment where I can efficiently transit between dev and prod roles

1. I went into AWS Organization and created an organization, which makes the root account the management account by default
2. I logged in to the production account and copy the account ID
3. Then I pasted the account ID from create new account in general account:
![unnamed (8)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/430ea9b5-7895-4a96-a1fd-e4adcff8f30e)
4. I logged back into the production account and accepted the invitation

* Then I generated IAM Roles for each DEV and PROD roles
1. Went into IAM Roles and created the production role first from the production account
![unnamed (10)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/1e441de6-f4b7-42e3-9f41-b8fded33ab52)
![unnamed (11)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f96ae17f-0de5-4a59-ba1e-4b7689098802)
2. Selected AWS account as the trusted entity type and pasted the general account ID
3. I gave the AdministratorAccess permission
4. I named the Role as OrganizationAccountAccessRole and created the role

* This allows this role to trust the general account
* Now I can switch role to the production account from general account
![unnamed (12)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/a7a870d2-79cd-41d5-a962-23fdd6af70e4)

* I did the same to create a development aws account from AWS Organization
![unnamed (13)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/65e6e5ab-a008-4a5c-ad78-e50697b688cd)

* Now I have three accounts: General, Production, and Development
* I can easily role switch to each accounts from the general account

* Then I created two organizational units and moved the dev and prod accounts to their respective OU.
![unnamed (14)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e61bb487-bba3-4056-aeb0-b8f3069a7332)

## Using Service Control Policies
* I want to add restrictions to the different accounts when accessing resources using SCP.
* I logged in to the PROD account and setted up an s3 bucket and uploaded an image file
![unnamed (15)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/7fb5183d-847a-4e84-a53a-5e15f2e04bbc)
![unnamed (16)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/960a7b27-6f20-4bdf-ae66-ed3319d08f85)
* This is possible because the PROD account has full administrator access.
* I will be limiting this by using service control policies
* I logged back into the general account and went to AWS Organizations Policies
* I enabled the Service Control Policies, which by default has a policy named ‘FullAWSAccess’, which has effect on the whole organization
![unnamed (17)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e410b930-6d2a-4f09-a740-fdf521822f9e)

* I created a new policy to deny all access to S3:
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "*",
            "Resource": "*"
        },
        {
            "Effect": "Deny",
            "Action": "s3:*",
            "Resource": "*"
        }
    ]
}

  ![unnamed (18)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/5243159d-4160-4fa6-a426-e583656755fc)

* Then I attached the policy to the PROD OU
![unnamed (19)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/c6fcd902-2a67-4eb0-b17a-54340008fa93)

* When I went back to the PROD account, I didn’t have access to the S3 policy
![unnamed (20)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6c2f07a4-20ed-4146-8a63-e7b52920e738)

