# 🚀 Day 3: Setting Up AWS CodeArtifact

## 🎯 Objective
In this project, we'll set up AWS CodeArtifact, a fully managed artifact repository service. We'll use it to store and manage our project's dependencies, ensuring secure and reliable access to Java packages for our web application.

This project is split into three parts:

**PART 1: Web App Setup** (a recap of Project #1)
- Launch an EC2 instance
- Set up your web app in the EC2 instance

**PART 2: Repository Setup** (a recap of Project #2)
- Connect your web app's code to a GitHub repository

**PART 3: CodeArtifact Setup**
- Create a CodeArtifact repository
- Set up EC2 instance permissions to access the CodeArtifact repository
- Verify the CodeArtifact repository stores your web app's packages

## 🔥 Steps to Follow

### 1️⃣ Launch an EC2 Instance

1. Log in to AWS as an IAM Admin User
2. Head to Amazon EC2
3. Switch your Region to the one closest to you (recommended regions listed below)
4. In your EC2 console, select Instances from the left hand navigation panel
5. Choose Launch instances
6. Set up your EC2 instance:
   - Name: `nextwork-devops-yourname` (replace yourname with your name)
   - AMI: Amazon Linux 2023 AMI
   - Instance type: t2.micro (Free tier eligible)
   - Key pair: Create a new key pair named `nextwork-keypair` or use an existing one
   - Store the .pem file in a folder called DevOps on your Desktop
7. Network settings:
   - Allow SSH traffic from: My IP
8. Choose Launch instance

> 💡 **Recommended AWS Regions** that support all services in the 7 Day DevOps Challenge:
> - us-east-1 (N.Virginia)
> - us-east-2 (Ohio)
> - us-west-2 (Oregon)
> - eu-west-1 (Ireland)
> - eu-west-2 (London)
> - eu-central-1 (Frankfurt)
> - eu-north-1 (Stockholm)
> - eu-south-1 (Milan)
> - eu-west-3 (Paris)
> - ap-southeast-1 (Singapore)
> - ap-southeast-2 (Sydney)
> - ap-northeast-1 (Tokyo)
> - ap-south-1 (Mumbai)

### 2️⃣ Set Up Your Web App Environment

1. Open VS Code on your local computer
2. Open a new terminal (Terminal > New Terminal)
3. Navigate to the DevOps folder: 
   - Mac/Linux: `cd ~/Desktop/DevOps`
   - Windows: `cd C:\Users\YourUserName\Desktop\DevOps`
4. Check that your .pem file is there:
   - Mac/Linux: `ls`
   - Windows: `dir`
5. Change permissions of your .pem file:
   - Mac/Linux: `chmod 400 nextwork-keypair.pem`
   - Windows:
     ```
     icacls "nextwork-keypair.pem" /reset
     icacls "nextwork-keypair.pem" /grant:r "USERNAME:R"
     icacls "nextwork-keypair.pem" /inheritance:r
     ```
     (replace "USERNAME" with your Windows username)
6. Find your EC2 instance's Public IPv4 DNS in the EC2 console
7. Connect to your EC2 instance:
   ```
   ssh -i nextwork-keypair.pem ec2-user@<your_public_ipv4_dns>
   ```
8. When prompted about host authenticity, type `yes`

### 3️⃣ Install Java and Maven

1. Install Apache Maven:
   ```sh
   wget https://archive.apache.org/dist/maven/maven-3/3.5.2/binaries/apache-maven-3.5.2-bin.tar.gz
   sudo tar -xzf apache-maven-3.5.2-bin.tar.gz -C /opt
   echo "export PATH=/opt/apache-maven-3.5.2/bin:$PATH" >> ~/.bashrc
   source ~/.bashrc
   ```

2. Install Java 8 (Amazon Corretto 8):
   ```sh
   sudo dnf install -y java-1.8.0-amazon-corretto-devel
   export JAVA_HOME=/usr/lib/jvm/java-1.8.0-amazon-corretto.x86_64
   export PATH=/usr/lib/jvm/java-1.8.0-amazon-corretto.x86_64/jre/bin/:$PATH
   ```

3. Verify installations:
   ```sh
   mvn -v
   java -version
   ```

   > 💡 If the Java version doesn't return openjdk version 1.8, run:
   > ```sh
   > sudo alternatives --config java
   > ```

### 4️⃣ Set Up VS Code Remote SSH

1. In VS Code, install the Remote - SSH extension
2. Click on the Remote SSH icon in the bottom left corner (or press Ctrl+Shift+P and type "Remote-SSH")
3. Select "Connect to Host..." > "+ Add New SSH Host..."
4. Enter the SSH command:
   ```
   ssh -i nextwork-keypair.pem ec2-user@<your_public_ipv4_dns>
   ```
5. Select the configuration file (typically /Users/username/.ssh/config)
6. Review the configuration details:
   - Host should match your EC2 instance's IPv4 DNS
   - IdentityFile should match nextwork-keypair.pem's location
   - User should say ec2-user
7. Click the Remote SSH icon again and select your EC2 instance to connect
8. Once connected, click "Open Folder" in VS Code's Explorer
9. Navigate to /home/ec2-user/ and select or create your project folder

### 5️⃣ Create or Clone Your Java Web App

**Option A: If you completed Projects #1 and #2**
1. Clone your existing GitHub repository:
   ```sh
   git clone <your_github_repo_url>
   ```
2. Navigate to the cloned directory:
   ```sh
   cd nextwork-web-project
   ```

**Option B: If you're starting fresh**
1. Generate a new Java web app using Maven:
   ```sh
   mvn archetype:generate \
     -DgroupId=com.nextwork.app \
     -DartifactId=nextwork-web-project \
     -DarchetypeArtifactId=maven-archetype-webapp \
     -DinteractiveMode=false
   ```
2. Navigate to the new project directory:
   ```sh
   cd nextwork-web-project
   ```
3. Initialize Git repository:
   ```sh
   git init
   ```
4. Create a new GitHub repository named "nextwork-web-project"
5. Connect your local repo to GitHub:
   ```sh
   git remote add origin <your_github_repo_url>
   ```
6. Stage, commit, and push your files:
   ```sh
   git add .
   git commit -m "Initial commit"
   git push -u origin master
   ```
   > 💡 You'll need a GitHub Personal Access Token (PAT) for authentication

### 6️⃣ Create a CodeArtifact Repository

1. Navigate to AWS CodeArtifact in the AWS Management Console
2. Click "Repositories" in the left menu, then "Create repository"
3. Repository configuration:
   - Repository name: `nextwork-devops-cicd`
   - Description: "This repository stores packages related to a Java web app created as a part of NextWork's CI/CD Pipeline series."
   - Select "maven-central-store" as a public upstream repository
4. Click "Next"
5. Domain selection:
   - Choose "This AWS account"
   - Domain name: `nextwork`
6. Click "Next", review your configuration, then click "Create repository"

### 7️⃣ Create IAM Policy for CodeArtifact Access

1. Navigate to the IAM console in AWS
2. Select "Policies" from the left menu
3. Click "Create policy"
4. Select the JSON tab and paste the following policy:
   ```json
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                   "codeartifact:GetAuthorizationToken",
                   "codeartifact:GetRepositoryEndpoint",
                   "codeartifact:ReadFromRepository"
               ],
               "Resource": "*"
           },
           {
               "Effect": "Allow",
               "Action": "sts:GetServiceBearerToken",
               "Resource": "*",
               "Condition": {
                   "StringEquals": {
                       "sts:AWSServiceName": "codeartifact.amazonaws.com"
                   }
               }
           }
       ]
   }
   ```
5. Click "Next"
6. Name the policy: `codeartifact-nextwork-consumer-policy`
7. Add a description: "Provides permissions to read from CodeArtifact. Created as a part of NextWork CICD Pipeline series."
8. Click "Create policy"

### 8️⃣ Create IAM Role and Attach to EC2

1. In the IAM console, select "Roles" from the left menu
2. Click "Create role"
3. For trusted entity type, select "AWS service"
4. For use case, select "EC2"
5. Click "Next"
6. Search for the policy you created: `codeartifact-nextwork-consumer-policy`
7. Select the checkbox next to it
8. Click "Next"
9. Role name: `EC2-instance-nextwork-cicd`
10. Description: "Allows EC2 instances to access services related to the NextWork CI/CD pipeline series."
11. Click "Create role"
12. Return to the EC2 console
13. Select your instance
14. Click Actions > Security > Modify IAM role
15. Select the IAM role you created: `EC2-instance-nextwork-cicd`
16. Click "Update IAM role"

### 9️⃣ Configure Maven to Use CodeArtifact

1. In your AWS CodeArtifact repository, click "View connection instructions"
2. Select "Mac and Linux" for Operating system and "mvn (Maven)" for Package manager client
3. Open VS Code connected to your EC2 instance
4. Create a new file called `settings.xml` in the root of your project
5. Add the following structure to the file:
   ```xml
   <settings>
   </settings>
   ```
6. From the connection instructions, copy the XML snippets from steps 4, 5, and 6 into your settings.xml file:
   - Server configuration inside `<settings>` tags
   - Profile configuration inside `<settings>` tags
   - Mirror configuration inside `<settings>` tags
7. Save the settings.xml file
8. In your terminal, run the export token command from the connection instructions
9. Compile your project using the settings file:
   ```sh
   mvn -s settings.xml compile
   ```
10. Check the terminal output for "Downloading from nextwork-devops-cicd" messages

### 🔟 Verify CodeArtifact Integration

1. Return to the CodeArtifact console
2. Close the connection instructions window
3. Navigate to your repository and check the Packages section
4. You should see Maven packages that were downloaded during compilation
5. If you don't see any packages, click the refresh button

## 🧹 Cleanup (Optional)

When you're done with the project, you can clean up resources to avoid costs:

1. Terminate your EC2 instance
2. Delete the IAM role and policy
3. Delete the CodeArtifact repository and domain
4. Delete the key pair file from your local machine

## 🎉 Congratulations!

You've successfully set up a CodeArtifact repository and integrated it with your Java web application. This is a crucial step in building a proper CI/CD pipeline, as it gives you a secure, reliable way to manage dependencies.

In the next project, you'll learn how to set up AWS CodeBuild to automatically build your web application.
