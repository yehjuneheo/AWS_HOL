#### This section covers about setting up AWS CloudWatch for monitoring an EC2 instance with a simple alarm for CPU utilization.

## Simple Monitoring with CloudWatch
* I am going to implement CloudWatch to monitor an EC2 instance.
* First I created a simple EC2 instance with the default settings for everything except enabling ‘Detailed CloudWatch monitoring’ in advanced settings
![unnamed (39)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/0d702e25-df7d-457e-a716-d084015110dc)

* I went into the CloudWatch console and created a new alarm for the specific instance on CPU utilization:
![unnamed (40)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/2da4f110-eac2-49a2-9af9-3d591ea04f69)
![unnamed (41)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/7d2a92b2-0c1a-4adc-99dc-600ebd9193a7)

* Then I made the condition to alarm when CPU Utilization is above 15%.
![unnamed (42)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/7eceae66-d8fe-45df-8c66-733787afab29)
![unnamed (43)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/448f4325-e7b0-4228-8e23-9d474d0f5f52)

* I can add actions for the alarm, but I won’t be adding anything for now.
* I left everything else as default and created the alarm
* Then I went into the EC2 console and connected using EC2 Instance Connect.
* I downloaded the stress package and configured ‘stress -c 1 -t 3600’ to see how the CloudWatch reacts when we run stress for 1 CPU for 3600 seconds.
![unnamed (44)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ea2ec65c-4b3d-4848-ab3a-fd6fdb4723fc)

* Before running stress, the alarm is in the OK state
![unnamed (45)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ce3bbdda-8a11-4894-80c7-cccfaa7db9a8)

* After running stress, the alarm is in the in-alarm state
![unnamed (46)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/7c96bcae-2e1b-46aa-b853-df72840ee12a)

#### What I Learnt
* I learnt that I need to enable Detailed CloudWatch monitoring from the EC2 instance to monitor CPU utilization
* I can configure various automatic actions based on the alarms.
* I can use the stress package from EC2 Instance Connect to check if the alarm is working well.

