# Demo Project: Deploy Web Application on EC2 Instance (Manually)

## **Project Overview**
This project demonstrates how to manually deploy a Dockerized web application on an AWS EC2 instance using a private Docker Hub repository. The goal is to configure an EC2 instance, install Docker, and deploy the application.

### **Technologies Used**
- **AWS**: For creating and configuring the EC2 instance.
- **Docker**: To containerize and deploy the web application.
- **Linux**: To configure and manage the remote server.

---

## **Project Steps**

### **1. Prerequisites**
Before starting, ensure you have the following:
- An **AWS account** with permissions to create EC2 instances.
- A **Docker Hub account** with a private repository.
- A **Dockerized web application image** in your private repository.
- SSH key pair to connect to the EC2 instance.
- **PuTTY/Terminal** installed for SSH access.

---

### **2. Launch and Configure EC2 Instance**

1. **Log in to AWS Management Console**.
2. Navigate to **EC2 Dashboard** → **Launch Instance**.
3. Configure the following:
   - **Name**: `demo-web-app`
   - **AMI**: Amazon Linux 2
   - **Instance Type**: t2.micro (free-tier eligible)
   - **Key Pair**: Select or create a new SSH key pair.
   - **Security Group**:
     - Allow **SSH** (port 22) for your IP.
     - Allow **HTTP** (port 80) for all IP addresses.
4. **Launch the instance** and wait for it to be in a `running` state.

5. **Connect to the EC2 instance** using SSH:
   - For Linux/Mac/WSL:
     ```bash
     ssh -i "<path-to-your-key.pem>" ec2-user@<EC2-Public-IP>
     ```
   - For Windows (PuTTY):
     - Convert `.pem` to `.ppk` using PuTTYgen.
     - Connect via PuTTY with the IP and private key.

---

### **3. Install Docker on the EC2 Instance**
Run the following commands to install and start Docker:

```bash
# Update the package manager
sudo yum update -y

# Install Docker
sudo amazon-linux-extras install docker -y

# Start Docker service
sudo service docker start

# Add ec2-user to the docker group to run docker without sudo
sudo usermod -aG docker ec2-user

# Reload group permissions
newgrp docker

# Verify Docker installation
docker --version
```

---

### **4. Log in to Docker Hub from EC2**
Log in to your Docker Hub account to pull the private image:

```bash
docker login -u <your-dockerhub-username> -p <your-dockerhub-password>
```
- Replace `<your-dockerhub-username>` and `<your-dockerhub-password>` with your Docker Hub credentials.

---

### **5. Pull and Run the Docker Image**
Pull your application image from Docker Hub and run it as a container.

1. **Pull the image**:
   ```bash
   docker pull <your-dockerhub-username>/<your-repo-name>:<tag>
   ```

2. **Run the container**:
   ```bash
   docker run -d -p 80:3000 <your-dockerhub-username>/<your-repo-name>:<tag>
   ```
   - `-d`: Runs the container in detached mode.
   - `-p 80:3000`: Maps port 3000 in the container to port 80 on the EC2 instance.

3. **Verify the container is running**:
   ```bash
   docker ps
   ```

4. **Test the deployment**:
   - Open a web browser and visit `http://<EC2-Public-IP>`.
   - You should see your web application running.

---

### **6. Clean Up (Optional)**
To avoid additional charges, stop or terminate your EC2 instance when no longer needed.

- **Stop the instance**:
   ```bash
   aws ec2 stop-instances --instance-ids <Instance-ID>
   ```
- **Terminate the instance**:
   ```bash
   aws ec2 terminate-instances --instance-ids <Instance-ID>
   ```

---

## **Conclusion**
This guide demonstrates a manual deployment of a Dockerized web application on an AWS EC2 instance using a private Docker Hub repository. By following these steps, you can set up, configure, and deploy applications efficiently.

---

## **Commands Summary**
| Step                           | Command                                                                 |
|--------------------------------|------------------------------------------------------------------------|
| Connect to EC2 Instance        | `ssh -i "<key.pem>" ec2-user@<EC2-Public-IP>`                          |
| Install Docker                 | `sudo amazon-linux-extras install docker -y`                          |
| Start Docker                   | `sudo service docker start`                                            |
| Add User to Docker Group       | `sudo usermod -aG docker ec2-user`                                     |
| Docker Login                   | `docker login -u <username> -p <password>`                            |
| Pull Docker Image              | `docker pull <username>/<repo>:<tag>`                                 |
| Run Docker Container           | `docker run -d -p 80:3000 <username>/<repo>:<tag>`                    |
| Verify Running Container       | `docker ps`                                                           |
| Test Deployment                | Visit `http://<EC2-Public-IP>`                                        |
| Stop EC2 Instance (Optional)   | `aws ec2 stop-instances --instance-ids <Instance-ID>`                 |
| Terminate EC2 Instance         | `aws ec2 terminate-instances --instance-ids <Instance-ID>`            |

---

Happy Deploying! 🚀
