#### This section covers about implementing a template using CloudFormation

## Simple Automation with CloudFormation
* I will try implementing an existing template from learn.cantrill.io using CloudFormation
![unnamed (34)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/19c8f8dd-e709-4bf6-b282-2fa14467bcaf)

* CloudFormations parameters that I can modify. I am going to leave it as default for now.
![unnamed (35)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/2e8062a8-a7b3-4093-b5f1-6ae29441e7c7)

* I can view the physical resources being created based on the logical resources on the template in events
![unnamed (36)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/58a17265-ba48-4e8e-8b0b-c1f02fc2f143)

* I can view the created resources and outputs
![unnamed (37)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/983b0766-a8bb-4033-a00e-79c578cc8a5d)
![unnamed (38)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/3eb071f3-bbd9-4c90-a529-89bb4d9c82f2)

*  I can run session manager on the EC2 instance since it was configured with specific permissions to allow using it from the template
*  Deleting the stack deletes the stack itself along with the corresponding resources.
