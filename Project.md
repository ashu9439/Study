This Spring Integration configuration outlines a multi-step process for handling messages related to vehicle inventory. Here's a breakdown of the major steps involved:

1. **Business Validations:** Messages undergo initial basic BOD validation before proceeding to business validations.
2. **Perform Business Validations:** Business rules validation is executed using a service activator.
3. **Routing Based on Validation:** Messages are routed based on the outcome of the business validation (true/false).
4. **Routing to Vehicle Details Channels:** Messages are further routed based on specific criteria (e.g., New or Used vehicles).
5. **Transformation and Processing:** Messages go through a series of transformations, including potential transformation from STAR to EMF format and sending messages to AWS Kinesis Stream or other channels.
6. **Header Enrichment:** Headers are enriched with message status information.
7. **Invoke Vehicle Detail Dealer API:** Invocation of external services to fetch vehicle details and dealer configuration data.
8. **Route for Further Processing:** Routing messages based on dealer-specific criteria.
9. **RTI Validations:** Performing Real-Time Integration validations and handling validation outcomes.
10. **Invoke RTI Services:** Invoking RTI services based on validations.
11. **Response Handling:** Handling responses based on service invocation success/failure.
12. **Error Handling:** Managing error scenarios, such as queue unavailability or service invocation failure.
13. **Further Routing:** Routing based on specific vehicle details or destination criteria (e.g., Toyota or Lexus vehicles).
14. **Additional Service Invocations:** Invoking services for used vehicles specific to Toyota or Lexus.

Overall, this configuration sets up a comprehensive flow to validate, process, transform, and handle vehicle inventory-related messages while invoking external services and managing various outcomes at different stages of the process.




*****

Certainly! Here's an explanation of each function in the provided code:

### `refreshOnBoardingForSpecifiedDealersProcessor`
- **Purpose**:
  - Initiates the refreshing process for specified dealer codes.
  - Calls `refreshOnBoardingProcessor` for each dealer code received in the event.

### `refreshOnBoardingProcessor`
- **Purpose**:
  - Handles the complete refreshing process for onboarding information.
  - Retrieves data from various tables (OnBoarding, Gating Table, Dealer Master, DMS Master, Program Master).
  - Calls `refreshOnBoarding` to update onboarding information.
  - Updates OnBoarding table based on configuration settings and handles email alerts.

### Functions for scanning tables:
- `scanOnBoardingTable`, `scanGatingTable`, `scanDealerMasterTable`, `scanDMSMasterTable`, `scanProgramMasterTable`:
  - Purpose: Perform full scans or queries on respective DynamoDB tables based on the provided dealer code or without any filter.
  - Return respective table data as arrays of objects.

### `refreshOnBoarding`
- **Purpose**:
  - Updates OnBoarding table data based on information gathered from various tables.
  - Validates and processes data obtained from the Gating Table against Dealer Master, DMS Master, and Program Master data.
  - Determines updated, failed, and deleted records for the OnBoarding table.

These functions collectively manage the process of refreshing onboarding information for specified dealers or all dealers based on configuration settings. They ensure data integrity, perform validations, and update the OnBoarding table accordingly.



*****


Certainly! Here's an explanation of each function:

### `refreshDealerMasterProcessor`
- **Purpose**:
  - Orchestrates the refreshing process for the `DealerMaster` records.
  - Retrieves records from different sources (DB, DIS, Gating Table), merges, updates, and maintains the `DealerMaster` records.
- **Logic**:
  - Scans the database for existing `DealerMaster` records.
  - Retrieves DIS (Distributed Integration System) dealer records.
  - Retrieves Gating Table records (if required based on configuration).
  - Merges these records and updates the `DealerMaster` table accordingly.

### `scanDealerMaster`
- **Purpose**:
  - Performs a full scan of the `DealerMaster` table.
- **Logic**:
  - Utilizes the `DynamoDbService` to perform a full scan of the `DealerMaster` table.

### `getAllDISDealers`
- **Purpose**:
  - Retrieves all dealer records from DIS based on configured brand IDs.
- **Logic**:
  - Retrieves dealer records from DIS for each configured brand ID.
  - Concatenates these records into a single array.

### `updateDealerMaster`
- **Purpose**:
  - Updates the `DealerMaster` table based on the merged dealer records.
- **Logic**:
  - Handles deletion of specific dealer records.
  - Handles insertion/upsertion of updated dealer records.
  - Sends alert emails for deleted dealers.

### `mergeDISDealerMaster`
- **Purpose**:
  - Merges DIS, current `DealerMaster` records, and Gating Table records to determine updated and deleted dealers.
- **Logic**:
  - Combines DIS records with existing `DealerMaster` records.
  - Identifies dealers to be deleted or updated based on the merged data.
  - Handles the addition of new dealers from the Gating Table.

These functions collectively manage the refresh and synchronization of `DealerMaster` records by fetching data from various sources, updating records accordingly, and maintaining data integrity within the `DealerMaster` table.



*****

Sure, here's an explanation of each function:

### `construtGatingRule`
- **Purpose**:
  - Constructs or updates the gating rules for a given dealer association based on associated programs.
- **Parameters**:
  - `request`: An instance of `DealerAssociation` representing dealer association details.
  - `currentGatingRule`: An instance of `GatingTableResponse` representing the current gating rules.
- **Logic**:
  - Iterates over the associated programs of the given dealer.
  - Builds or updates the rules for the specific DSP (Dealer Service Provider) associated with each program.
  - Handles various scenarios like creating new DSP rules if not existing, updating existing rules, and merging authorization and business rules.

### `prepareHtmlTableWithData`
- **Purpose**:
  - Prepares an HTML table with the given JSON data.
- **Parameter**:
  - `emailJSONData`: An array of JSON objects to be represented in the table.
- **Logic**:
  - Generates HTML table markup with rows and columns based on the provided JSON data.

### `isArrayOfObject1PresentInObject2`
- **Purpose**:
  - Checks if an array of objects (`obj1`) is completely present in another array of objects (`obj2`).
- **Parameters**:
  - `obj1`: Array of objects to check presence.
  - `obj2`: Array of objects to check within.
- **Logic**:
  - Compares each object in `obj1` to all objects in `obj2` and checks if all objects in `obj1` are present in `obj2`.

### `isObjectsEqual`
- **Purpose**:
  - Compares two objects for equality.
- **Parameters**:
  - `obj1`: First object to compare.
  - `obj2`: Second object to compare.
- **Logic**:
  - Recursively compares each property in `obj1` with the corresponding property in `obj2`.
  - Handles different types of properties (strings, objects, arrays) and checks for equality.

These functions primarily deal with constructing/updating gating rules, preparing HTML tables, and performing object comparisons for equality or presence in other arrays/objects.



***



This code comprises TypeScript functions for refreshing gating rules based on different conditions.

### `refreshGatingRulesForRequest`
- **Function Signature**: 
  - This function is `async` and takes a single parameter `requestBody` of type `RequestGatingRequest`.
- **Function Logic**:
  - It initializes a `serviceResponse` array to store the results.
  - Processes each dealer asynchronously from the `requestBody.dealers` array:
    - Validates the incoming dealer association details using `validate`.
    - If the request is invalid, it logs the details and pushes an error response object to `serviceResponse`.
    - Otherwise, it creates a new `GatingTableResponse` object, populates it with the dealer details, and generates gating rules based on associated programs.
    - If no programs are associated with the dealer, it marks the request as invalid and adds an error message to `serviceResponse`.
  - Logs the `serviceResponse` after processing the requests.
  - Determines the HTTP status code based on the presence of error messages in the `serviceResponse`.
  - Updates `constants.CALL_BACK_RESPONSE` object with the determined status code and constructs a JSON response based on certain conditions:
    - If `canUpdateTable` is `true` and the status code is 200, it updates the DynamoDB table, constructs a response message, prepares HTML content with the updated data, and sends an alert mail.
    - Otherwise, it constructs a response body containing `serviceResponse`.

### `refreshAllGatingRulesForOnBoarding`
- **Function Signature**: 
  - This function is `async` and takes a single parameter `requestBody` of type `any`.
- **Function Logic**:
  - Performs a full scan on the `onBoardingTable` in DynamoDB to fetch all `OnBoarding` items.
  - Extracts `dealerCd` from each item and stores them in the `dealerCds` array.
  - Invokes `refreshGatingRulesForRequest` by passing an object containing `canUpdateTable` (or `false` if not provided) and the array of `dealerCds`.

These functions primarily process dealer association details, validate them, generate gating rules based on associated programs, and perform updates on the DynamoDB table if certain conditions are met. Additionally, it sends alert mails with relevant data when updating the table.


****

This code represents a TypeScript function called `generateGatingRulesForRequest` responsible for processing an array of `DealerAssociation` objects to generate gating rules based on certain conditions. Here's a breakdown:

- **Imports**: This section imports various modules, services, models, validators, and utility functions required for this file's operation.

- **Function Signature**: 
  - The function `generateGatingRulesForRequest` is asynchronous (`async`) and expects two parameters:
    - `request`: An array of `DealerAssociation` objects.
    - `overwritePrograms`: A boolean flag indicating whether to overwrite existing programs or not.

- **Function Logic**:
  - It initializes a `serviceResponse` array to store the results.
  - Validates the incoming `request` using the `validate` function from the gating-master-validator module.
  - Processes each item in the `request` array asynchronously using `Promise.all`:
    - If the request is invalid, it logs the details and pushes an error response object to `serviceResponse`.
    - Otherwise, it retrieves current gating rules for the dealer (`req.dealerCd`) and updates the gating rules based on the associated programs.
    - If there are warning messages, it creates a warning response object; otherwise, it pushes the updated gating rule object to `serviceResponse`.
  - Logs the `serviceResponse` after processing the requests.
  - Determines the HTTP status code based on the presence of error messages in the `serviceResponse`.
  - Constructs a response object with the determined status code, content type, and the `serviceResponse` as JSON in the body.

The final part of the code, currently commented out, seems to involve updating a DynamoDB table (`AppConfig.dspDealerMappingTable`) if certain conditions are met and returning a success or failure response accordingly.

This function mainly processes incoming `DealerAssociation` objects, generates gating rules based on their details, and constructs a response indicating the success or failure of the process.

*****


#Gating rules lamda

Certainly! The `serverless.yml` file defines several Lambda functions and their configurations:

1. **generateGatingRules:** This Lambda handles the generation of gating rules. It's triggered by an HTTP POST request to `/generateGatingRules/v1/`.

2. **refreshGatingRules:** This Lambda refreshes gating rules. It's triggered by an HTTP POST request to `/refreshGatingRules/v1`.

3. **refreshAllGatingRules:** This Lambda refreshes all gating rules from the OnBoarding table. It's triggered by an HTTP POST request to `/refreshAllGatingRules/v1`.

4. **refreshDealerMaster:** This Lambda refreshes the Dealer Master table from DIS (Dealer Information System). It runs on a schedule defined by a cron expression.

5. **refreshOnBoardingForAllDealers:** This Lambda refreshes the OnBoarding table for all dealers. It runs on a schedule defined by a cron expression.

6. **refreshOnBoardingForSpecificDealer:** This Lambda refreshes the OnBoarding table for specific dealers.

7. **getDealersForProgram:** This Lambda retrieves dealer codes for a given program code. It's triggered by an HTTP GET request to `/getDealersForProgram/v1/{programCd}`.

8. **getDealersForDsp:** This Lambda retrieves dealer code and enrolled program details for a given DSP (Dealer Service Provider) code. It's triggered by an HTTP GET request to `/getDealersForDsp/v1/{dspCd}`.

9. **getDealerDetails:** This Lambda retrieves details for a specific onboarding dealer. It's triggered by an HTTP GET request to `/getDealerDetails/v1/{dealerCd}`.

Each function has its specific purpose and trigger events defined, such as HTTP requests or scheduled executions, and they often have associated authorization configurations for handling access control.

****







*****





Certainly! This code seems to be an extensive module responsible for updating a database based on the contents of files stored in an AWS S3 bucket. Let's break it down:

1. **Imports:** The code imports necessary modules from various libraries (`aws-sdk`, `pg`, `lodash`) and internal configurations (`AppConfig`, `constants`) required for AWS services, database operations, and other utilities.

2. **Functions:**
    - `databaseUpdate`: This function takes an array of files and a pool configuration object as input and performs multiple operations:
        - Reads an indicator file from S3 to get MD5 details.
        - Checks the MD5 integrity against the files in the array.
        - Processes the files, creates SQL queries, and inserts records into the database based on certain conditions.
        - Deletes files from S3 based on specific conditions.
        - Returns a boolean indicating the success or failure of the database update.

    - Other functions within `databaseUpdate` are utility functions for specific database operations (`insertHDRTable`, `insertLineItemTable`, etc.), creating notifications for certain file types, and checking MD5 integrity (`checkmd5` and `readIndicatorFile`).

3. **Asynchronous Operations:**
    - The code extensively uses `async/await` with `Promise` objects to handle asynchronous tasks, especially while interacting with AWS S3, the PostgreSQL database, and performing other file-related operations.

4. **Error Handling:**
    - The code includes error handling using `try/catch` blocks and logs detailed error messages using the `log` utility from `@tmna-libraries/node-commons`.

5. **Database Operations:**
    - Several functions (`insertHDRTable`, `insertLineItemTable`, etc.) execute SQL queries on the PostgreSQL database using the `pg` library's `Pool` object.

6. **S3 Operations:**
    - Functions like `readIndicatorFile` and `deleteSource` interact with AWS S3 using the `AWS.S3` object to read indicator files and delete files from the S3 bucket.

7. **Business Logic:**
    - The code contains specific business logic to handle different file types, create notifications for different entities based on database records, and manage the execution flow based on certain conditions.

Overall, this module orchestrates file processing from an S3 bucket, MD5 validation, database updates, and associated notifications and error handling. It's quite comprehensive, covering various aspects of file management and database interactions within an AWS environment.





***


This code appears to be an AWS Lambda function written in TypeScript that serves to perform various operations based on an event trigger, likely associated with AWS S3 bucket actions. Here's a breakdown of the main parts:

1. **Imports:** The code imports various AWS SDK modules (`aws-sdk`) and other custom-defined modules for configuration, logging, database operations, etc.

2. **Functions:**
   - `notifyLaborOperations`: This is the main Lambda handler function. It's triggered by an event (presumably an S3 event) and performs several tasks:
     - Initializes configuration and credentials.
     - Lists objects in an S3 bucket's 'outgoing/' folder.
     - Updates a database based on the listed contents.
     - Deletes a specific file from the S3 bucket.
     - Sends alert emails based on the success or failure of the operations.
     - Stops an EC2 instance.
     - Returns appropriate responses based on success or failure.

   - `deleteIndicator`: A function responsible for deleting a specific object from an S3 bucket.

3. **Variable Declarations:**
   - `poolConfig`: Configuration object for a PostgreSQL connection.
   - `deleteparams`: Parameters object for deleting an object from an S3 bucket.
   - `s3`: Instance of the AWS S3 service.

4. **Asynchronous Operations:**
   - The code uses asynchronous operations (`async/await`) extensively, especially when interacting with AWS services (S3, EC2), handling database operations, and sending emails.
   - It uses `Promise` objects to handle asynchronous behavior and employs `try/catch` blocks to catch and handle errors.

5. **Error Handling and Notifications:**
   - The code handles errors by logging them and sending alert emails, then stopping the EC2 instance. It returns appropriate HTTP status codes (`200` for success, `500` for errors) along with descriptive messages.

6. **External Dependencies:**
   - It relies on external configurations, credentials, and constants imported from other files (`AppConfig`, `constants`, etc.).

Overall, this Lambda function seems to manage a workflow that involves processing files in an S3 bucket, updating a PostgreSQL database, and performing other associated tasks with error handling and notifications for success or failure.



*****

Certainly! This code snippet appears to be a function responsible for stopping an EC2 instance using the AWS SDK.

Here's a breakdown:

1. **Imports:** The code imports necessary modules like `AWS` from the AWS SDK, `log` from the '@tmna-libraries/node-commons', `AppConfig` from a configuration file, and `constants` from a utility file.

2. **`stopEc2Instance` function:**
    - This function is designed to stop an EC2 instance.
    - It begins by creating an instance of the `AWS.EC2` service.
    - It prepares the parameters required for the `stopInstances` method, specifying the instance ID(s) to stop (retrieved from `AppConfig.bastionId`), and sets `DryRun` to `false`.
    - Updates the AWS configuration with the specified region from `AppConfig`.
    - Calls the `stopInstances` method asynchronously, which takes the prepared parameters and a callback function.
    - Inside the callback function, it checks for errors or successful data returned from the `stopInstances` operation.
    - If an error occurs during the `stopInstances` call, it logs an error message using the `log.error` method.
    - If the operation is successful, it logs a success message with details about the stopped instances.

3. **Error Handling:**
    - The code employs a try/catch block to catch any synchronous errors that might occur during the setup or EC2 stopping process.
    - If any error is caught, it's logged using `log.error` and rethrown as an error for potential higher-level error handling.

This function encapsulates the logic to stop an EC2 instance using the AWS SDK, handles errors that might occur during the process, and logs the status of the operation accordingly.

***"
This function, `deleteSource`, is responsible for deleting a file from an S3 bucket using the AWS SDK's `deleteObject` method. Here's a breakdown:

1. **Imports:** The code imports `log` from `@tmna-libraries/node-commons` for logging purposes and `constants` from a utility file containing constant values.

2. **`deleteSource` function:**
    - Takes three parameters: `s3` (the AWS S3 service object), `bucketName` (name of the S3 bucket), and `fileName` (name of the file to delete).
    - Returns a `Promise<any>` to handle the asynchronous deletion operation.

3. **Deleting the Object:**
    - Prepares the `deleteparams` object with the required parameters - the `Bucket` name and the `Key` (the path to the file in the S3 bucket).
    - Uses `s3.deleteObject` to delete the specified file asynchronously.
    - Provides a callback function that handles the result of the deletion operation.
    - If an error occurs during deletion, it logs an error message using `log.error`, mentioning the file name and the error details, then resolves the Promise with `false` to indicate a failure.
    - If the deletion is successful, it logs a success message using `log.info` and resolves the Promise with `true` to indicate success.

4. **Promise Handling:**
    - Wraps the deletion operation inside a `Promise` to manage the asynchronous nature of the `deleteObject` function.
    - `await` is used to ensure the completion of the deletion operation before resolving or rejecting the `Promise`.

This function encapsulates the logic to delete a specific file (`fileName`) from a specified S3 bucket (`bucketName`) using the AWS SDK's `deleteObject` method, providing error logging and Promise-based handling for deletion success or failure.


****

This function, `sendAlertMail`, is responsible for sending alert emails using AWS SES (Simple Email Service). Here's a breakdown:

1. **Imports:** The code imports necessary modules like `AWS` from the AWS SDK, `log`, and `tmnaInit` from `@tmna-libraries/node-commons`, `Callback`, `Context` from 'aws-lambda', `AppConfig` from a configuration file, and `constants` from a utility file.

2. **`sendAlertMail` function:**
    - Takes three parameters: `status` (boolean indicating success or failure), `errorObj` (details about the error), and `context` (AWS Lambda context).
    - Uses `tmnaInit` to initialize the configuration using `AppConfig` and the AWS request ID from the Lambda context.
    - Creates an instance of the AWS SES service (`ses`).
    - Prepares `params` object required for the SES `sendEmail` method:
        - Defines the email recipient's address from `AppConfig.recipientMailAddress`.
        - Defines the sender's address from `AppConfig.senderMailAddress`.
        - Sets the email subject and body based on the `status` and `errorObj` provided.
    - Uses `ses.sendEmail` to send the email asynchronously.
    - Provides a callback function that handles the result of the email sending operation:
        - If there's an error during the email sending process, it logs an error message using `log.error`.
        - If the email is sent successfully, it logs a success message using `log.info`.

3. **SES Configuration:**
    - The function assumes the AWS credentials and configuration are already set up and available through the AWS SDK.

This function encapsulates the logic to send alert emails using AWS SES based on the provided status (success/failure) and error details. It uses predefined constants for email subjects and messages, and it logs the success or failure of the email sending process for monitoring and error tracking purposes.


