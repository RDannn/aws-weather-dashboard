# **NBA DATALAKE** 🏀

# **Overview**

We will be creating an Amazon S3 bucket that will store raw and processed data that uploads in JSON format samples of NBA data/statistics. 
We will also be using the AWS Glue database service for querying this data in an external data table.
Finally the Amazon Athena service will query this data that is stored in the S3 bucket. 

# **Prerequisites**

You will need the SportsData.io API Key from: https://SportsData.io. 🏀

<img width="1437" alt="sportsio" src="https://github.com/user-attachments/assets/8e31ef00-a4bb-4b99-b7a1-f24e222bc916" />



Sign up for a free account and head over to the API resources section to select "NBA."
Once registered, you can find your API key in the "NBA" section under "Query String Parameters."

<img width="1409" alt="apikey" src="https://github.com/user-attachments/assets/7c41fbe0-f02b-4420-ace3-c93002882b37" />


IAM Role/Permissions: Make sure the IAM user or role running this script has these permissions:

S3: s3:CreateBucket, s3:PutObject, s3:DeleteBucket, s3:ListBucket

Glue: glue:CreateDatabase, glue:CreateTable, glue:DeleteDatabase, glue:DeleteTable

Athena: athena:StartQueryExecution, athena:GetQueryResults



# **Steps**


Ever wanted a quick and easy way to check out and save your favorite players' stats or have access to NBA analytics anytime you need? Look no further! Today, we’re diving into building a data lake to handle sports analytics like a pro. This project will use Amazon S3, AWS Glue, and Amazon Athena to create a powerful solution for storing, processing, and querying NBA data. Let’s break it down step by step!

# **What We’ll Be Using**

Amazon S3: Think of S3 as a storage powerhouse. It’s scalable, durable, and ready to hold all your raw and processed data in formats like JSON, CSV, and Parquet. For this project, we’re sticking with JSON to store our NBA data.
AWS Glue: Glue does exactly what it sounds like—it holds everything together. This serverless ETL (Extract, Transform, Load) service will catalog our S3 data and define schemas so we can easily query it.
Amazon Athena: A serverless, interactive query service that lets us run SQL queries directly on our S3 data. No moving data around, no extra infrastructure—just simple and cost-effective analytics.

How It Works:

Store Raw Data in S3:

First, we’ll set up an S3 bucket to hold our unprocessed NBA data in JSON format. This will act as the foundation of our data lake.

Catalog with AWS Glue:

Glue will create a schema (metadata layer) for the raw data in S3. This makes the data queryable using Athena. Think of it as creating a map of your data’s structure so you can navigate it easily.

Query with Amazon Athena:

Finally, Athena will let us query the NBA player stats directly in S3 using SQL. We’ll configure an output location in S3 for query results, keeping everything streamlined and organized.

1.) Diving into the Code

<img width="1160" alt="py" src="https://github.com/user-attachments/assets/f60e8df6-889b-42d4-8d9c-32ea3ac3a20f" />


Imports and Setup
At the top of the script, you’ll see some key libraries we’re using:

Boto3: The AWS SDK for Python, which lets us interact with AWS services programmatically.
JSON: For formatting and handling our NBA data.
Time: Used to add delays so AWS resources like S3 buckets have time to propagate before moving to the next step.
Requests: For fetching data from the SportsData.io API.
Configuring AWS Resources
In the script, we define:

Region: We’re using us-east-1 for simplicity.
S3 Bucket Name: This must be unique. For example, you might use drr-nba-analytics-datalake-<yourname>.

<img width="1241" alt="inputbucketnmae" src="https://github.com/user-attachments/assets/16f5adb7-c82e-44f1-855f-c36da051eaed" />


Glue Database Name: This will be something like nba_data_lake, so it’s easy to track the project.
Athena Output Location: This is where query results will be stored in S3.
Creating AWS Clients
We’ll use Python to create clients for S3, Glue, and Athena. This replaces manual steps in the AWS Management Console, saving time and reducing errors.

Fetching Data
The script sends an HTTP GET request to the SportsData.io API, authenticates with your API key, and retrieves player stats in JSON format. If there are errors during this step, the script logs the issue for easy debugging.

Uploading Data to S3
The raw JSON data is uploaded to the S3 bucket. This forms the foundation of our data lake.

Creating Glue Tables
Glue defines the schema for the NBA data. This includes details like column names (e.g., Player ID, First Name), data types (string, integer), and delimiters (e.g., commas or semicolons).

Configuring Athena
Athena queries the data directly in S3. If an output location doesn’t exist, the script creates one automatically.

Main Function
The main function ties everything together. It ensures the S3 bucket exists, fetches NBA data, uploads it, creates a Glue table, and sets up Athena for querying. Delays are added between steps to ensure smooth execution.

Running the Script
Log in to AWS
Open the AWS Management Console, and in the search bar, click the arrow next to the bell icon to access CloudShell.

2.) Create the Python File

In CloudShell, type:

nano setup_nba_data_lake.py
This opens a blank file where you’ll paste the script.

<img width="1189" alt="nanosetupdl" src="https://github.com/user-attachments/assets/8b6c483b-1e2e-42e9-bc2e-c2a34a981324" />



Get the Code
Go to the Git repository for this project. Under the src folder, find setup_nba_data_lake.py. Click Copy Raw File to grab the entire script. Paste it into your favorite code editor (like VS Code or Sublime Text) for easy edits.

<img width="1413" alt="nanodatalake" src="https://github.com/user-attachments/assets/b97c82e9-4df1-47c3-8be5-a0c1f3c585cf" />



3.) Create .env file
In the CLI (Command Line Interface), type
nano .env
paste the following line of code into your file, ensure you swap out with your API key
SPORTS_DATA_API_KEY=your_sportsdata_api_key
NBA_ENDPOINT=https://api.sportsdata.io/v3/nba/scores/json/Players
Make sure to replace your_sportsdata_api_key with your actual API key before saving the file. Keep this key private. This ensures your code has everything it needs to connect to the NBA API!


<img width="1344" alt="nanoenv" src="https://github.com/user-attachments/assets/bc2ad07d-dcc4-4dc8-9c9f-06b76de5bac5" />

Bucket Name: Make the S3 bucket name unique, e.g., nba-datalake-<yourname>.

<img width="1410" alt="bucketname" src="https://github.com/user-attachments/assets/e1e8c66b-e85f-4407-a613-c99a63cf761f" />


Save the File
In CloudShell, save the script:

Press Ctrl + O to save
Press Ctrl X to exit
Hit Enter
Run the Script
Execute the script with:

python3 setup_nba_data_lake.py
The script will create the S3 bucket, fetch NBA data, catalog it with Glue, and set up Athena for querying.

<img width="1130" alt="successpy" src="https://github.com/user-attachments/assets/1ac316d8-b743-49f7-bc0c-de240a6604b8" />


Next Steps
Once the data lake is set up, head over to the AWS Management Console to explore your S3 bucket, Glue catalog, and Athena queries. You’ll have a fully functional analytics pipeline ready to go! 
You will see the players stats in JSON format in the S3 bucket under "raw-data". Click this. Then click "nba_player_data.jsonl" Cick Download to view this information. 
You can view this JSON information in VSCode for instance. A ton of information 
concerning the player stats! 

<img width="1412" alt="rawdata" src="https://github.com/user-attachments/assets/12f35d71-334d-46d1-820f-678315c4a13d" />


<img width="1418" alt="playerinfo" src="https://github.com/user-attachments/assets/21186c01-933d-492c-aa87-e56249446053" />


<img width="1439" alt="downloadplayerinfo" src="https://github.com/user-attachments/assets/cf013f4b-d879-4e2a-99c6-aedb120e8ed3" />


<img width="1252" alt="jsonplayerformat" src="https://github.com/user-attachments/assets/6419cf34-e9d3-478b-aa70-43f0c7e7d4f0" />



<img width="1424" alt="jspnplayer" src="https://github.com/user-attachments/assets/7e8251a2-699d-4f36-949d-22a7be59acb6" />



One last step! To delete all of your resources, input in Cloud Shell, "nano delete.py." Grab the 
"delete_aws_resources" code and input this in the nano file. Ctrl O to save, Ctrl X to exit.
Run the code, and your resources will be deleted. Saves money! 


<img width="983" alt="nanodeletepy" src="https://github.com/user-attachments/assets/a466cee4-157e-49d2-bc93-c749908c7a9c" />



<img width="1395" alt="deletepy" src="https://github.com/user-attachments/assets/7ebd64a6-b14a-4185-a218-31213a03ae9a" />


And that's a wrap! 🎉 You’ve just built an end-to-end data lake for NBA analytics using Amazon S3, AWS Glue, and Athena. From setting up the raw data storage to querying it like a pro, you've created a fully functional solution that can handle sports stats like a champ. 💪🏀

This project not only shows off your AWS skills but also highlights your ability to design scalable, serverless architectures. Remember, every step you take in building projects like this brings you closer to mastering cloud computing and landing your dream role.

Stay curious, keep experimenting, and let your passion for cloud solutions shine! 🚀


