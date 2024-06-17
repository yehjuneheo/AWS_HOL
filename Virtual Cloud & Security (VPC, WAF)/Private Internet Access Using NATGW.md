#### This section covers about implementing private internet access using NAT Gateways.

## Implementing Private Internet Access Using NATGW
* First I used the CloudFormation template provided by learn.cantrill.io to automate building the required resources.
![unnamed (27)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/5d836c86-128b-4645-b6c8-592c42181312)

* When I connect to the EC2 Instance using session manager, I currently cannot ping 1.1.1.1
![unnamed (281)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/016627cb-00c5-4493-bc5e-6ebdfd13c557)

* Then I created a NAT Gateway for the sn-web-A subnet with an allocated elastic IP.
![unnamed (29)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/cbeddbb3-bc3f-4dd5-97e2-59a9901f085f)

* I created 3 NAT Gateways, one for each web subnet.
![unnamed (30)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/3aa0bedf-e01d-4337-8947-51e61e52279a)

* Then I created three route tables for each web subnet.
![unnamed (31)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/973823be-2ae1-431e-ae42-1781f0d81a5c)

* Then I edited the routes for each route table to navigate to NATGW for every IP.
![unnamed (32)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/7e5dd19c-e72a-45e9-879b-ee2e64e5a670)

* Now I need to associate each route table with their corresponding private subnets.
![unnamed (33)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/45cb0492-3b9f-43d1-a6ba-2215fd573968)

* After configuring each route table, I went back to the session manager and tried pinging 1.1.1.1
![unnamed (34)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e549ce3e-15c0-40e4-a886-eb0834b6245e)
* Now it can successfully ping from 1.1.1.1

### What I Learned
* I can using ping 1.1.1.1 if I want to test if the instance is publicly accessible
* I always need to edit route tables and need to associate them to corresponding private subnets for NATGWs to work.
