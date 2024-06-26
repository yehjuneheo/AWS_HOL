#### This section covers about using Amazon Workspace from a Windows computer.

## Amazon Workspaces
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
![unnamed (33)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/a112e4bd-2e61-4240-a519-c1a7bb2f7bf7)

* Then I navigated to the AWS Workspace console and provisioned an advanced workspace setup.
* I had to create a directory, so I created a Simple AD directory with a new organization inside the custom VPC
![unnamed (34)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/52ceef50-b3a7-47ea-9227-c69499eb7d14)

* To use the directory, I had to register it first. So I noted the subnet IDs for application subnet A,B, and C, then selected those.
![unnamed (35)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/8ce3ba7f-588f-4c53-8542-f10b739468b3)

* Then, I created an additional user with my personal email, and selected Standard with Windows 10 bundle for the configuration.
* Then, I created the WorkSpace with everything in default settings.
![unnamed (36)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/87260946-b0ef-4e77-9286-7e6d026ce790)

* After the creation was completed I got an email about the instructions to set it up.
![unnamed (37)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/8bda54ec-27f9-402f-802e-f159f20e8c40)

* Now I can follow the setup instructions to download the virtual desktop.
