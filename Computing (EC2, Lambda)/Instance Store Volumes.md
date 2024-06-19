#### This section covers about using Instance Store Volumes on EC2 instances.

## Instance Store Volumes
* I created a new instance inside the A4L VPC Web Tier subnet (us-east-1a availability zone) with the instance type of m5dn.large.
![unnamed (49)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/5633cad6-53dd-46f4-932b-ccd7ff60be08)

* Then I connected using Instance connect and mounted the file system to the instance store directory (same process as above)
![unnamed (50)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6d454599-cfb0-4770-a7bc-b58cd7e8ee5d)

* However, when I reboot the system and connect again, the file system is not mounted because we didn’t configure it to do so.
![unnamed (51)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/cf5f0313-af76-4130-b097-a4103859c621)

* We can still see that the file I created is still there because instance store volumes do persist through restarts.
![unnamed (52)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/5aa6b5dc-51d9-41e2-a043-54e196c340c6)

* Now If I stop and start the instance, the public IPv4 address is reallocated and I can’t see the file I stored in the file system.
  * This is because if I restart an EC2 instance, it restarts on the same EC2 host, but if I stop and start an EC2 instance, it moves from one EC2 host to another

