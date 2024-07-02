#### In this lab, I am going to implement a dynamic, highly available accelerated VPN, which uses BGP for route advertisement. The on-premises environment will be simulated inside AWS.
![unnamed (49)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d3868d80-28c5-4bb3-a68d-642f1ba1fbc9)

## AWS and On-Premises Setup
* First I used the Cloudformation template provided by learn.cantrill.io to automate the building of some necessary resources.
![unnamed (50)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b1dea9dd-3a11-4ba1-90f6-0c54dce1b03b)

* Then I navigated to hte VPC console and created a customer gateway using the router1public IP address with 65016 as the BGP ASN. Then I created another customer gateway using the router2public IP address.
![unnamed (51)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/a0f9a596-da25-42c6-84b2-9044f5706f57)

* To make sure there is no connectivity right now, I navigated to the EC2 console (where I found that the template created 6 instances) and connected to the on-prem server2 instance using session manager.
![unnamed (52)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/c94e6400-a44a-4be8-b052-6699c5f5f8e6)
* I can see that there is no connectivity between the on-premises server and AWS instance.


## Transit Gateway VPN Attachments
* In this part, I am going to create the AWS side of the VPN architecture, including two VPN attachments for the transit gateway and configuring accelerated VPN endpoints.

* For this, I navigated to the VPC console and created a new transit gateway attachment for router 1.
![unnamed (53)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/40632648-e12e-43ec-a8ec-a84d6d135c9e)

* Then I created another one for router 2.
![unnamed (54)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9e1f4c2e-f678-4447-8129-265e5f45530a)

* Then from the Site-to-Site VPN connections tab, I downloaded the configuration files for both VPN connections.

* Then I filled out this template based on the configuration files:
````
# SHARED VALUES

ROUTER1_PRIVATE_IP                  =   192.168.12.57
ROUTER2_PRIVATE_IP                  =   192.168.12.8
ONPREM BGP ASN                      = 65016  
AWS BGP ASN                         = 64512  

# CONNECTION1 - AWS => ON PREM ROUTER1

CONN1_TUNNEL1_PresharedKey          =  UGz.gKgyJxzsJKIrzutn5uyRO7Tm3YGe
CONN1_TUNNEL1_ONPREM_OUTSIDE_IP     =  3.209.107.162
CONN1_TUNNEL1_AWS_OUTSIDE_IP        =  35.71.148.78
CONN1_TUNNEL1_ONPREM_INSIDE_IP      =  169.254.229.62/30
CONN1_TUNNEL1_AWS_INSIDE_IP         =  169.254.229.61/30
CONN1_TUNNEL1_AWS_BGP_IP            =  169.254.229.61

CONN1_TUNNEL2_PresharedKey          =  HW0Rir2PD9oE5UssEsJtVXZcsjYx92EK
CONN1_TUNNEL2_ONPREM_OUTSIDE_IP     =  3.209.107.162
CONN1_TUNNEL2_AWS_OUTSIDE_IP        =  75.2.123.82
CONN1_TUNNEL2_ONPREM_INSIDE_IP      =  169.254.62.230/30
CONN1_TUNNEL2_AWS_INSIDE_IP         =  169.254.62.229/30
CONN1_TUNNEL2_AWS_BGP_IP            =  169.254.62.229


# CONNECTION2 - AWS => ON PREM ROUTER2

CONN2_TUNNEL1_PresharedKey          =  KGo2EIaGD_Smyseh.Mh4WExlklWNtLhk
CONN2_TUNNEL1_ONPREM_OUTSIDE_IP     =  18.214.189.83
CONN2_TUNNEL1_AWS_OUTSIDE_IP        =  3.33.159.191
CONN2_TUNNEL1_ONPREM_INSIDE_IP      =  169.254.82.198/30
CONN2_TUNNEL1_AWS_INSIDE_IP         =  169.254.82.197/30
CONN2_TUNNEL1_AWS_BGP_IP            =  169.254.82.197

CONN2_TUNNEL2_PresharedKey          =  fmGTSgfoH_rbsH3qLg9If2vyTQ27lIoF
CONN2_TUNNEL2_ONPREM_OUTSIDE_IP     =  18.214.189.83
CONN2_TUNNEL2_AWS_OUTSIDE_IP        =  15.197.140.54
CONN2_TUNNEL2_ONPREM_INSIDE_IP      =  169.254.149.198/30
CONN2_TUNNEL2_AWS_INSIDE_IP         =  169.254.149.197/30
CONN2_TUNNEL2_AWS_BGP_IP            =  169.254.149.197
````


## IPSec Tunnel Configuration
* In this part, I am going to establish the IPSec tunnels by configuring the on-premises side of the architecture.

* First I navigated to the EC2 console and connected to the on-prem-router1 using a session manager.
* I navigated to the ipsec.conf using:
````
sudo bash
cd /home/ubuntu/demo_assets/
nano ipsec.conf
````

* Then I replaced the placeholders with real values from the template I configured above.
![unnamed (55)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d1dfca20-f69c-476b-91d9-e3a179f3fbfc)

* Then I also replaced the placeholders for ipsec.secrets:
![unnamed (56)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/849f77f5-a923-4968-9176-04c372e94d6a)

* Then I replaced the placeholders for ipsec-vit.sh
![unnamed (57)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/22fca632-3bfc-4dd0-9371-3957bf5257b6)

* Then I copied the configuration files into the ETC directory where they’ll be read by the strongSwan software, and make the script file executable.
````
cp ipsec* /etc
chmod +x /etc/ipsec-vti.sh
systemctl restart strongswan
````

* I can check that the tunnels are up and running using: ifconfig
![unnamed (58)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e2b69b87-4026-4149-8cd0-229d982e0c5a)

* Then I did same process for the on-premises router 2 instance.
* Then I navigated to the VPC console and verified that the IPSEC tunnel is up and running.
![unnamed (59)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/79940253-9e0f-4967-b168-aff83d954590)


## BGP Routing & Testing
* In this part, I am going to configure BGP to run over the IPSec tunnels.

* First, I connected to the on-prem router 1 using session manager and created FRR script executable and ran it to install BGP capability.
````
sudo bash
cd /home/ubuntu/demo_assets
chmod +x ffrouting-install.sh
./ffrouting-install.sh
````

* Then I did the same for the on-prem router 2.

* Then I connected back to the on-prem router 1 and used the following command lines to configure BGP:
````
vtysh
conf t
frr defaults traditional
router bgp 65016
neighbor CONN1_TUNNEL1_AWS_BGP_IP remote-as 64512
neighbor CONN1_TUNNEL2_AWS_BGP_IP remote-as 64512
no bgp ebgp-requires-policy
address-family ipv4 unicast
redistribute connected
exit-address-family
exit
exit
wr
exit
sudo reboot
````

![unnamed (60)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/8e702be5-6b37-4cc4-97b4-eeafd0d6d91f)

* Then I followed the same process for on-prem router 2.
* Now the instances have established a BGP session within AWS. I can verify it by going to the VPC console and Site-to-Site VPN connection tab, and go to tunnel details.
![unnamed (61)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/0b48aa3f-d7dd-48d4-b619-9c5b7d12af37)
* The tunnel status are up and there are 2 BGP routes configured.

* I can also see all of the networks being advertised by the on-premises infrastructure.
![unnamed (62)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/82fa7bc4-c224-4d43-a772-0c0c91876a71)

* Then I connected to the on-prem server 1 instance and tried pinging from the AWS instance.
![unnamed (63)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ef1c3084-8af0-4658-a2cf-df93a7a487ea)
* I can successfully ping from the on-premises server instance to the AWS instance.

### What I Learned:
* I learned how to configure transit gateways so that it supports multiple VPN connections with multiple gateways.
* I learned that I need to manually configure the IPSec tunnels in order to establish the connection.
* I also learned that I need further configuration on the ec2 instances to use BGP.



