![nba drawio](https://github.com/user-attachments/assets/3638be70-648d-4210-861e-489d07632bd5)




# **NBA Game Notification System Using AWS**

This project implements a serverless, event-driven architecture to send email notifications about NBA game updates. Using AWS Lambda, the solution fetches game data from the SportsData.io API, filters final game results, and formats them into human-readable summaries. Notifications are sent via Amazon SNS to subscribed email addresses. The workflow is automated using AWS EventBridge, which triggers the Lambda function on a recurring schedule. The project adheres to best practices by using environment variables for sensitive information like the API key and SNS topic ARN, ensuring security and flexibility.



1. Set Up AWS SNS Service
Navigate to the AWS Management Console and create an SNS service.
Create a Standard SNS Topic named gd-topic. Scroll down and click Create Topic.

<img width="1398" alt="sns" src="https://github.com/user-attachments/assets/854ac10a-cdad-419e-bf55-e8b71c975366" />



Create a subscription for the topic using your email:
Input the ARN of the topic.
Select Email as the protocol and provide your email address.
Confirm your subscription by checking your email and verifying the link sent by AWS.

<img width="1418" alt="emailsubscribe" src="https://github.com/user-attachments/assets/ac598af5-4ba6-47de-bc7e-2b383c2ca190" />



2. Create IAM Role and Policy for Lambda Function
Lambda functions require permissions to publish messages to the SNS topic. To achieve this, create a role and policy in the IAM Console.
Policy Creation:

Go to the Policies section in the IAM Console and click Create Policy.
Select SNS as the service.
Retrieve the gd_sns_policy.json file from the GitHub repository under the policies folder.
Copy the policy content and paste it into the JSON editor in the policy creation wizard.
Update the Resource field with the ARN of your SNS topic to allow the Lambda function to publish messages to it.
Name the policy gd_sns_policy and click Create Policy.

<img width="1422" alt="policy" src="https://github.com/user-attachments/assets/06a6218f-b2cf-46ce-af11-35882ea68836" />


Role Creation:

Go to the Roles section in the IAM Console and click Create Role.
Select Lambda as the trusted entity type.
Attach the newly created gd_sns_policy and the AWS-managed policy AWSLambdaBasicExecutionRole for CloudWatch logging.
Name the role gd_lambda_role and click Create Role.

<img width="1418" alt="iam role" src="https://github.com/user-attachments/assets/05bc0bb8-6f60-47be-b5b7-2cc0ad5056ba" />



<img width="1404" alt="permission" src="https://github.com/user-attachments/assets/05d8aee0-c6e3-4631-98f2-2611c7d9ea45" />



3. Create and Configure Lambda Function
Navigate to the AWS Lambda Console and create a new function.
Choose Author from Scratch.
Name the function gd_notifications.
Select Python 3.13 as the runtime.
Change the default execution role to Use an existing role and select gd_lambda_role.
Click Create Function.
Add Lambda Code:

Remove the default code in the Lambda function editor.
Retrieve the code from the src folder in the GitHub repository (gd_notifications.py).
Copy the code and paste it into the Lambda editor.
Click Deploy to save the changes.


<img width="1406" alt="lambdacode" src="https://github.com/user-attachments/assets/d687c0cd-ca28-4cee-a036-0697c81f4450" />



Environment Variables:

To avoid hardcoding sensitive data such as the API key and SNS topic ARN, configure environment variables:
Go to the Configuration tab and select Environment Variables.
Add two variables:
NBA_API_KEY (value: your API key from sportsdata.io).
SNS_TOPIC_ARN (value: the ARN of the SNS topic).
Click Save.




4. Understanding the Lambda Code
The code uses the following libraries:
OS: Accesses environment variables.
JSON: Parses API responses into a readable format.
urllib: Makes API requests.
Boto3: Interacts with AWS services like SNS.
datetime: Formats the current date and time.
Key Functions:

Lambda Handler: Entry point of the Lambda function, which retrieves environment variables, fetches API data, and sends notifications to SNS.
Format Game Data: Converts API responses into a human-readable format, extracting details like game status, scores, teams, and more.
Publish to SNS: Sends formatted game updates to the SNS topic, notifying subscribed users via email.

5. Test the Lambda Function
In the Lambda Console, go to the Test tab.
Create a test event named TEST1 and click Test.
Confirm the Lambda execution by checking your email for the SNS notification.


<img width="1388" alt="success" src="https://github.com/user-attachments/assets/4c257d8e-4504-4ab1-a8f8-bdcf3350477e" />




6. Schedule the Lambda Function with EventBridge
Go to the AWS EventBridge Console and click Create Rule.
Name the rule gd_rule and select Schedule.
Choose Recurring Schedule and define a cron expression for the schedule.
Example: 0-9-23/2,0-2/2 * * ? * runs the function every minute between 9:00 AM and 2 AM, next day.
Disable the Flexible Time Window option and proceed.
For the target, select AWS Lambda and choose the gd_notifications function.
Ensure Create a new role for this schedule is selected, then click Create Rule.

<img width="876" alt="crone" src="https://github.com/user-attachments/assets/34cbfb4e-b7ea-4fe5-b976-92ee849ad087" />



<img width="1405" alt="schedule" src="https://github.com/user-attachments/assets/85bed534-3879-404e-8c03-2fb4a2194423" />



<img width="1388" alt="ebrule" src="https://github.com/user-attachments/assets/df6b43e6-7d46-4cbd-9482-fa333a41a1c6" />



<img width="1372" alt="ebrule2" src="https://github.com/user-attachments/assets/d099faa8-89e1-43e1-bab9-9d2509da440a" />


7. Validate the Event-Driven Architecture
Test the solution by waiting for the scheduled time or modifying the cron expression to trigger the function sooner.
Check your email for game updates sent via SNS.

<img width="1439" alt="emailgameupdates" src="https://github.com/user-attachments/assets/aab850a7-1860-4b90-8668-be178991bdcf" />



This architecture demonstrates the use of AWS Lambda, SNS, and EventBridge in a serverless and event-driven application, effectively integrating external APIs with AWS services.
