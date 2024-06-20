#### In this lab, I will automate EC2 control using Lambda and Events

## Automated EC2 Control using Lambda and Events
* In this lab, I am going to create two instances and use Lambda functions with EventBridge to start, stop, and protect those instances.
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
![unnamed (59)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/37d7a4b3-2983-4b81-b85b-a68a97a86c58)

* Then I created a policy named Lambdastartandstop with this json permissions:
````
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource": "arn:aws:logs:*:*:*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "ec2:Start*",
        "ec2:Stop*"
      ],
      "Resource": "*"
    }
  ]
}
````

* Then I created a Lamda execution role and attached the created policy.
![unnamed (60)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b6e3ae11-cd2e-49f9-b357-d9f931f1a827)
![unnamed (61)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f138fcaf-09f2-4bed-bf53-52e5001e0e35)

* Then I went to the EC2 console and copied down the 2 created instances:
![unnamed (62)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/189ba3ff-23a2-40e1-86fa-8328874eaf0c)

* Then I went to the Lambda console and created a EC2Stop function with the execution role:
![unnamed (63)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/522e5898-d426-4ca0-84b3-d3cc62161099)

* Then I copy and pasted the provided code to stop EC2 instances:
![unnamed (64)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f3b6a9b8-b282-412d-bf4b-ea61cba3c429)
![unnamed (65)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b665cd65-1c0d-45aa-84b4-fb06d7a29f5a)

* Then I added environment variables to the function for EC2_INSTANCES:
![unnamed (66)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/4b70ac20-fce6-46f0-8dcf-0350ceb8e45f)

* Then I tested the function from the test tab and it was successful.
![unnamed (67)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ef3c1afe-5653-43f8-a607-f4293f5230be)

* I can also see that the EC2 instances were stopped:
![unnamed (68)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/703d1aff-a611-4ce8-8d4f-50eafafa1b3b)

* Now I did the same process above to create a new lambda function with EC2Start role using this code:
![unnamed (69)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/450e0e27-37be-452b-a3b9-a18fc42ae91d)

* I added the same environment variables and ran the test, which successfully worked to start the instances
![unnamed (70)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/59da5049-bc10-4602-8788-9afe20f638e5)
![unnamed (71)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/8896ea34-b7fc-437a-a8ca-7b45aff60706)

* Lastly, I created an EC2Protect lambda function, that will protect EC2 instances from stopping.
![unnamed (72)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/21561cc4-777c-41f9-9781-1de4c95ae76d)
* The function has events as inputs, so that when the lambda function receives an event that indicates that an EC2 instance is in a stop state, it will atuomatcially launch it back to the running state.

* Then I went into EventBridge and created an EC2Protect rule with EC2 Instance State-change Notification as the event pattern for the first instance.
![unnamed (73)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/72c4faf0-f9ac-43b4-94ce-239ab3df1df4)
![unnamed (74)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b0f45777-fbb9-4225-bd51-17004e4a0ee0)

* Then I set the target to be the EC2Protect Lambda function:
![unnamed (75)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/44d67846-be55-42dc-a470-d2d6a839488a)

* I successfully created the EventBridge Rule on the default event bus:
![unnamed (76)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/05ac85e9-e945-4819-b980-2c71b81bd1b2)

* Now if I try to stop the first instance, once it is in the stopped state, the lambda function is invoked and starts the instance right away.
![unnamed (77)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f22aaed8-ee0e-4aa9-894e-e64764fe8311)

* I can view the logs in CloudWatch because lambda function log their outputs to log groups with the same name by default.
![unnamed (78)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/3cc18148-2958-4ef8-a944-dba523fc9a93)

* Then I can view the detailed logs from log streams:
![unnamed (79)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d3cea30b-125d-4af9-931f-fa300a5af18f)

