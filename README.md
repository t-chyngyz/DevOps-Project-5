# DevOps-Project-5

![2 Tier App Deployment (1)](https://github.com/rutikdevops/DevOps-Project-5/assets/109506158/5fc26e1e-e8f7-48f5-a71f-342dff3ef608)


# Project Blog link :-
https://medium.com/@rutikdevops/devops-project-4-31891d829c00
<br></br>

# Project Overview :-

# Project Steps :-
- Create 1 ec2 instance :
2-Tier-App-Docker    : AWS Linux-2, t2 micro
<br></br>
![image](https://github.com/rutikdevops/DevOps-Project-5/assets/109506158/a2e3083b-e690-4709-9904-7c5549d78295)

# 1. Install and Configure the Docker :-
```bash
ubuntu
sudo su
apt update -y
apt install docker.io -y
hostnamectl set-hostname docker
bash
docker ps
whoami
chown $USER /var/run/docker.sock
```
# 2. Clone the Github code :-
```bash
git clone https://github.com/LondheShubham153/two-tier-flask-app.git
cd two-tier-flask-app
```
# 3. Create Docker File :-
```bash
# Use an official Python runtime as the base image
FROM python:3.9-slim

# Set the working directory in the container
WORKDIR /app

# install required packages for system
RUN apt-get update \
    && apt-get upgrade -y \
    && apt-get install -y gcc default-libmysqlclient-dev pkg-config \
    && rm -rf /var/lib/apt/lists/*

# Copy the requirements file into the container
COPY requirements.txt .

# Install app dependencies
RUN pip install mysqlclient
RUN pip install --no-cache-dir -r requirements.txt

# Copy the rest of the application code
COPY . .

# Specify the command to run your application
CMD ["python", "app.py"]
```












