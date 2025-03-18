# 🚀 Set Up Your Web App in the Cloud 

## 🎯 Objective

- Launch an EC2 instance
- Set up VSCode on your local computer
- Use Remote - SSH to connect VSCode with your EC2 instance
- Set up your web app

## 🔥 Steps to Follow

### 1️⃣ Launch an EC2 Instance

Since we want your web app to be entirely created and run on the cloud, we'll use a virtual server (EC2 instance) to house our development work.

1. Log in to AWS as an IAM Admin User
2. Head to Amazon EC2
3. Switch your Region to the one closest to you

> 💡 **Tip**: We recommend using the following regions:
> 
> There are only 13 AWS Regions that provide ALL the services we'll use in the 7 Day DevOps Challenge:
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

4. In your EC2 console, select Instances from the left hand navigation panel
5. Choose Launch instances
6. Set up your EC2 instance:
   - In Name, enter the value `nextwork-devops-yourname` (replace yourname with your name)
   - Choose Amazon Linux 2023 AMI under Amazon Machine Image(AMI)
   - Leave t2.micro under Instance type
   - Under Key pair (login), select Create a new key pair and use `nextwork-keypair` as your key pair's name
   - Store nextwork-keypair.pem in a new folder called DevOps in your local computer's Desktop
7. Back to our EC2 instance setup, head to the Network settings section
8. For Allow SSH traffic from, select the dropdown and choose My IP
9. Choose Launch instance

### 2️⃣ Connect to EC2 via SSH (Terminal)

1. Open VSCode in your local computer
2. Select Terminal from the top menu bar
3. Select New Terminal from the dropdown
4. Navigate your terminal to the DevOps folder: 
   - `cd ~/Desktop/DevOps` (Mac/Linux) 
   - or `cd C:\Users\YourUserName\Desktop\DevOps` (Windows)
5. Once you're in the DevOps folder, check if your .pem file is there: 
   - `ls` (Mac/Linux) 
   - or `dir` (Windows)
6. Change the permissions of your .pem file:
   - For Mac/Linux: `chmod 400 nextwork-keypair.pem`
   - For Windows:
     ```
     icacls "nextwork-keypair.pem" /reset
     icacls "nextwork-keypair.pem" /grant:r "USERNAME:R"
     icacls "nextwork-keypair.pem" /inheritance:r
     ```
     (replace "USERNAME" with your Windows username)
7. Head back to your AWS Management Console
8. Click on Instances from the left hand navigation panel
9. Click on the checkbox next to your EC2 instance to view its details
10. Under the Details tab, look for Public IPv4 DNS
11. Head back to VSCode and open your terminal again
12. Use the following command to connect to your EC2 instance: 
    ```
    ssh -i [PATH TO YOUR .PEM FILE] ec2-user@[YOUR PUBLIC IPV4 DNS]
    ```
13. Your terminal will ask if you want to continue connecting to this EC2 instance. Enter yes to continue connecting

### 3️⃣ Install Apache Maven and Amazon Corretto 8

Once connected to your EC2 instance, install Java and Maven:

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

> 💡 **Important**: If the Java version command doesn't return openjdk version 1.8, run:
> ```sh
> sudo alternatives --config java
> ```

### 4️⃣ Generate a Basic Web App with Maven

1. Use Maven to generate a Java web app:
   ```sh
   mvn archetype:generate \
      -DgroupId=com.nextwork.app \
      -DartifactId=nextwork-web-project \
      -DarchetypeArtifactId=maven-archetype-webapp \
      -DinteractiveMode=false
   ```

2. Watch for a BUILD SUCCESS message in your terminal

### 5️⃣ Set Up VS Code Remote SSH

1. If you don't have Remote - SSH installed in VSCode already, select the Extensions icon and install it
2. Click on the double arrow icon at the bottom left corner of your VSCode window
3. Select Connect to Host...
4. Select + Add New SSH Host...
5. Enter the SSH command you used to connect to your EC2 instance:
   ```
   ssh -i [PATH TO YOUR .PEM FILE] ec2-user@[YOUR PUBLIC IPV4 DNS]
   ```
6. Select the configuration file (should look similar to /Users/username/.ssh/config)
7. Click on the double arrow button on the bottom left corner and select Connect to Host again
8. Select your EC2 instance
9. From VSCode's left hand navigation bar, select the Explorer icon
10. Select Open folder
11. Enter `/home/ec2-user/nextwork-web-project`
12. Press OK
13. If asked if you trust the authors of the files in this folder, select Yes

### 6️⃣ Modify Your Web App

1. From your file explorer, click into index.jsp
2. Modify index.jsp by changing the placeholder code:
   ```html
   <html>
   <body>
   <h2>Hello {YOUR NAME}!</h2>
   <p>This is my NextWork web application working!</p>
   </body>
   </html>
   ```
3. Save the changes (Command/Ctrl + S)

### 7️⃣ Install Git

1. In your EC2 instance's terminal, install Git:
   ```sh
   sudo dnf update -y
   sudo dnf install git -y
   ```

2. Verify the installation:
   ```sh
   git --version
   ```

### 8️⃣ Set Up GitHub and Push Your Code

1. Create a new repository on GitHub named `nextwork-web-project`
2. Initialize Git in your project folder:
   ```sh
   git init
   ```
3. Connect your local repo with your GitHub repo:
   ```sh
   git remote add origin [YOUR GITHUB REPO LINK]
   ```
4. Stage, commit, and push your changes:
   ```sh
   git add .
   git commit -m "Updated index.jsp with new content"
   git push -u origin master
   ```
5. When prompted for your GitHub credentials, you'll need to set up a token instead of using your password

### 9️⃣ Set Up a GitHub Token

GitHub no longer accepts passwords for repository operations. You'll need to create a token:

1. In GitHub, select your profile icon and select Settings
2. Select Developer settings at the bottom of the left navigation panel
3. Select Personal access tokens
4. Select Tokens (classic)
5. Select Generate new token
6. Select Generate new token (classic)
7. Give your token a descriptive note like "Generated for EC2 Instance Access"
8. Lower the token expiration limit from 30 days to 7 days
9. Select the checkbox next to repo
10. Select Generate token
11. Copy your token immediately (you won't be able to see it again)
12. Back in your VS Code terminal, when prompted for a password during git push, paste your token
13. Optionally, configure Git to remember your credentials:
    ```sh
    git config --global credential.helper store
    ```

### 🔟 Set Git Identity and Make More Changes

1. Set your Git identity:
   ```sh
   git config --global user.name "Your Name"
   git config --global user.email you@nextwork.org
   ```

2. Make additional changes to index.jsp, then commit and push again:
   ```sh
   git add .
   git commit -m "Add new line to index.jsp"
   git push
   ```

Congratulations! You've successfully set up your web app development environment in the cloud, connected it to GitHub, and started developing your application.