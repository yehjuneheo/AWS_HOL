#### In this project, I will create a simple site-to-site VPN between AWS and a simulated on-premises network.

## AWS Side VPN Configuration
* First I subscribed to the pfsense software for this project.
![unnamed (37)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/85babbb3-26ce-4ef1-ba87-f8bde3d66424)

* Then I navigated to the EC2 console and created a .pem key pair named infra.
* Then, I used the CloudFormation template provided by learn.cantrill.io and created the stack with the newly created key pair.
![unnamed (38)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ce8c802a-46a6-406d-8c36-8229dd7bd675)

* Then I navigated to the VPC console and created a customer gateway with the outputted onpremRouterIP value.
![unnamed (39)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/aec91a4a-594e-472d-ad34-24dcaf475348)

* Then I created a virtual private gateway and attached it to the A4L VPC.
![unnamed (40)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/8a183e77-276f-4218-a9d9-bbe667631df5)
![unnamed (41)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e3b2105f-cd8e-48b2-bb59-a69a88005e0b)

* Then I navigated to the Site-to-Site VPN Connections section and created a new VPN connection
![unnamed (42)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/3c8095c9-8dbc-407f-91bc-cb8b485415d2)

* I configured the VGW and CustomerGW, and chose static for the routing options, and provided the IP range used by the simulated on-premises network.
* While the VPN is setting up I downloaded the configuration with pfSense as the vendor.
* The VPN connection was successfully created.
![unnamed (43)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/772b70b9-abe0-4094-a15a-6e24a8be2f35)

## pfSense Router Configuration
* In this part, I will configure the simulated on-premises side of this project.

* First I navigated to the EC2 console, where 3 instances are running that were created by CloudFormation.
![unnamed (44)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/1dc44280-99a0-4e5d-881d-771fadf7a81b)

* I selected the onpremRouter instance and went into the get system log section to copy the changed ec2-user password.
![unnamed (45)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6f8634e7-4769-4924-a352-a90176dd1e04)

* Then I opened the IP address for the onpremRouter and signed in using the copied password.
![unnamed (46)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/7ca01531-d500-4183-a46f-f9c5edcc2b68)

* Then I navigated to the Interfaces Assignments tab and added the LAN network port.
![unnamed (47)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e3f4ec09-5166-4037-ac58-f5e389f4fe6d)

* Then I configured the LAN interface to use DHCP as the IPv4 configuration type.
* Then I navigated to the IPSEC VPN tab to create a phase one and phase two configuration for each of the AWS side endpoints.

* First I created a Phase 1 tunnel with the following configuration:
````
General information
 a. Disabled : uncheck
 b. Key Exchange version :V1
 c. Internet Protocol : IPv4
 d. Interface : WAN
 e. Remote Gateway: 52.21.153.230
 f. Description: Amazon-IKE-vpn-0b8b507f7b6b20973-0

 Phase 1 proposal (Authentication)
 a. Authentication Method: Mutual PSK
 b. Negotiation mode : Main
 c. My identifier : My IP address
 d. Peer identifier : Peer IP address
 e. Pre-Shared Key: 2w1kw3csN_L_aQtWfszlHlgYXloKpdsB

 Phase 1 proposal (Algorithms)
 a. Encryption algorithm : aes128
 b. Hash algorithm :  sha1
 c. DH key group :  2
 d. Lifetime : 28800 seconds

 Advanced Options
 a. Disable Rekey : uncheck
 b. Responder Only : uncheck
 c. NAT Traversal : Auto
 d. Dead Peer Detection : Enable DPD
    Delay between requesting peer acknowledgement : 10 seconds
    Number of consecutive failures allowed before disconnect : 3 retries

Then I created a phase 2 tunnel for the phase 1 tunnel using this configuration:
 a. Disabled :uncheck
 b. Mode : Tunnel
 c. Local Network : Type: LAN subnet
    Address :  ! Enter your local network CIDR in the Address tab
 d. Remote Network : Type : Network
    Address :  ! Enter your remote network CIDR in the Address tab
 e. Description : Amazon-IPSec-vpn-0b8b507f7b6b20973-0

 Phase 2 proposal (SA/Key Exchange)
 a. Protocol : ESP
 b. Encryption algorithms : aes128
  c. Hash algorithms : hmac-sha1-96
  d. PFS key group :   2
e. Lifetime : 3600 seconds

Advanced Options

Automatically ping host : ! Provide the IP address of an EC2 instance in VPC that will respond to ICMP.
````

* I successfully created the endpoint.
![unnamed (48)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d402d0fa-28ef-4ac7-a231-4f58d905ea08)

* Then I did the same process to create the second AWS endpoint and applied the changes.
![unnamed (49)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e880f098-7f61-47f9-994a-21f59878feef)

* Then I navigated to the IPsec status tab and manually connected the two phases.
![unnamed (50)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/98568cca-a999-4ce8-bd3b-adc14be96dbf)

## Routing and Security
* In this part, I will configure the AWS side and on-premises routing to allow traffic to each other.

* First I navigated to the Route Tables section inside the VPC console.
![unnamed (51)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/dddcdee2-e2fe-41f9-8ff7-c2fb87c17c82)

* Then I edited the route propagation to enable propagation on my VGW.
* Now I see the VGW route in the routes section.
![unnamed (52)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/82aea14c-2f73-4891-8507-e6a2d0df68c5)

* Then I edited rt-onprem-private route to include the AWS network IP range.
![unnamed (53)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/38866228-c02f-4076-bdcb-aad1d3c75596)

* Then I navigated to the security groups tab to edit security groups to allow connections from each other.
![unnamed (54)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/de47dbac-c262-4e64-b442-aa736c7425a7)

* First I edited the default A4L AWS SG to allow inbound traffic from the on-premises network:
![unnamed (55)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f315a90a-86f4-4f5c-b949-3ffd45d693ef)

* Then I edited the on premises security group to allow inbound traffic from AWS.
![unnamed (56)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/1e5944f9-618e-4290-8f58-ee26a1fed30a)

* Lastly, I edited the on-premises router security group to allow inbound traffic from the default security group used by anything else in the on-premises environment.
![unnamed (57)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/90a9b61b-7e0b-44e5-939c-0830df353b0b)

## Testing
* In this part, I will be testing the VPN connection.
* First I navigated to the EC2 console and connected to the onpremServer instance using a RDP client and Fleet Manager.
![unnamed (58)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/a69a885f-1fd3-4528-b434-54df3bf70171)

* I used the key pair generated previously to decrypt the password and open the fleet manager remote desktop.
![unnamed (59)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/846afc3f-5ca3-454b-8d08-bacaa8168af0)

* To test the VPN functionality, I am going to ping the AWS server.
* So I copied the private IPv4 address for the awsServerA instance and pinged it in the command prompt.
![unnamed (60)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/5fc0c9af-f350-4484-9cb6-4fc20f108279)
* I can see that I can successfully ping from the instance

### What I Learned
* I learned how to successfully establish IPSEC VPN tunnels from an on-premises network to the AWS network configuring gateways, route tables, and security groups.
* I also learned that I can simulate on-premises networks without the need to establish hardware requirements using services in AWS Marketplace.
* I also learned that I can easily configure the IPSEC phase tunnels using configuration files downloaded from AWS.
