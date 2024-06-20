#### This lab covers about seeing session stickiness in action.

## Seeing Session Stickiness in Action
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
![unnamed (51)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f6b4b300-a60b-46ad-bbc5-d675e0c2be78)

* This created 6 EC2 instances, and I opened the public IPv4 DNS address for each one on separate tabs.
![unnamed (52)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b876be9b-cefe-492d-add1-9e86e6b59a67)
![unnamed (53)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/074ea375-81cf-42b1-9234-345c58f4e0d9)

* Then I navigated to the load balancers tab and opened the DNS address associated to the application load balancer
![unnamed (54)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/82733193-d274-4d38-91a9-9c29dfc9141f)
![unnamed (55)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/aa872d7b-da07-4b12-82cf-6995ef5cc211)

* If I hard refresh the website, the instance ID changes every time because stick sessions not enabled.
* To enable it, I went to the Target Groups tab and edited the target group attributions to make the load balancer generate a cookie that lasts for a minute:
![unnamed (56)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/0386a481-20ca-40a8-bace-c21e72decb24)
![unnamed (57)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/73e32fbc-11a4-424c-aa46-6f4ecbdfdb66)

* Now even if my hard fresh the website, I get assigned to the same EC2 instance every time.
![unnamed (58)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/c547d631-92c8-46f2-a936-c66f44dc12e4)

* If I stop a specific instance, the ALB will think that the instance has failed based on health checks and thus navigate me to another EC2 instance.
