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



