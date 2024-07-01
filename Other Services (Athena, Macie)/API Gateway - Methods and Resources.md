#### In this lab, I am going to create a simple API using API Gateway and Lambda

## API Gateway - Methods and Resources
* These are the two lambda functions I used for this lab:

````
#api_listcats_resource.py
import json
import os

def lambda_handler(event, context):
  print (event)
  statusCode = 200
  return {
    "statusCode": statusCode,
    # Imagine this is being loaded from a database
    "body": json.dumps(["roffle", "truffles", "penny", "winkie"]),
    "headers": {
      "Content-Type": "application/json"
    }
  }
````
````
#api_root_resource.py
import json
import os

def lambda_handler(event, context):
  print (event)
  body = "cats are the best!"
  statusCode = 200
  return {
    "statusCode": statusCode,
    "body": json.dumps(body),
    "headers": {
      "Content-Type": "application/json"
    }
  }
````

* I first navigated to the API Gateway console and built a REST API
![unnamed](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/f6befb18-0df4-4db3-b028-279b2793f1e6)

* Then I created a new lambda function for api-root-resource and tested the function.
![unnamed (1)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/47e7b0d4-8ac1-4c8c-a160-5a867f9b1132)

* Then I created another function and tested it too.
![unnamed (2)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9beffd42-5c51-4c6e-8db5-a9a011f75029)

* Then I created a GET method for the API Gateway for the lambda function
![unnamed (3)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b1c6369d-7800-432d-a7cc-30de88a53246)

* Now if I deploy the API and invoke the URL, I can see the log from it:
![unnamed (4)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/872d0748-6d68-4800-bc10-e07e74f3a728)

* Then I created a new GET method for /listcats and deployed the API.
![unnamed (5)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ff2ba270-50e3-4fe6-b61c-08ab106d0db7)

* When I add /listcats to the invoke URL, I can see the new GET output.
![unnamed (6)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/e5d835ef-0a83-492c-840d-e4d92e8c5d96)

