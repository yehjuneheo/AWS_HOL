#### This section covers about using IAM Identity Center for single sign on purposes.

## Using IAM Identity Center
* First I navigated to the IAM Identity Center and enabled it.
* Then I edited the AWS access portal URL so that it is more simpler than the default one.
![unnamed (26)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/c319c8f3-f5f9-42b3-a160-ab72670f816f)

* Then I created a permission set with AdministratorAccess with a session duration of 4 hours.
![unnamed (27)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9da73893-a0fa-4061-9aac-7208810026aa)

* I created 4 permission sets with the following permissions:
![unnamed (28)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/24c63289-e10e-4c7a-a300-67d85167a1cf)

* Then I created a user named Sally that will have billing permissions. For this I had to created a new group named billing associated the user with the group.
* Then for the PROD, DEV, and general account, I assigned the billing group with billing permissions
![unnamed (29)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/21e68958-12a5-4d96-8357-5d0b48717406)

* Then in incognito mode, I used the access portal URL to login as sally, and set up the MFA.
![unnamed (30)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9da1dbce-c8d4-4596-a9d6-aacb1cdb34c5)

* Now if I click the single sign on link, I can login to the account as Sally, who has full access to the billing console.
![unnamed (31)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6a938945-f6e3-4b25-b292-c2e5cb441596)

* Then I navigated badk to the IAM Identity Center and created a new group named PowerUsers with sally in it.
* Then I assigned the AWS accounts to have permissions to PowerUserAcess.

* Then I signed out and signed in to the AWS access portal, which gave me the PowerUserAccess permissions.
## Using IAM Identity Center
* First I navigated to the IAM Identity Center and enabled it.
* Then I edited the AWS access portal URL so that it is more simpler than the default one.
![unnamed (26)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/c319c8f3-f5f9-42b3-a160-ab72670f816f)

* Then I created a permission set with AdministratorAccess with a session duration of 4 hours.
![unnamed (27)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9da73893-a0fa-4061-9aac-7208810026aa)

* I created 4 permission sets with the following permissions:
![unnamed (28)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/24c63289-e10e-4c7a-a300-67d85167a1cf)

* Then I created a user named Sally that will have billing permissions. For this I had to created a new group named billing associated the user with the group.
* Then for the PROD, DEV, and general account, I assigned the billing group with billing permissions
![unnamed (29)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/21e68958-12a5-4d96-8357-5d0b48717406)

* Then in incognito mode, I used the access portal URL to login as sally, and set up the MFA.
![unnamed (30)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9da1dbce-c8d4-4596-a9d6-aacb1cdb34c5)

* Now if I click the single sign on link, I can login to the account as Sally, who has full access to the billing console.
![unnamed (31)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6a938945-f6e3-4b25-b292-c2e5cb441596)

* Then I navigated badk to the IAM Identity Center and created a new group named PowerUsers with sally in it.
* Then I assigned the AWS accounts to have permissions to PowerUserAcess.

* Then I signed out and signed in to the AWS access portal, which gave me the PowerUserAccess permissions.
![unnamed (32)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/c7bebb1b-1577-4b26-82ad-f607d26b22a1)

