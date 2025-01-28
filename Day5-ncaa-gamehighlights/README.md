![ncaagamehidrawio](https://github.com/user-attachments/assets/5a3f5c85-46a4-444d-a404-b6e8ae7b0918)


## **NCAA Game Highlights**


We’re back with another one! This time, we’re creating a NCAA Game Highlights Processor! These projects are coming at you fast, so let’s build! 🚀

Here’s the plan: we’ll find some game highlights, process them, and then view the game highlights/videos. For this project, we’ll use Docker for containers, S3 for storage, and AWS MediaConvert to process and enhance our game highlight videos. Let’s break down the basics of this easy-to-set-up project. Ready? Let’s go!

Docker, S3, and Elemental MediaConvert Overview 🎨👨🏾‍🎨🖌️
Docker has three main components: the Dockerfile, the Docker image, and the Docker container. Think of the Dockerfile as a recipe—it contains the instructions to build what we need. The Docker image is the result of following that recipe, and the Docker container is the running version of that image. Containers are lightweight, portable, and easy to scale across different systems.

Imagine Docker as a painter’s toolkit. The same brushes, paint, and tools are used for every project. Similarly, the Dockerfile (our recipe) defines the tools and setup we need, the Docker image is our completed setup, and the Docker container is the job-ready version we can take anywhere.

Amazon S3 is our durable storage solution. It’s where we’ll store raw data pulled from APIs. Think of it as the place where we keep leftover paint and tools from previous jobs. In S3, we can organize data into folders for easy access. It’s where external API data is retrieved, stored, and prepped for use.

AWS Elemental MediaConvert acts like a quality control expert. Just as a customer reviews and tweaks a paint job to ensure it meets expectations, MediaConvert processes our videos and images to deliver crisp, high-quality results. It lets us fine-tune resolutions, formats, and more to make sure everything looks amazing.

These are the building blocks for our project. Now, let’s dive into our first code files, fetch.py! 💻


## Code Breakdown: 💻

### fetch.py 💻

<img width="1434" alt="fetchpy" src="https://github.com/user-attachments/assets/ec335498-81a6-46fc-8b32-3c8404436663" />


Introduction

This script is designed to fetch basketball highlights from an external API and save them securely in an Amazon S3 bucket🪣. It integrates API processes/consumption, AWS services, and Python to automate data processing. Below is a breakdown of the key components and their roles in this workflow. This will fetch from our RAPID API external API NCAA game highlights. The collegiate level of American basketball to be exact! 🏀. This is within the free tier of the RAPID API external API we will be using. Enough explaining, lets dive in! 

Module Imports and Configuration

json: Converts Python dictionaries into JSON format for saving and handling structured data.

boto3: AWS SDK for Python, allows us to interact with AWS services like S3.

requests: A library to make HTTP requests, used here to communicate with an external API.

config: Contains all reusable variables, such as API credentials, AWS settings, and league-specific parameters.

This setup will ensure the script is easy to configure and scales well with changes.

Function Overview

1. fetch_highlights()
Purpose: Retrieves basketball highlights from a third-party API.

Workflow:

Step 1: Define the API parameters, such as the date, league, and limit of highlights.

Step 2: Add authentication headers using the RapidAPI key and host.

Step 3: Make an HTTP GET request to fetch data, ensuring timeouts prevent hanging connections.

Step 4: Parse the JSON response and return it.

Key Details:

Error Handling: Any issues during the request, such as timeouts or invalid responses, are gracefully managed with informative error messages.
Output: Returns a Python dictionary of highlights if successful, or None if an error occurs.
2. save_to_s3(data, file_name)
Purpose: Uploads the fetched highlights to an S3 bucket🪣.

Workflow:

Step 1: Use the boto3 client to interact with the S3 service.

Step 2: Check if the target bucket🪣 exists. If not:
Create the bucket🪣.
Adjust for specific constraints, like LocationConstraint in non-default AWS regions.

Step 3: Serialize the highlight data to JSON and save it to S3 under a predefined path (highlights/<file_name>.json).
Key Details:

Error Handling: Both bucket🪣 creation and data upload operations are wrapped in exception handling to ensure robustness.
Output: Confirms successful uploads with the full S3 path to the saved file.

3. process_highlights()
Purpose: Orchestrates the process of fetching and saving highlights.

Workflow:

Step 1: Start by fetching highlights using fetch_highlights().

Step 2: If the data retrieval is successful, call save_to_s3() to upload the highlights.

Step 3: Log progress at each step for transparency and troubleshooting.
This function acts as the script's control center, streamlining the overall workflow.

Running the Script

By including:

if __name__ == "__main__":
    process_highlights()
we ensure the script executes only when run directly, not when imported into another module. This is a best practice for reusable Python scripts.

Why This Matters

This script demonstrates:

Practical API Integration: Shows how to securely fetch and parse external data.
AWS Automation: Highlights key AWS features, such as S3 and regional configuration management.
Error Resilience: Builds fault tolerance into workflows, ensuring smooth operation under unexpected conditions.

So now we get the data saved in an S3 bucket🪣 but its just in JSOn format? We need to process this into 1 video format! Let's now checout, process_one_video.py.

### process_one_video.py💻



This script’s job is pretty straightforward:

It grabs a file (JSON) from S3 that contains video URLs.
It picks the first video URL, downloads the video from there, and uploads it back to S3 in another location.
Let’s break it down step by step:

Imports (The Tools We’ll Use)
json: This helps us work with JSON data—think of it like a structured way of handling data.

boto3: This is the Python library we use to talk to AWS services like S3.

requests: This is what we’ll use to download videos from the web by sending HTTP requests.

BytesIO: This lets us handle files (like videos) in memory without saving them to disk.

config.py: This is like a settings file where we keep important variables (example like S3 bucket🪣 names and file paths).

Config Variables
From config.py, we’re using:

S3_BUCKET_NAME: Where everything is stored in S3.

AWS_REGION: The region where the bucket🪣 lives.

INPUT_KEY: The path to the JSON file with video URLs.

OUTPUT_KEY: Where we’ll save the processed video.

What Does the process_one_video Function Do?
This function handles everything from retrieving the JSON file to uploading the processed video. Here's what happens:

Connect to S3:
It starts by creating an S3 client (a tool to communicate with S3) and setting the AWS region.
Fetch the JSON File:
The script grabs the JSON file from S3 (using the bucket🪣 name and input key) and reads its content. Think of this as downloading a to-do list.

Extract the Video URL:
It opens the JSON file, looks for the first video URL, and pulls it out. You might need to adjust this depending on how your JSON file is structured.

Download the Video:
Using requests, it sends an HTTP request to the video URL to fetch the video. The stream=True option ensures we don’t overload memory by downloading everything at once.

Prepare the Video for Upload:
It stores the video content in memory using BytesIO. This is like holding the video in a temporary spot without saving it on your computer.

Upload the Video to S3:
The script uploads the video to the specified bucket🪣 and path. It also sets the file type as "video/mp4" so AWS knows what kind of file it is.

Print Success or Errors:
If everything works, it prints the S3 path where the video was uploaded. If something goes wrong, it prints the error.

The Final Line:
This part:

if __name__ == "__main__":
    process_one_video()
This means just Run the function if you’re executing this file directly.

Summary:
Fetch/retrieve JSON from S3 to Get Video URL to Download Video to Upload Video to S3.
It’s all automated, and if something fails, it will let you know what went wrong.


### mediaconvert_process.py 💻

At the top, we import our library and set our constants. You will replace the placeholder bucket🪣 name with your unique bucket🪣 name. In the README file of this repository, you'll find the bash script needed to run the MediaConvert endpoint.

Within the create_job function, we initialize our MediaConvert client using Boto3 and specify the MediaConvert endpoint for the correct AWS region. This is why we need the URL listed in the configuration!

In the job settings, you’ll need to replace the account ID in the appropriate section. The job settings are quite long, so make sure to review the entire code. If the screenshots don’t show the full details, the complete code is in this repository.

The script assumes the IAM role for MediaConvert, ensuring it has the required permissions to interact with AWS services. The S3 input and output file paths are specified, pointing to the bucket for the video to process and where the processed video will be saved.

For the outputs, the video settings use the H.264 codec for video compression, with the bitrate controlling video quality. A higher bitrate results in better quality. The audio settings use the AAC codec, with bitrate and sample rate also affecting audio quality.

Toward the bottom of the code, additional settings include disabling hardware acceleration and setting the job priority to normal. This configuration enhances the video quality using AWS services.

When you run the create_job function, it sends the job settings to MediaConvert, creating the video processing job and printing details like the status and job ID. Error handling is also included at the bottom to catch issues during processing.

Finally, the script specifies the entry point so the create_job function runs when the script is executed directly. This wraps up our mediaconvert_process.py code!

Next code breakdown coming up!


### run_all.py 💻

This run_all.py script is the ultimate orchestrator—it brings together all the previous scripts we’ve worked on into a seamless pipeline! How cool is that? Code truly is powerful and fascinating! 😱🤯

Now, let’s break down the magic happening here:

Retry Logic
The script comes with a built-in safety net: retry logic. If any script fails—like if there’s a delay in creating or finding a resource—this script doesn’t just crashout and burn! 🚨 It retries the process before throwing in the towel. Talk about resilience! 💪

Adding Delays
We’ve accounted for potential slowness or timing hiccups by adding buffer time between scripts. This ensures resources have enough time to stabilize before moving to the next step. Think of it as the code taking a breather. 🧘‍♂️

Dependency Chain
Here’s the catch: these scripts depend on each other. None of them can function independently, they’re like puzzle pieces, each fitting perfectly into the next. If one fails, the whole pipeline is affected. That’s why we’ve wrapped everything in a neat retry and wait strategy to keep things smooth!


Lastly we will dive into our Dockerfile that brings everything together and executes.

### Dockerfile 💻 

This will build our entire container for us. First line, FROM PYTHON 3.9 slim. This tells Docker to use a lightweiht version of python 3.9 as the base for the container. This basically tells us lets set up a painting job pallete for our upcoming job. This our pyhton environment. We basically do not need to build this ourselves, it will be automatially built.

WORKDIR /app provides the working directory of the container. Its set /app as the current working directory inside the container. Any commands or files will be referenced to this relative directory. This is like your organized paint desk spread where you have all your tools in an organized way. 

COPY requirements.txt .copies a text file we create called requirements.txt  from our local machine to the containers working directory. It will the run PIP intsall, which is all of the 
python libraries we need in requirements.txt inside the container itself. Copying is like copying your same paint techniuqes for the paiint jobs, and PIP install is like painting all of the required coats of paint for each job. Maybe 2-3 coats are needed in the home project! 🏡🎨🖼️ requirements.txt has line of files/code, we need, PIP install make sure we get these things we need.

Next COPY fetch.py process_one_video.py mediaconvert_process.py run_all.py config.py . copies all of our python scripts into the container. This is placing your paint job instuctions on the counter before working and getting your paint on! 👨🏾‍🎨 
RUN apt-get update && apt-get install -y awscli installs the aws cli inside the container to allow us AWS access to services/resources like S3, media convert, etc.

This will be adding new paint brushes, and colors for a new specific home project inside a particlar room inside the home you are working on! 🖼️🏡 We need to talk with and work with AWS services right? We need to do so with the AWS CLI.

Our last line of code ENTRYPOINT ["python", "run_all.py"] sets the default command to run the pipeline entrypoint python run all.py. This will set run.py as the default command and python run all.py as the default command to execute when the container runs. The run all scripts runs all the other scripts and runs this automatic. This is our A.I. robotic painter automatically working on the painting jobs with all the tools need to get the job done! 🤖🦾🦿This Dockerfile takes all of our scripts and make sure that we build out the container with everything we need automatically. Everything we need to get our paint job done! 🎨🖼️

Ok so we have broken down the code in depth details! Let's now get into our prerequisites and build this project out! 👷🏾‍♂️🧱🏡

Prerequisites ⚒️

Before diving into the scripts, make sure you've got the following in place:

Create a RapidAPI Account: 🏀
You'll need a RapidAPI account to access highlight images and videos. For this example, we’re using NCAA (USA College Basketball) highlights, which are included for free in the basic plan. The Sports Highlights API is the endpoint we’ll be working with.

Verify Prerequisites are Installed: 🛠️
Check that the following tools are installed on your system:
Docker: Should be pre-installed in most regions. Run docker --version to verify.
AWS CloudShell: Comes with the AWS CLI pre-installed. Verify by running aws --version.
Python3: Make sure it's installed by running python3 --version.

Retrieve Your AWS Account ID ☁️🔑
To get your AWS Account ID, log in to the AWS Management Console. Click on your account name in the top-right corner, and you’ll see your Account ID. Copy and save this ID somewhere secure because you’ll need it later when updating the code in the labs.

Retrieve Access Keys and Secret Access Keys🔑
In the IAM dashboard, check under Users for your access keys. Click on your main user that has the credentials, go to Security Credentials, and scroll down to find the Access Key section. If you don’t have a secret access key, you’ll need to create one, as it can’t be retrieved later. Make sure you save it securely!🔑

Step 1: Creat IAM role/user👨🏾‍💻

We create the IAM role/user that has the proper permsissions to execute the processes we need fo our project. They have just enough access to do things 
they need to do. Do not need full administrator level access. Depending on your role for instance, at a job, you will need the proper permissions to excecute certain processes based on the permissions level of your role. Log into the AWS console, in the search box input IAM. Click on Roles, then Create role to create a new role. For the use case, Service or use case we will select S3. We will add this trust policy shortly. We need more to interact than just S3. Click next. Under  Add Permissions search for AmazonS3FullAccess, MediaConvertFullAccess and AmazonEC2ContainerRegistryFullAccess and click next. Under Role details enter "HighlightProcessorRole" as the name. This is specifically called and used in the script itself. Select Create Role. Find the role in the list and click on it .Under Trust relationships Edit the trust policy to this: Edit the Trust Policy and replace it with this: [View and Copy IAM Trust Policy](./iam-trust-policy.json)

Then click Update Policy. Let's now get into our Cloudshell Terminal for the nex steps! 

Step 2: Create S3 Bucket🪣

We will now need to create the S3 bucket🪣 and its contents. Open the Cloudshell Terminal. Input this code and ensure you make your bucket unique. 'aws s3api create-bucket --bucket rddrapids --region us-east-1'. To verify the bucket input command, 'aws s3 ls'. You should see your bucket listed. Let's continue now setting up the rest of this project! 

Step 3: Create Local Folder and Clone Repository🗂️

Ready to dive in? Let’s set up your local environment and get rolling with this project!

1️⃣ Create a New Folder
Fire up your terminal and create a local folder to house your project. For example: mkdir -p "Day5-ncaa-folderplaceholder". Now, move into the folder: cd "Day5-ncaa-folderplaceholder"

2️⃣ Clone Your GitHub Repository
Head to GitHub and create a repository if you haven’t already: [Create a New Repo](https://github.com/new).
Clone your repository to your local machine. For example, my repo is named DevOps-30-Day-Challenge: git clone https://github.com/username-placeholder/DevOps-30-Day-Challenge.git
After cloning, navigate into your project folder: cd DevOps-30-Day-Challenge

3️⃣ Verify Your Structure
Run: ls

Ensure you see the folders you’ve created and cloned, like Day5-ncaa-folderplaceholder. Now we’ll build the file/folder structure locally and push it to GitHub.

Your folder should look like this when you’re done:

 Day5-ncaa-gamehighlights
├── README.md
├── resources
│   ├── IAMPolicies
│   ├── ncaaprojectcleanup.sh
│   └── vpc_setup.sh
└── src
    ├── terraform/
    ├── .env
    ├── .gitignore
    ├── Dockerfile
    ├── config.py
    ├── fetch.py
    ├── mediaconvert_process.py
    ├── process_one_video.py
    ├── requirements.txt
    └── run_all.py


## Files and Their Purposes

### **Resources Folder**
- **[IAMPolicies](resources/IAMPolicies)**: Contains the IAM policies required for the project.
- **[ncaaprojectcleanup.sh](resources/ncaaprojectcleanup.sh)**: Script for cleaning up AWS resources used in the project.
- **[vpc_setup.sh](resources/vpc_setup.sh)**: Script for setting up the VPC for the NCAA game highlights.

### **Source Folder**
- **[.env](src/.env)**: Environment variables for the project.
- **[.gitignore](src/.gitignore)**: Git ignore file to prevent sensitive or unnecessary files from being tracked.
- **[Dockerfile](src/Dockerfile)**: Docker configuration for containerizing the application.
- **[config.py](src/config.py)**: Configuration file for the project.
- **[fetch.py](src/fetch.py)**: Script to fetch NCAA game highlights data.
- **[mediaconvert_process.py](src/mediaconvert_process.py)**: AWS Elemental MediaConvert processing script.
- **[process_one_video.py](src/process_one_video.py)**: Script to process a single NCAA game highlight video.
- **[requirements.txt](src/requirements.txt)**: Python dependencies for the project.
- **[run_all.py](src/run_all.py)**: Main script to run all components of the project.

---

## How to Run the Project

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/username-placeholder/DevOps-30-Day-Challenge.git
   cd DevOps-30-Day-Challenge/Day5-ncaa-folderplaceholder
Set Up the Environment:
You can copy all of the .env config.py, etc files from my Github repository. 
And copy to your remote Github files/folders. Commit changes concerning all folders, files, Dockerfule, etc. Once you do so remotely, go backk to 
your local machine and in the main directory input git fetch. From there pull all of your remote changes to your local machine. For instance, I input git pull origin main. This should pull all changes. To ensure your local machine is update to date locally, input git status.
This ensures your local environment is completely up-to-date with your remote repository.
🎉 You’re All Set! Ready to crush the next steps? Let’s keep the momentum going! 💪

Step 4: Configure the .env File 🗂️
Now it’s time to update the .env file with all the critical details our project needs. Open up your local repository and navigate to the src folder. Use the command:

nano .env
This will allow you to edit the .env file contents directly. Let’s customize it with your unique values for the placeholders below:

RAPIDAPI_KEY=your_rapidapi_key_here
AWS_ACCESS_KEY_ID=your_aws_access_key_id_here
AWS_SECRET_ACCESS_KEY=your_aws_secret_access_key_here
S3_BUCKET_NAME=your_S3_bucket_name_here
MEDIACONVERT_ENDPOINT=https://your_mediaconvert_endpoint_here.amazonaws.com
MEDIACONVERT_ROLE_ARN=arn:aws:iam::your_account_id:role/HighlightProcessorRole
How to Fill These In:

RAPIDAPI_KEY: Create an account on RapidAPI and search for "Sports Highlights." Subscribe to the API and grab your key from the "Subscribe to Test" section.

AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY: These keys are generated in your AWS IAM console. Make sure they’re secure.

S3_BUCKET_NAME: Use the name of the S3 bucket you created earlier. For me, this is rddrapids; for you, it’ll be something unique.

MEDIACONVERT_ENDPOINT: Run the following command to find your endpoint:
aws mediaconvert describe-endpoints
Copy the URL and paste it here.

MEDIACONVERT_ROLE_ARN: This is the ARN for the MediaConvert role you created earlier. Replace your_account_id with your actual AWS account ID.

Save and close the .env file by pressing CTRL+O to save and CTRL+X to exit. To keep your environment variables secure, lock down the permissions:

chmod 600 .env

Finally, ensure Docker is up and running because we’re about to build and run the container for this project.

Step 5: Build & Run the Docker Container Locally 🛠️
Here’s where the magic happens! 🚀 Let’s build and run our project using Docker.

In your CLI, build the Docker container:
docker build -t highlight-processor .
Watch as Docker processes your image into a container. If everything’s set up correctly, you’re almost there!
Run the container with your .env file:
docker run --env-file .env highlight-processor
This will kick off the entire process! Our application will:

Upload the video to the S3 bucket.
Create a MediaConvert job to process the video.
Verify the Results 🎉
Let’s check the output of all our hard work:

S3 Bucket Contents: Navigate to your bucket, and you’ll find these folders:
highlights: Download the basketball_highlights.json file to see all the game highlights (try viewing it in VS Code).

videos: Contains the uploaded first_video.mp4.

processed_videos: Holds the final converted video! 🎥
MediaConvert Job Details: In the AWS Management Console, search for MediaConvert and review the job we created for first_video.mp4.

View the Video: Download the processed video and watch it. Enjoy the sound, game highlights, and all the enhancements we implemented! Success! 🏀

### What We Learned 💡
Using Docker to containerize and manage workflows.
Leveraging AWS services like S3, MediaConvert, and IAM.
The importance of least privilege and secure practices in IAM.
Automating media processing and quality enhancement.
Future Enhancements 🚀
Here’s where we can take this project next:

Infrastructure as Code: Use Terraform to define the entire setup.
Scalability: Process multiple videos in parallel with MediaConvert.
Dynamic Date Ranges: Make the dates dynamic, such as "last 30 days" instead of hardcoded timestamps.




