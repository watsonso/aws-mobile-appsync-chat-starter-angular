Introduction
============

This is a Starter Angular progressive web application (PWA) that takes advantage of AWS AppSync offline and real-time capabilities to allow users to communicate via chat. A script creates a GraphQL Schema, a related AppSync API, an Amazon Cognito User Pool for AuthN/Z as well as provisions Amazon DynamoDB resources, then connects them appropriately with AppSync Resolvers. The application demonstrates GraphQL Mutations, Queries and Subscriptions using AWS AppSync. You can use this for learning purposes or adapt either the application or the GraphQL Schema to meet your needs.

<p align="center">
  <img src="screenshots/chatql.png">
</p>

## Pre-Reqs

* Valid AWS Account
* [AWS CLI](http://docs.aws.amazon.com/cli/latest/userguide/installing.html)
* [NPM](https://www.npmjs.com/get-npm)
* [AWS Mobile CLI](https://github.com/aws/awsmobile-cli) - ```npm install -g awsmobile-cli```
* [Angular CLI](https://github.com/angular/angular-cli) - ```npm install -g angular-cli```

## Features

- PWA: A progressive web application takes advantage of the latest technologies such as Service Workers to combine the best of web and mobile apps. Think of it as a website built using web technologies but that acts and feels like an app in a mobile device. 

- GraphQL Mutations (src/app/graphql/mutations)
  - Create messages
  - Create conversations and link them to the user
  - Create users

- GraphQL Queries (src/app/graphql/queries)
  - Get all users
  - Get all messages in a conversation
  - Get all user related conversations by Id

- GraphQL Subscriptions (src/app/graphql/subscriptions)
  - Real time updates for new messages in conversations

- Auth
  - The app uses JWT Tokens from Cognito User Pools as the AuthN/Z mechanism

Setup
======

1. Clone this repository

2. Click the button to create a Mobile Hub project that will deploy User Sign-In, Analytics and Hosting resources, name the project as "ChatQL" and follow the instructions in the console:


<p align="center">
   <a target="_blank" href="https://console.aws.amazon.com/mobilehub/home#/starterkit/?config=https://github.com/awsed/chatql/blob/master/backend/mobile-hub-project.zip">
   <span>
       <img height="100%" src="https://s3.amazonaws.com/deploytomh/button-deploy-aws-mh.png"/>
   </span>
   </a>
</p>

3. Go to the project and click on ADD NEW APP then select WEB. Follow the instructions.

4. Execute the "awsmobile init xxxxxxxxxxxxxx" command the root folder and provide the following details:

* Source Directory: src
* Distribution Directory: dist
* Build Command: ng build --prod

This will copy the "aws-exports.js" file to the "/src" folder and configure the resources accordingly.

(Alternatively, after the project is created in the Mobile Hub console download the "aws-exports.js" configuration file by clicking the Hosting and Streaming tile then download the file "aws-exports.js", then copy the file "aws-exports.js" to the "/src" folder)

4. Execute the shell script "setup.sh" from the folder "/backend". The script will create an IAM Service Role, AppSync API, Data Sources (DynamoDB Tables), Resolvers and a GraphQL Schema 

5. Go to the AWS AppSync Console and select the ChatQL API created on the previous step. From the homepage of your GraphQL API (or you can click on "ChatQL" in the left hand navigation), select WEB at the bottom to download your "AppSync.js" configuration file into your project's "/src" directory.

6. Now go to the root folder of the cloned repository and execute the following command to install all packages and test the application locally (if there are any errors, copy the file backend/mobile-hub-project.yml to the folder awsmobilejs/backend/):

```
awsmobile run
```

6. Access your ChatQL app on http://localhost:4200 and Sign Up/In at least 2 test users

Host your ChatQL app in CloudFront and add Pinpoint Analytics with Mobile Hub and AWS Amplify
=============================================================================================

1. Execute "awsmobile publish" from the root folder of the cloned repository (If there are any errors, copy the file backend/mobile-hub-project.yml to the folder awsmobilejs/backend/)

2. If you want to access the application from S3, while the application is building/publishing, go back to the Mobile Hub console and select HOSTING & STREAMING -> MANAGE FILES -> PROPERTIES -> STATIC WEB SITE HOSTING and add "index.html" under ERROR DOCUMENT

3. If you want to access the application from CloudFront, while the application is building/publishing, go back to the Mobile Hub console and select HOSTING & STREAMING -> EDIT YOUR CDN DISTRIBUTION -> ERROR PAGES -> CREATE CUSTOM ERROR RESPONSE. Use the following settings:

* HTTP Error Code: 403
* Minimun TTL: 0
* Customize Error Response: Yes
* Response Path: /index.html
* HTTP Response Code: 200

The last two steps are necessary because of the way Angular internal routing works. An authenticated request will be redirected to /chat that doesn't exist on S3, only internally in the Angular application.

4. Wait until the CloudFront settings are replicated (Status: Deployed)

5. Access your public ChatQL application using the S3 Website Endpoint URL or the CloudFront URL returned by the "awsmobile publish" command, share the link, sign up users and start sending messages and creating conversations with other users.

6. The "aws-exports.js" files also provides the Amazon Pinpoint project details to start collecting analytics data from the chat activity in the application. AWS Amplify is configured to send custom events to Pinpoint when a message is sent and when a conversation is created (/src/app/chat-input/chat-input.component.ts and /src/app/chat-user-list/chat-user-list.component.ts)

7. After a couple of minutes go to the Amazon Pinpoint console, select the project, go to ANALYTICS -> EVENTS and select the specific event ("Chat MSG Sent" or "New Conversation" Events) from the EVENT dropdown menu to see the data about the related custom event.

Clean Up
========

Execute the script "teardown.sh" under the "/backend" folder