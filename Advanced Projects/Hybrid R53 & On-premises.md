#### In this lab, I am going to implement a hybrid DNS system between AWS and a simulated on-premises environment.

## Provision and Verify
* First I used the Cloudformation template provided by learn.cantrill.io to automate the building of some necessary resources.
![unnamed (19)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/1018a7fa-f76f-4bc2-a9e2-cfbe93bd9976)

* The template created 5 instances, and I tested them by connecting into them with session manager and tried pining app.corp.animals4life.org, which is a DNS domain also created by the template.
![unnamed (20)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/16817b55-42a7-47c7-a966-b1df0d05b99e)
* Currently, nothing is configured so the instance cannot ping from it.

## Connectivity
* In this part, I am going to establish the hybrid network connectivity using VPC peering.
* For this, I navigated to the VPC console and created a peering connection with the AWS VPC as the requester VPc and the On-Prem VPc as the accepter VPC.
![unnamed (21)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/92e42cda-b378-4098-b776-9575386243f7)

* Then I edited the route table for both AWS and On-prem so that both route tables has the IP CIDR range of each.
![unnamed (22)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ee58dd17-5b46-40e1-8724-a5d98fce4a8a)

* To test the connectivity, I copied the IP address of one of the instance hosted on the on-premises server and connected to an instance hosted on AWS and tried pinging from it.
![unnamed (23)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/298c46ab-e0d3-4cb7-b75a-5bab175ac376)
* I can see that the instance can ping from the IP address but it still doesn’t have DNS integration.

## Inbound R53 Endpoint
* In this part, I am going to create the inbound endpoint for traffic coming into AWS from the on-premises server.
* For this, I first navigated to the R53 console and created an inbound endpoint.
![unnamed (24)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9c68b823-1e88-47ba-89c0-8a0fd29f1705)

* Then I connected to an on-premises instance and navigated to the /etc/named.conf file and edited the zone part to this:
````
zone "aws.animals4life.org" {   type forward; 
  forward only;
  forwarders { INBOUND_ENDPOINT_IP1; INBOUND_ENDPOINT_IP2; }; 
};
````

* Now I can do a name server look up from the session manager.
![unnamed (25)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/7d47013c-63f5-439f-82ba-3abcd73699ee)

* Then I did the same thing for the instance hosted on another availability zone.
* Then I navigated to the application instance of the on-premise server and opened: /etc/systemd/resolved.conf
* I edited it by adding DNS= the private IP addresses of the on-premise DNS instances.
![unnamed (26)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ad6d778e-633f-46dc-9d76-8981ee0c6299)

* Then I rebooted and reconnected into the EC2 instance and tried pinging from the DNS domain.
![unnamed (27)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/1914a5d9-c506-4e2d-b1db-9b34f808b7bd)
* The pinging is working successfully.

## Outbound R53 Endpoint
* In this part I am going to create an outbound endpoint for traffic going out of AWS.
* I navigated to the R53 console and created a new outbound endpoint
![unnamed (28)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/aabc1f3d-cadf-4cff-9416-d92a65dbd1ee)

* Then I created a new rule for the outbound endpoint that targets the private IP addresses of the on-premise DNS instances.
![unnamed (29)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/27569a2a-7448-44f7-9806-8bef0820ea0f)

* Now I can see that I can ping app.corp.animals4life from my AWS instance.
![unnamed (30)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/fd731030-f2d2-4286-b1f8-2ad47c79236f)


### What I Learned
* I need to create both an inbound and outbound endpoint to successfully configure a working VPC peering.
* I need to use private IP addresses when trying to connect AWS and on-premises instances for a secure VPC peering connection.
