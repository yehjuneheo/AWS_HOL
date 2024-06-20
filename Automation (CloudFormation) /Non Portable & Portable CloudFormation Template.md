#### This section covers about implementing non portable and portable CloudFormation templates.

## Simple Non Portable CloudFormation Template
* This section aims to understand why non portable templates are bad practice and shouldn’t be used

* First I am going to manually create a template written in YAML, guided by learn.cantill.io. This is the YAML file I created:
![unnamed (49)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6bb357ca-480c-4f71-b0eb-95ec22d223eb)

* Then I navigated to the CloudFormation console and created a new stack.
![unnamed (50)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/4395d017-35f4-4c68-87bc-7937d47bc920)
* The stack wasn’t able to be created because the bucket name was already in use.

* So I deleted the stack, changed the bucket name to a unique one, and created the stack again, which was successful.
![unnamed (51)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/74123b1a-1ce6-46c5-aa9b-908e5eb6b138)

* The limitation of this template is that if I want to create a stack again, it won’t work because the bucket name is already in use, which is not practical.
* Also, AMIs are unique to regions, so there also will be an error if I try to run the template in another region.
* These show why I should not use a non portable template to deploy stacks in CloudFormation.

## Portable CloudFormation Template
* First I downloaded 4 YAML files from learn.cantrill.io that includes nonportable.yaml, portable-stage1.yaml, portable-stage2.yaml, and  portable-stage3.yaml
* In the previous section, I saw that the nonportable.yaml file can’t be used twice. However for the portable-stage1.yaml file, the bucket name has been deleted, which means there will be no error due to non-unique bucket names.
![unnamed (52)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/aa4d3207-333f-4859-984a-75d12651cfb2)

* However, this template still can’t be worked in another region. So for the stage 2 file, rather than explicitly providing the image ID, instead the template uses AMIid parameter
![unnamed (53)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/60713ea7-8f09-4b2d-abba-07959eeb0d90)

* This allows me to configure the AMIid parameter from the CloudFormation console:
![unnamed (54)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/bf104054-48ed-4824-8876-5c45e518517c)

* It is always good practice to provide a defalt value for parameters so that the user doesn’t have to manually input all the parameters.
* So the last portable-stage3.yaml file enhances this feature:
![unnamed (55)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/15506e20-ba83-4b7b-a200-294770cfef03)
* The defaulte value automatically references the latest amazon linux AMI id.
* This is a fully portable form that can be used multiple types to generate resources easily.
