#### In this project, I will implement a fully serverless application using step functions, Lambda, API Gateway, and S3 static website hosting.

## Configure SES
* First I navigated to the Simple Email Service console, and created an identity for the email address where the emails will come from the application.
![unnamed (13)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/13323cc2-f78d-4f15-8335-37cadaf38ebb)

* I also configured another email address, which the customers will send the email to.
![unnamed (14)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/49451d56-6751-40f2-957a-1282d98162f9)

## Using Lamba to Send Emails
* For this part, I used the CloudFormation template provided by learn.cantrill.io to automate the creation of the lambda role.
![unnamed (15)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/13962bdc-80ca-4f50-aecc-41e0ccaae246)

* I can see that the created role has permissions on CloudWatchLogs, SNS, and SES.
![unnamed (16)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/4a054a2f-a305-4149-a39f-a25e5b23a46f)

* Then I navigated to the Lambda console and created a new function with the existing lambda role.
![unnamed (17)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/848f6102-e309-4f1f-aabe-40e948f29c91)

* Then I used this code for the lambda function:
````
import boto3, os, json

FROM_EMAIL_ADDRESS = 'heoyehjune@gmail.com

ses = boto3.client('ses')

def lambda_handler(event, context):
    # Print event data to logs .. 
    print("Received event: " + json.dumps(event))
    # Publish message directly to email, provided by EmailOnly or EmailPar TASK
    ses.send_email( Source=FROM_EMAIL_ADDRESS,
        Destination={ 'ToAddresses': [ event['Input']['email'] ] }, 
        Message={ 'Subject': {'Data': 'Whiskers Commands You to attend!'},
            'Body': {'Text': {'Data': event['Input']['message']}}
        }
    )
    return 'Success!'
````
* Then I copied down the function arn so I can use it later.

## Configure State Machine
* In this part, I will create the role that the state machine will use, then create the state machine, and then integrate it into the lambda email function.

* First, I used the CloudFormation template provided by learn.cantrill.io to automate the creation of the IAM role.
![unnamed (18)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ed8e8aed-948f-427b-83bc-833c2a4c13be)

* The role has permissions to CloudWatchLogs and invoking Lambda and sending SNS.
![unnamed (19)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6f24caf9-30db-4b29-83bd-a478ab2c8e6c)

* Then I navigated to the Step Functions console and created a new state machine from a blank template.
![unnamed (20)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d2bf8fbf-5abe-455c-be09-dbdc9d219f5b)

* Then I used this JSON file provided by learn.cantrill.io and pasted it to the code section.
````
{
  "Comment": "Pet Cuddle-o-Tron - using Lambda for email.",
  "StartAt": "Timer",
  "States": {
    "Timer": {
      "Type": "Wait",
      "SecondsPath": "$.waitSeconds",
      "Next": "Email"
    },
    "Email": {
      "Type" : "Task",
      "Resource": "arn:aws:states:::lambda:invoke",
      "Parameters": {
        "FunctionName": "EMAIL_LAMBDA_ARN",
        "Payload": {
          "Input.$": "$"
        }
      },
      "Next": "NextState"
    },
    "NextState": {
      "Type": "Pass",
      "End": true
    }
  }
}
````

![unnamed (21)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/a21d10cb-5a45-4005-92fe-0e34fd6b66c3)

* I changed the EMAIL_LAMBDA_ARN to the arn that I copied down in the previous section, and I configured the state machine name and type to standard. I chose the existing execution role and changed the log level to ALL.

* The state machine was successfully created, and I copied down the arn for the state machine.
![unnamed (22)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ebc144fc-fef5-4416-9a52-2b0768adf345)

## API Gateway and the Supporting Lambda Function
* In this part, I will create the API and the API Gateway so that my client application has an endpoint to use to talk to my application, along with a Lambda function that provides the compute required to service that API.

* First I created a new Lambda function with the existing role.
![unnamed (23)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/61e67fc4-a802-4d12-9a41-b7e0eafa0dfb)

* Then I used the source code provided by learn.cantrill.io for the Lambda function. This code functions as the supporting compute reousrce for our API.
````
import boto3, json, os, decimal


SM_ARN = 'YOUR_STATEMACHINE_ARN'


sm = boto3.client('stepfunctions')


def lambda_handler(event, context):
    # Print event data to logs ..
    print("Received event: " + json.dumps(event))


    # Load data coming from APIGateway
    data = json.loads(event['body'])
    data['waitSeconds'] = int(data['waitSeconds'])
   
    # Sanity check that all of the parameters we need have come through from API gateway
    # Mixture of optional and mandatory ones
    checks = []
    checks.append('waitSeconds' in data)
    checks.append(type(data['waitSeconds']) == int)
    checks.append('message' in data)


    # if any checks fail, return error to API Gateway to return to client
    if False in checks:
        response = {
            "statusCode": 400,
            "headers": {"Access-Control-Allow-Origin":"*"},
            "body": json.dumps( { "Status": "Success", "Reason": "Input failed validation" }, cls=DecimalEncoder )
        }
    # If none, start the state machine execution and inform client of 2XX success :)
    else:
        sm.start_execution( stateMachineArn=SM_ARN, input=json.dumps(data, cls=DecimalEncoder) )
        response = {
            "statusCode": 200,
            "headers": {"Access-Control-Allow-Origin":"*"},
            "body": json.dumps( {"Status": "Success"}, cls=DecimalEncoder )
        }
    return response


class DecimalEncoder(json.JSONEncoder):
    def default(self, obj):
        if isinstance(obj, decimal.Decimal):
            return int(obj)
        return super(DecimalEncoder, self).default(obj)
````

* I changed the 'YOUR_STATEMACHINE_ARN' to my state machine arn and deployed the code.
* Then I navigated to the API Gateway console and built a REST API.
![unnamed (24)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/a217a68c-ce9e-421d-ba59-629937a24591)
![unnamed (25)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/0ce5feda-b8ce-47d6-9849-5dfef1c61bd7)

* Then I created a resource with petcuddleotron as the name with the CORS option enabled.
![unnamed (26)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/01d8ebf9-4f69-4d8a-a0a8-ffc2a201a762)

* Then I created a method inside the resource with POST as the method type and chose the created api_lambda function. I also enabled Lambda proxy integration and created the method.
![unnamed (27)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9f43d66c-6102-4a3e-a538-84b1b22954cd)
![unnamed (28)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/78bb1a8e-5ab4-41ab-b68e-e12c9c98bc76)

* Then I deployed the API as a prod stage, and copied down the invoke URL: https://11jpkud3gg.execute-api.us-east-1.amazonaws.com/prod
![unnamed (29)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9d55fef3-7a7a-4080-aa28-0ba9ce52a134)

## Implementing Static Frontend Application
* In this part, I will be hosting a static website in an S3 bucket and testing the application.

* First I navigated to the S3 console and created a new bucket with blocked all public access disabled
* Then I used this bucket policy to allow access:
````
{
    "Version":"2012-10-17",
    "Statement":[
      {
        "Sid":"PublicRead",
        "Effect":"Allow",
        "Principal": "*",
        "Action":["s3:GetObject"],
        "Resource":["REPLACEME_PET_CUDDLE_O_TRON_BUCKET_ARN/*"]
      }
    ]
  }
````

* Then I enabled static website hosting and installed the necessary frontend files provided by learn.cantrill.io
* I changed the serverless.js file to use my API Gateway endpoint URL and uploaded the files to my bucket.
````
var API_ENDPOINT = 'https://11jpkud3gg.execute-api.us-east-1.amazonaws.com/prod/petcuddleotron';
````
![unnamed (30)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/ea6078d9-6f87-4f1f-a7d3-718f40cdf724)

* Then I opened the static website URL and tested the application.
![unnamed (31)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b6437e33-13c4-4bf6-89c3-835bc5242eb7)
![unnamed (32)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/9a017513-bf7c-40bc-bbe2-4733654b04c1)

* Now if I go to the Step Functions console, I can see that there is a running exeuction for the state machine.
![unnamed (33)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/b18a9a93-f3ab-4912-87e8-b9247024672b)

* I can visually see where the state is, which will be in the Timer state for 120 seconds
![unnamed (34)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/6780ebd4-7745-4340-899d-f9fc4e8a7672)

* I can also see the parameters passed through the API Gateway, API Lambda, and the state machine.
![unnamed (35)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/d4915909-0bcd-4fc4-8680-ad288dab7c7d)

* After the execution was complete, I successfully received the email.
![unnamed (36)](https://github.com/yehjuneheo/AWS_HOL/assets/51499085/2b93178f-3602-4bcf-ac27-dff9a980dbd2)

### What I Learned
* I learned that I can visually see the timeline of the state machine and logs to know what state I am in during the execution process.
* I also learned that I need to configure an API Gateway for the client side application to invoke my lamda function.
