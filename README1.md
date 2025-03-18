🚀 Day 1: Setting Up an EC2 Instance & Connecting with VS Code
🎯 Objective
On Day 1, we will set up the foundational infrastructure needed for our DevOps journey by:

✅ Launching an AWS EC2 instance
✅ Connecting to it using VS Code (via SSH)
✅ Installing Java & Maven to prepare for web app development
✅ Generating a basic web application using Maven
✅ Editing code without VS Code to understand the power of IDEs

🔥 Steps to Follow
1️⃣ Launch an EC2 Instance

Log in to AWS Console.
Navigate to EC2 > Launch Instance.
Choose the Amazon Linux 2 AMI (or Ubuntu if preferred).
Select t2.micro (free-tier eligible).
Configure instance settings and security group:

Allow SSH (port 22)
Allow HTTP (port 80) for future web app access.


Generate and download a .pem key pair (keep it safe!).
Launch the instance.

2️⃣ Connect to EC2 via SSH (Terminal)
If using a local terminal:
shCopychmod 400 your-key.pem  # Secure your private key
ssh -i "your-key.pem" ec2-user@your-ec2-public-ip
3️⃣ Set Up VS Code Remote SSH

Open VS Code.
Install the Remote - SSH extension.
Add a new SSH host:

Open Command Palette (Ctrl + Shift + P)
Select Remote-SSH: Add New SSH Host
Enter:
shCopyssh -i "your-key.pem" ec2-user@your-ec2-public-ip

Save & Connect.



4️⃣ Install Java & Maven
Once connected to your EC2 instance, install Java and Maven:
shCopysudo yum update -y
sudo yum install java-11-amazon-corretto -y
java -version

sudo yum install maven -y
mvn -version
5️⃣ Generate a Basic Web App with Maven
Run the following command to generate a simple web application:
shCopymvn archetype:generate \
    -DgroupId=com.devops \
    -DartifactId=my-web-app \
    -DarchetypeArtifactId=maven-archetype-webapp \
    -DinteractiveMode=false
6️⃣ Test the App
Once the project is generated, navigate to the directory and package the application:
shCopycd my-web-app
mvn package