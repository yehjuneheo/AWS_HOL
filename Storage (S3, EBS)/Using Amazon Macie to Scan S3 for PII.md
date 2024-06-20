#### This lab covers about using Amazon Macie to scan for personally identifiable information.

## Using Amazon Macie to Scan S3 for PII
* For this lab, I created 4 files:
  * Cc.text - Contains credit informations
  * Employees.txt - Constraints personal addresses
  * Keys.txt - Contains access credentials
  * Plates.txt - Contains custom data (Australian licence plates)

* Then I created a new S3 bucket with default settings
![unnamed (95)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f2b6ce3e-ea7e-49ba-bdc3-c293cd32d83d)

* Then I uploaded the 4 files to the bucket
![unnamed (96)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/613631cc-4195-4fe0-992f-3e36b9d4853a)
* I also uploaded a non sensitive file for testing purposes.

* Then I navigated to the Macie console, enabled Macie and created a new one-time job for the S3 bucket.
![unnamed (97)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f4367c47-059f-43a7-8f7e-08dc978944f9)
![unnamed (98)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/426a8d30-22ab-4b9b-86e5-f5f33748cc8b)

* It took about 15 minutes to run and I navigated to the show results tab after.
![unnamed (99)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ab4fc054-6bbb-4521-97f4-9b75bff2094c)
* Macie found one sensitive information about credit cards.

* To create a notification weh insensitive information is detect, I navigated to the SNS console and created a topic with default standard settings.
![unnamed (100)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9e21a96f-36ea-4f54-939c-96a530c6baff)

* Then I created a subscription using my personal email endpoint.
![unnamed - 2024-06-21T005957 035](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/4aa98171-d3d3-4e91-9479-8b1b6d4004ea)
![unnamed - 2024-06-21T010000 617](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9f9f82f5-7c9e-48ba-807a-0bed2f304fa5)
* Then I confirmed the subscription from my email iinbox.

* Then I navigated to the Amazon EventBridge console and to create a new rule.
![unnamed - 2024-06-21T010028 074](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/c6743263-464d-4c37-8113-a418f5bea0cf)

* I used an event pattern and selected Amazon Macie as the service with the event type as Macie Finding.
![unnamed - 2024-06-21T010031 803](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ff90a900-7048-4e98-8d8f-9fb5b156c6ac)

* Then I selected the SNS topic as the target.
![unnamed - 2024-06-21T010035 824](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/589a20e4-c58b-408e-9de8-4105dc32d3a0)

* Then from the Macie console, I created a new custom data identifier to identify license plates. This is the regular expression I used:
````
([0-9][a-zA-Z][a-zA-Z]-?[0-9][a-zA-Z][a-zA-Z])|([a-zA-Z][a-zA-Z][a-zA-Z]-?[0-9][0-9][0-9])|([a-zA-Z][a-zA-Z]-?[0-9][0-9]-?[a-zA-Z][a-zA-Z])|([0-9][0-9][0-9]-?[a-zA-Z][a-zA-Z][a-zA-Z])|([0-9][0-9][0-9]-?[0-9][a-zA-Z][a-zA-Z])
````
![unnamed - 2024-06-21T010040 598](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/cbed72cd-f64b-4c65-ae3b-81c9a2ba0840)

* Then I ran a new Macie job with the custom identifier. While running the job, I received several email notifications in JSON that warns me about the identified sensitive informations.
![unnamed - 2024-06-21T010055 801](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/62c16dd0-d97f-4909-9050-6adaf4e7d214)

* The job ran successfully while finding all of the personal informations:
![unnamed - 2024-06-21T010103 798](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/99ae295b-e2d8-41b3-ae82-e1a767582e38)

