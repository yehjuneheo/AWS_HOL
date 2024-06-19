#### This section covers about configuring logging and metrics about EC2 on CloudWatch

## EC2 Logging and Metrics with CloudWatch
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
![unnamed (85)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/aa16b263-6199-4e93-8f7b-32363a37e217)

* Then I connected to the created EC2 instance using EC2 Instance Connect.
* I first installed the CloudWatch agent using this command: sudo dnf install amazon-cloudwatch-agent
* If I want this instance permission to interact with CloudWatch Logs, I need to give this instance permissions to interact with it.
* I went to the IAM console and created a role with two policies: AmazonSSMFullAccess and CloudWatchAgentServerPolicy.
* Then I attached the role to the EC2 instance and started the CloudWatch Agent configuration wizard using this command:  
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard

* I used the default settings with 3 log files to monitor:/var/log/secure, /var/log/httpd/access_log, /var/log/httpd/error_log
![unnamed (86)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ff7f4d73-7b2f-4c07-a053-2165c23d1fbf)

* Now if I go to the Parameter Store, I can see the configuration file:
![unnamed (87)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/420c5358-0513-4374-adfb-e23475a599ff)

* Before starting the agent, I need to run these few commands to ensure a successful launch:  
sudo mkdir -p /usr/share/collectd/  
sudo touch /usr/share/collectd/types.db  

* Now to run the agent, I used this command: sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c ssm:AmazonCloudWatch-linux -s
![unnamed (88)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/7ef7c702-36aa-491f-b0bf-cd4e8b8cf0fa)

* Now if I go to the CloudWatch console, I can see the created logs:
![unnamed (89)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/4e46da8a-6a4c-4cb6-9caa-23ab72316ad6)

* If I connect to the IP address for the EC2 instance, I can view the access logs from the /var/log/httpd/access_log file now.
![unnamed (90)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/51927f7b-dfab-4766-9082-e02537f2c6ac)

