## Installed Jenkins on AWS EC2 instance to run sudo commands without any restrictions and passwords

Here is a step by step guide to install jenkins on EC2 instance and to edit `/etc/sudoers` to run sudo commands:

### Step 1: Launch an EC2 Instance
1. Go to the AWS Console and navigate to EC2 > Instances.
2. Launch a new EC2 instance:   
        - Choose Ubuntu Server as the Amazon Machine Image (AMI)   
        - Select an instance type (e.g t4g micro)    
        - Configure security group settings to allow inbound access on port 8080 (for Jenkins) and port 22 (for SSH access).
3. Launch the instance and connect to it via SSH.
![Jenkins Instance](https://github.com/user-attachments/assets/21e37d10-83d5-4106-95c8-f31a2e006d54)


### Step 2: Update the System

Once connected to the EC2 instance via SSH, update the system packages:

```
sudo apt update
sudo apt upgrade -y
```

### Step 3: Install Java

Jenkins requires Java, so let’s install OpenJDK:
```
sudo apt install openjdk-11-jdk -y
```
Verify the Java installation:
```
java -version
```

### Step 4: Add the Jenkins Repository
Add Jenkins to the package source list:
```
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```

### Step 5: Install Jenkins
After adding the repository, install Jenkins:

```
sudo apt update
sudo apt install jenkins -y
```

### Step 6: Start and Enable Jenkins
Start Jenkins and ensure it starts automatically on boot:
```
sudo systemctl start jenkins
sudo systemctl enable jenkins
```
Check the status to ensure Jenkins is running:
```
sudo systemctl status jenkins
```
### Step 7: Adjust Firewall Rules
If you’re using Ubuntu UFW firewall (optional), open port 8080:
```
sudo ufw allow 8080
sudo ufw enable
sudo ufw status
```

### Step 8: Access Jenkins

1. Open your web browser and go to http://<your-ec2-public-ip>:8080. `http://44.234.72.219:8080/`
2. You’ll be prompted for an initial admin password. Retrieve it by running:
```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
3. Copy the password, paste it into the Jenkins setup page, and proceed with the installation. 

### Step 9: Complete Jenkins Setup
1. Install the recommended plugins or customize as needed.
2. Create an admin user, configure other settings, and finalize the setup.

### Final Output of the Jenkins Dashboard

![Jenkins Dashboard](https://github.com/user-attachments/assets/b7587aba-17a8-435b-b01c-1962af71878e)

### Step 10: Guide to Editing /etc/sudoers on EC2
1. Connect to the EC2 Instance:     
   SSH into your instance:   
   ```
   ssh -i path/to/your-key.pem ec2-user@your-ec2-public-ip
   ```
2. Open /etc/sudoers Using visudo:
   Run the following command to open the sudoers file in a safe editor:
   ```
   sudo su
   sudo visudo
   ```
3. Edit the File as Needed:
   Add the following command in the last section of the editor
   ```
   jenkins ALL=(ALL) NOPASSWD: /usr/bin/apt-get
   ```
4. Restart your EC2 instance to apply the changes

## Jenkins Pipeline Step-by-Step Guide:
   
### Step 1: Create a simple Python web application repository on Github with following repository structure

```
Your Repo Name/
├── templates/
│   └── index.html
├── tests/
│   └── test_app.py
├── Jenkinsfile
├── README.md
├── app.py
└── requirements.txt
```
### Step 2: Create a `Jenkinsfile` in the root of your project repository with the following stages:

![Pipeline Stages](https://github.com/user-attachments/assets/fb63ba40-d5c5-4584-8534-3b8162161305)

*Kindly refer `Jenkinsfile` for complete Groovy code*               

Complete Breakdown of the Pipeline Stages:    

Agent:
- `agent any:` This specifies that the pipeline can run on any available agent in the Jenkins environment.

Stages:   

1. Checkout: Checks out the latest code from the SCM.

![Checkout Stage](https://github.com/user-attachments/assets/1dd67d38-7596-4471-8df2-ceacd1e69d70)

2. Install pip: Installs the `pip` package manager on the agent if it's not already present.

![Install pip Stage](https://github.com/user-attachments/assets/bce28daf-2353-413b-81c7-3ab20f5c64f1)

3. Install Python venv: Installs the `venv` module for creating virtual environments.

![Python venv Stage](https://github.com/user-attachments/assets/6bbc6e28-4935-46ee-bf3a-cb28c37b4e93)

4. Check Python and pip Installation: Verifies the installation of Python and `pip`.

![Python and pip Installation Stage](https://github.com/user-attachments/assets/39282bd5-40c4-4a17-8d72-dd61378549fd)

5. Set Up Python: Creates a virtual environment, activates it, and installs dependencies from `requirements.txt`.

![Set Up Python Stage](https://github.com/user-attachments/assets/9508e261-02d6-46f5-9a2c-b3ad50ed8138)

![Set Up Python Stage 1](https://github.com/user-attachments/assets/685fbeca-cc26-4fd5-b6cd-8378db807a5f)

![Set Up Python Stage 2](https://github.com/user-attachments/assets/7a9c23c5-0142-4b2a-9f74-faaa8e206eb0)

6. Run Tests: Runs the test suite using `pytest`.

![Tests 1](https://github.com/user-attachments/assets/cd36bd1f-ee19-46a8-baea-4e9c9b25d2b8)

![Tests 2](https://github.com/user-attachments/assets/e689038c-843c-4e72-9e3c-5840faba79d5)

7. Move to Staging Environment: This stage is executed only if the previous stages are successful. It prints a message indicating the successful deployment to the staging environment.

![Staging Environment](https://github.com/user-attachments/assets/cc6274f5-9abb-4e45-893f-f7ef9b1f7331)

## Step 3: Configure the Pipeline to Trigger on Push: In the "Build Triggers" section of Jenkins, select "GitHub hook trigger for GITScm polling."    

   pollSCM('* * * * *'): This triggers the pipeline every minute to check for changes in the source code repository.    
   
![Triggers](https://github.com/user-attachments/assets/33291406-104f-4cb9-b9e6-675b75751493)

## Step 4: Email Notification: 

1. Go to Manage Jenkins > Configure System.
2. Scroll down to E-mail Notification.
    - SMTP Server: Enter the SMTP server for your email provider (e.g., smtp.gmail.com for Gmail).
    - Use SMTP Authentication: Check this if your SMTP server requires authentication.
    - User Name: Enter the SMTP username (for Gmail, it’s your full email address).
    - Password: Enter the SMTP password or an application-specific password if using a secure account like Gmail.
    - SMTP Port: Typically, use 587 for TLS or 465 for SSL (depending on your email provider).
    - Charset: UTF-8 is recommended.
3. Test the Configuration: You can send a test email by entering a recipient email address under Test configuration by sending a test email.      

![Test Email 1](https://github.com/user-attachments/assets/0937616e-3d5b-44d3-8a00-31a0ff244e1d)

![Test Email 2](https://github.com/user-attachments/assets/daa6d5d0-b857-4156-a27f-f32d635f9614)

## Final Output: Post Action Stage 

![Final Email](https://github.com/user-attachments/assets/2048b52c-9ebd-481f-9f92-78d17e3ad979)


