# Sports API Management System


Welcome to an exciting new project! Today, we’re building our very own endpoint – a custom URL where users can search and retrieve updated NFL game schedules. With the Playoff season underway, the stakes are high as teams battle for a spot in the Super Bowl! 🏈

In this project, we’ll leverage several AWS services to bring our API to life, including containers, load balancers, and API Gateway. Let's dive in!

### What is a Container?

You’re probably wondering, what exactly is a container? A container is a lightweight, portable unit that bundles everything an application needs to run. This includes application code, libraries, environment variables, configuration files, and runtime libraries. The beauty of containers is that they allow the application to run consistently across different environments – no more compatibility issues! For example, if we run a Python app in a container, it will work even if the host machine doesn’t have Python installed.

Containers are smaller, faster, and more efficient than traditional virtual machines. Instead of running an entire operating system, containers run as isolated environments within a host operating system, such as Linux or Windows. Think of containers as mini virtual machines that hold everything needed to run the application – the infrastructure (CPU, memory, storage), the operating system, and the container engine that manages it all.

In simple terms, a container is like a fully-built house that people can move into. The application runs in this isolated environment, ready to go. In this project, we’re using a container to host a Python Flask app. Cool, right?

### Overview of the Project

Our container will hold the application code, libraries, dependencies, and everything else necessary to run the Python Flask app. This self-contained environment makes the app lightweight and portable – you can run it anywhere! We’ll be using AWS ECS (Elastic Container Service) to manage our container in the cloud, which simplifies the process of handling containers at scale.

The Flask app will request data from a third-party API that provides NFL game schedules. We don’t need to worry about the backend – the external API does all the heavy lifting. For those unfamiliar with APIs: an API (Application Programming Interface) is a set of rules that allows different software applications to communicate with each other. In our case, the API will send NFL game data in JSON format, which we’ll then reformat into a user-friendly format inside the container.

High Availability and Redundancy:

Now, you might be wondering: What if one container fails? To ensure high availability and redundancy, we’ll use two containers. If one container goes down, the other can step in to serve the request. This setup helps us maintain reliability, even if something goes wrong.

Handling Traffic with Load Balancers:

As our service becomes more popular, we’ll likely experience increased traffic. What happens when thousands (or more!) of users request NFL game schedules at the same time? This could overwhelm our system and cause containers to crash or slow down. So, how do we ensure our system can handle high traffic without crashing?

We use a load balancer! A load balancer distributes traffic evenly across multiple containers to prevent any one container from getting overloaded. For this project, we’ll use an AWS Application Load Balancer (ALB). The ALB will manage incoming requests, distributing them evenly between the two containers. If 1,000 requests come in, the load balancer will send 500 to each container. Simple, right?

Adding Security and Throttling with API Gateway:

To add an extra layer of security and prevent abuse, we’ll implement an API Gateway. The API Gateway will sit in front of the load balancer and act as the main access point for users. It allows us to secure, authorize, and throttle requests. For example, if someone tries to make too many requests at once, the API Gateway can limit their access, preventing our system from becoming overwhelmed.

The Flow of Data:

Here’s how the data flows in this system:

User Requests: Users will send requests to the API Gateway for NFL game schedule information.

API Gateway: The API Gateway will handle these requests, sending them to the Application Load Balancer.

Load Balancer: The ALB will distribute the requests evenly between our two containers.

Containers: The containers will use the Flask app to query the third-party API for NFL game data.

Reformatted Data: The Flask app will reformat the raw JSON data from the third-party API into a human-readable format.

Response: The data is sent back through the load balancer and API Gateway to the user.
W
hy Not Just Contact the API Directly?

You might wonder, why don’t users just query the third-party API directly? The answer is that the raw data from the third-party API isn’t organized in a user-friendly way. Our Flask app inside the container takes care of transforming the raw JSON into a clean, organized schedule that’s easy for users to read.

Let's now breakdown our code! 💻

## Code Breakdown

<img width="1065" alt="dockerfilee" src="https://github.com/user-attachments/assets/53829887-1719-4af2-8772-92401b06f3e1" />


### Dockerfile Breakdown:

Specify the Base Image:
FROM python:3.9-slim

#### Purpose: This line sets the base image for the container. The python:3.9-slim image is a lightweight version of Python 3.9. It is optimized to minimize the size of the container while providing the Python runtime.
Set the Working Directory:

#### WORKDIR /app

#### Purpose: This sets the working directory inside the container to /app. All subsequent instructions and file operations (e.g., COPY, RUN) will be executed relative to this directory. This structure helps keep your files organized inside the container.

#### Copy the Requirements File:
COPY requirements.txt requirements.txt

#### Purpose: This copies the requirements.txt file from your local machine into the container's working directory (/app). The requirements.txt file contains a list of Python dependencies your application needs to run.

#### Install Dependencies:
RUN pip install -r requirements.txt

#### Purpose: This command installs all the Python libraries specified in the requirements.txt file using pip, the Python package installer. This step ensures that the application has all the dependencies it needs.

#### Copy the Application Files:
COPY . .
#### Purpose: This copies all the files and directories from your current directory (on your local machine) into the container's working directory (/app). This ensures that your application code and any necessary files are available inside the container.

#### Expose the Application Port:
EXPOSE 8080

#### Purpose: This specifies that the container will listen for network requests on port 8080. While this doesn't publish the port to the host machine, it informs Docker and anyone using the container that the application runs on this port.

#### Define the Command to Run the Application:
CMD ["python", "app.py"]

#### Purpose: This sets the default command to execute when the container starts. In this case, it runs the app.py file using Python. The CMD instruction ensures your application launches automatically when the container is started.

#### Summary
This Dockerfile creates a lightweight containerized environment for a Python application. It ensures that dependencies are installed, the application code is included, and the app is ready to run on port 8080. By using python:3.9-slim as the base image, the container is both efficient and tailored for Python 3.9 applications.


### app.py file:

<img width="1081" alt="app" src="https://github.com/user-attachments/assets/e9795ded-c0fd-450d-b8f5-56c448137e75" />


#### Imports: 
from flask import Flask, jsonify import requests import os

#### Purpose: 
Import necessary libraries: Flask for creating the web server and handling HTTP routes.
requests for making HTTP calls to SerpAPI.
os for fetching the environment variable (SPORTS_API_KEY).

#### App Initialization:
app = Flask(__name__)
#### Purpose: 
Initializes the Flask app to handle routes.

#### Set API Details:
SERP_API_URL = "https://serpapi.com/search.json" SERP_API_KEY = os.getenv("SPORTS_API_KEY")

#### Purpose:
SERP_API_URL is the base URL for SerpAPI queries.
SERP_API_KEY is pulled from the environment to keep credentials secure.

#### Route Definition:
@app.route('/sports', methods=['GET'])

#### Purpose: Sets up the /sports endpoint to handle GET requests.
Fetch and Format NFL Schedule:
def get_nfl_schedule():

#### Purpose: Defines the function to fetch NFL schedules and return them as JSON.

#### Query SerpAPI:
params = {
    "engine": "google",
    "q": "nfl schedule",
    "api_key": SERP_API_KEY
}
response = requests.get(SERP_API_URL, params=params)
response.raise_for_status()
data = response.json()

#### Purpose:
Prepares query parameters (engine, q, and api_key).
Sends a GET request to SerpAPI and fetches the response as JSON.
Extract Games:
games = data.get("sports_results", {}).get("games", [])

#### Purpose: Retrieves the list of games under the sports_results key. Defaults to an empty list if no games are found.
Format Games:
formatted_games = []
for game in games:
    teams = game.get("teams", [])
    ...
    game_info = { ... }
    formatted_games.append(game_info)

#### Purpose:
Loops through the games list.
Extracts away_team, home_team, venue, date, and time.
Appends formatted game details to formatted_games.

#### Return Response:
return jsonify({"message": "NFL schedule fetched successfully.", "games": formatted_games}), 200
Purpose: Sends the formatted NFL schedule as a JSON response with an HTTP status of 200.

#### Error Handling:
except Exception as e:
    return jsonify({"message": "An error occurred.", "error": str(e)}), 500

#### Purpose: Catches and returns any errors that occur during execution.
Run the App:
if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080)

#### Purpose: Starts the Flask application, making it accessible on port 8080 for all network interfaces.

#### Summary
This code builds a simple Flask-based API endpoint to fetch and return NFL schedules using SerpAPI. It handles:

Secure API key management via environment variables.
External API integration with requests.
Error handling and response formatting for clean JSON outputs.

### requirements.txt file:

<img width="832" alt="requirements" src="https://github.com/user-attachments/assets/ec5b2983-2feb-4e4c-af24-bab9dde38a3e" />


#### Code Breakdown
Flask==2.2.5

#### Purpose: This specifies the version of the Flask framework being used.
Flask is essential for creating and running the web server for your application.
Version 2.2.5 ensures compatibility with other dependencies and the project code.
requests==2.31.0

#### Purpose: This sets the version of the requests library.
Used for making HTTP requests to external APIs, like SerpAPI in the app.
Version 2.31.0 ensures the latest bug fixes and features for handling HTTP requests.

#### Summary: These two dependencies (Flask and requests) are critical for the app:

Flask runs the web server and handles routing.
Requests fetches data from external APIs (e.g., NFL schedule from SerpAPI).
This is your requirements.txt file, which ensures that your environment installs the exact versions of these libraries needed for consistent functionality.

### What’s Next?

In the next steps, we’ll explore how we can package everything in Docker and deploy it using ECS with Fargate, AWS’s serverless compute service for containers. This will allow us to run the containers without worrying about managing the underlying infrastructure. We’ll also see how the Application Load Balancer distributes traffic, and how the API Gateway adds security, throttling, and monitoring to our system.

Now, let’s dive into the detailed steps for setting up this API from scratch!

## Step-by-Step: Containerizing and Deploying a Sports API with ECS and Fargate

Step 1: Create Local Folder and Clone Repository

Open your terminal and create a new folder:
mkdir -p "Day4-containerized-sports-api" is my folder for instance
Change the directory to the folder:
cd Day4-containerized-sports-api
Clone the GitHub repository containing your project. You can create a new repository on GitHub:
git clone https://github.com/RDannn/DevOps-30-Day-Challenge.git for instance, my repository name
After cloning, navigate to the project folder:
cd DevOps-30-Day-Challenge for an example.


Step 2: Create ECR Repository

On your CLI input: aws ecr create-repository --repository-name sports-api --region us-east-1 to create a new Elastic Container Registry (ECR) repository

<img width="1432" alt="ecrreposcli" src="https://github.com/user-attachments/assets/acd4c8b9-7e08-4f13-8004-e043a3a78f8a" />


Go to the AWS console and search for "ECR" to see your new repository listed under Elastic Container Registry.

<img width="1437" alt="ecr" src="https://github.com/user-attachments/assets/564eab31-dbb6-4c78-96ac-524fc62be008" />


Step 3: Authenticate Docker to ECR

Retrieve a temporary authentication token for Docker to log into ECR:
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com
Replace <AWS_ACCOUNT_ID> with your AWS account ID (you can find it in the AWS console).
Once logged in successfully, you should see Login Succeeded.

Step 4: Build Docker Image

Make sure Docker is running and in the correct project directory.
Run the following command to build your Docker image:
docker build --platform linux/amd64 -t sports-api .
After the build completes, verify the image by listing Docker images:
docker images

Step 5: Tag and Push Docker Image to ECR

Tag your Docker image to associate it with the ECR repository:
docker tag sports-api:latest <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/sports-api:sports-api-latest

<img width="1439" alt="dockertag" src="https://github.com/user-attachments/assets/70289928-27a3-4c50-ae2d-42274d21eab2" />


Push the image to the ECR repository:
docker push <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/sports-api:sports-api-latest

<img width="1413" alt="pushcli" src="https://github.com/user-attachments/assets/f8193b36-f70e-4f89-a85b-b76f32462fdb" />


If you encounter an authorization token error, like I did! smh, reauthenticate by running the following:
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com

<img width="1434" alt="authenticaterror" src="https://github.com/user-attachments/assets/b798319f-75b7-4c7f-a618-af65542be13c" />


After successfully pushing, verify the image in the AWS console under your ECR repository.

<img width="1440" alt="ecrimage" src="https://github.com/user-attachments/assets/21aecd7e-9f78-486a-8af4-97e94a973eca" />


Step 6: Set Up ECS Cluster with Fargate

Open the AWS console and navigate to ECS (Elastic Container Service).
Click Clusters, then click Create Cluster.
Name the cluster sports-api-cluster and leave all default settings. Click Create.

Step 7: Create ECS Task Definition

Navigate to Task Definitions in the ECS console and click Create new Task Definition.
Select Fargate as the launch type, then click Next Step.
Name the Task Definition family sports-api-task, and under Container Definitions, add a new container:
Container name: sports-api-container
Image URI: Paste the ECR image URI from your repository.

<img width="1421" alt="ecruri" src="https://github.com/user-attachments/assets/1bcdca58-278a-4b8a-9f64-555246928d8a" />


Container port: 8080 (since your app.py exposes this port).

<img width="1403" alt="task8080" src="https://github.com/user-attachments/assets/84dbcd88-8ca3-43e3-ad9d-b10d8dafc593" />


Environment variables:
Key: SPORTS_API_KEY
Value: Your API key from SerpAPI (Sign up for SerpAPI at serpapi.com for your key).

<img width="1418" alt="serpapi" src="https://github.com/user-attachments/assets/eb720df6-2992-428c-a5a1-2299fefdeaac" />

<img width="1438" alt="serpapikey" src="https://github.com/user-attachments/assets/dd93902c-b750-4caa-852f-41d1e9ab9cb6" />



Click Create to complete the task definition.

Step 8: Create ECS Service

Go back to the sports-api-cluster and click Create under Service.

<img width="1356" alt="sportservice" src="https://github.com/user-attachments/assets/8b2071e8-a29a-47b2-8490-53820dcab563" />


Choose Fargate as the launch type, and select the sports-api-task definition.
Set the Service name to sports-api-service.

<img width="1438" alt="taskdef" src="https://github.com/user-attachments/assets/e4af7509-0d46-4178-bc12-4578a1e2dda5" />


Set Desired tasks to 2 (for two containers to be deployed for high availability).

<img width="1437" alt="sportsapitask" src="https://github.com/user-attachments/assets/fd367dff-6ee1-46df-aff6-f8d263964c68" />


Under Networking, select your VPC and subnets, ensuring the security group allows HTTP traffic on port 8080. Create a new security group if needed.
For Load balancing, select Application Load Balancer and name it sports-api-alb.
Click Create to set up the ECS service with load balancing. 

Step 9: Test Application Load Balancer

Once the load balancer is provisioned, go to EC2 in the AWS console.
Under Load Balancing, find your sports-api-alb and copy the DNS name.
Test the DNS name in your browser. You should receive a response from the sports-api endpoint with NFL game schedules if everything is working correctly.


Troubleshoot:

If any errors occur, check the CloudWatch logs for detailed debugging. Ensure that your ECS service is running and check the security group settings for correct inbound and outbound traffic rules.

Last Step! 🏈 Configure API Gateway
Alright, it's time to expose our Sports API using API Gateway! This step is all about creating the interface that connects users to your app. Let's make it happen! 🚀

Step 1: Set Up API Gateway

Head over to the AWS Management Console and search for API Gateway in the search bar.
Under REST API, select Build.

Step 2: Create Your API

For the API name, enter: Sports API.
Click Create API.

Step 3: Add the Resource

Time to create your endpoint! Name the resource /sports. This is the actual API path.
Click Create Resource.

Step 4: Define the Method

Now, let's add functionality to our resource. Click Create Method.
For Method Type, select GET.
This request will use HTTP Proxy Integration. Enter GET as the method and provide the Endpoint URL.

<img width="1418" alt="httproxy" src="https://github.com/user-attachments/assets/b49cfde7-7fa4-477e-8a31-2ad4ef4c9d2b" />


Endpoint URL Setup:

Go to the EC2 Dashboard. Under Load Balancers, locate your Application Load Balancer (ALB).
Copy the DNS Name of the ALB. Your endpoint URL should look something like this:
http://sports-api-alb-414429727.us-east-1.elb.amazonaws.com/sports.

<img width="1421" alt="dns" src="https://github.com/user-attachments/assets/280474cf-74f2-49cc-8e10-d48ab27f7224" />


Don’t forget to include /sports at the end—this is critical because it points to the /sports route in your app.py.
Scroll down and click Create Method.

Step 5: Deploy Your API

Click Deploy API.
For Deployment Stage, create a new stage and name it dev.
Click Deploy.

<img width="1236" alt="deployapi" src="https://github.com/user-attachments/assets/b6bf750f-d238-4e1d-ae6d-ac958c0767a1" />

Step 6: Test Your API

Copy the Invoke URL from your API stage. It will look like this:
https://<api-id>.execute-api.us-east-1.amazonaws.com/dev/sports.

<img width="1418" alt="invokeurl" src="https://github.com/user-attachments/assets/052a2ccc-76b1-4b06-89e6-a94d58e3cab6" />


Paste this URL into your browser, ensuring you add /sports at the end.

<img width="674" alt="browseraddress" src="https://github.com/user-attachments/assets/4f41008a-4377-4442-a03a-d93beaece64d" />

🎉 Boom! The NFL schedule is live! You’ve built a fully functional API that provides real-time data. How cool is that? 🏈

<img width="1343" alt="gamsched" src="https://github.com/user-attachments/assets/e41eb44a-79fe-4adf-b11f-709c81fa6efc" />


Clean-Up Checklist
Before wrapping up, let’s delete the resources we created to avoid unnecessary costs:

Delete API Gateway: Go to the API Gateway console and delete the Sports API.
Remove the ALB: Navigate to the EC2 dashboard, select your Load Balancer, and delete it.
ECS and ECR:
Update your ECS service by setting the Desired Tasks to 0.
Delete the ECS service, cluster, and any remaining tasks.
Remove your ECR repository.
Reflect and Celebrate
You just architected an API from scratch that connects an application load balancer to an endpoint delivering NFL game schedules! 🏈

This is the same foundation used in real-world, scalable projects. The skills you applied here—API Gateway, ECS, ECR, and ALB—are the backbone of cloud-based applications.

Now, let’s keep building and pushing boundaries. Sky’s the limit! 🚀

