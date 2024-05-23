# Lovevery iOS Engineer Homework
The scope of this small project is to build a native iOS application that can POST to and GET messages from a service that has a REST interface.  
The application should be able to perform the following operations: 

### POST Messages
Post messages to the API using the URL:  
`https://abraxvasbh.execute-api.us-east-2.amazonaws.com/proto/messages`  

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
`https://abraxvasbh.execute-api.us-east-2.amazonaws.com/proto/messages`  

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
`https://abraxvasbh.execute-api.us-east-2.amazonaws.com/proto/messages/{user}`  
<sub>_Note that_ `{user}` _is the same user field in the_ `POST` _operation._</sub>  

An example of the URL and response could be: 
```
https://abraxvasbh.execute-api.us-east-2.amazonaws.com/proto/messages/dan

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