# Lovevery iOS Engineer Homework
The scope of this small project is to build a native iOS application that can POST to and GET messages from a service that has a REST interface.  
The application should be able to perform the following operations: 

**Current API base URL (`API_BASE_URL`):** `<paste the ApiBaseUrl stack output here>`  
<sub>All URLs below are relative to this base, e.g. `{API_BASE_URL}/messages`. The Lovevery hiring team will provide the current value.</sub>

### POST Messages
Post messages to the API using the URL:  
`{API_BASE_URL}/messages`  

The format of the body is: 
```
{ 
  "user": "dan", 
  "operation": "add_message", 
  "subject": "pets", 
  "message" : "cats are grumpy" 
}
```
The operation parameter must be "add_message". Other fields can have variable input strings. The return value is the message that has been saved.

### GET All Messages
Get all messages from the API using the URL:  
`{API_BASE_URL}/messages`  

There are no parameters for this request. The response is JSON:
``` 
{ 
  "statusCode": 200, 
  "body": { 
    "dan": [ 
      { 
        "subject": "pets", 
        "message": "dogs are happy" 
      }, 
      { 
        "subject": "pets", 
        "message": "cats are grumpy" 
      } 
    ],
    "bob": [ 
      { 
        "subject": "bob stuff", 
        "message": "bob bob bob" 
      }, 
      { 
        "subject": "bob stuff", 
        "message": "there once was a guy named bob" 
      } 
    ] 
  } 
} 
```

### GET Messages by User
Get messages based on the submitter’s user name from the API using the URL:  
`{API_BASE_URL}/messages/{user}`  
<sub>_Note that_ `{user}` _is the same user field in the_ `POST` _operation._</sub>  

An example of the URL and response could be: 
```
{API_BASE_URL}/messages/dan

{ 
  "statusCode": 200, 
  "body": { 
    "user": "dan", 
    "message": [ 
      { 
        "subject": "pets", 
        "message": "dogs are happy" 
      }, 
      { 
        "subject": "pets", 
        "message": "cats are grumpy" 
      } 
    ] 
  } 
} 
```

### Deliverable
1. A GitHub repository that has been forked from `lovevery-homework-ios` containing the completed Xcode project.
2. A brief design document describing overall approach, design decisions, and tradeoffs made.
3. A basic test plan and the inputs used to test. Please include a description of how you would automate testing.

<sub>Note that the API service is of prototype quality, so there is limited error handling and recovery logic. Also, the API is only maintained in AWS Lambda memory, so if you haven’t posted any data in a while, a GET will return an empty object. If you have any questions about its operations or think something is off, please feel free to ask us about it.</sub>

---

## For Lovevery hiring team: running the homework API
The API is **not** permanently provisioned. It is a Lambda function behind a REST API Gateway, defined in `cloudformation/message-service.yaml`. Create it at the start of a hiring cycle and destroy it when the cycle ends.

> **Warning:** Every create (or recreate) of the stack produces a **new execute-api hostname**. After every create you **must** update the `API_BASE_URL` at the top of this README and anything already sent to candidates. Recreating or destroying the stack will break candidates who are mid-assignment.

### Prerequisites
- AWS CLI v2
- Credentials for the Lovevery Legacy AWS account
- Region `us-east-2`

### Create the stack
```sh
aws cloudformation deploy \
  --template-file cloudformation/message-service.yaml \
  --stack-name ios-homework-message-service \
  --region us-east-2 \
  --capabilities CAPABILITY_IAM
```
The `StageName` parameter defaults to `proto`; override it with `--parameter-overrides StageName=<name>` if needed.

### Read the URLs
```sh
aws cloudformation describe-stacks \
  --stack-name ios-homework-message-service \
  --region us-east-2 \
  --query "Stacks[0].Outputs" \
  --output table
```
- `ApiBaseUrl`: e.g. `https://<api-id>.execute-api.us-east-2.amazonaws.com/proto`; paste this as `API_BASE_URL` at the top of this README.
- `MessagesUrl`: `<ApiBaseUrl>/messages`

### Smoke test
```sh
API_BASE_URL="https://<api-id>.execute-api.us-east-2.amazonaws.com/proto"

# POST a message (no API key required)
curl -s -X POST "$API_BASE_URL/messages" \
  -H "Content-Type: application/json" \
  -d '{"user":"dan","operation":"add_message","subject":"pets","message":"cats are grumpy"}'

# GET all messages
curl -s "$API_BASE_URL/messages"

# GET messages by user
curl -s "$API_BASE_URL/messages/dan"
```
Data lives only in Lambda memory, so it disappears on cold starts and when the stack is destroyed.

### Destroy the stack (end of hiring cycle)
```sh
aws cloudformation delete-stack \
  --stack-name ios-homework-message-service \
  --region us-east-2
aws cloudformation wait stack-delete-complete \
  --stack-name ios-homework-message-service \
  --region us-east-2
```
After deleting, replace the `API_BASE_URL` at the top of this README with the placeholder so no stale hostname is left behind.
