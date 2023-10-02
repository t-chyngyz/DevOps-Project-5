# DevOps-Project-5
![2 Tier App Deployment (1)](https://github.com/rutikdevops/DevOps-Project-5/assets/109506158/5fc26e1e-e8f7-48f5-a71f-342dff3ef608)


# Project Blog link :-

<br></br>

# Project Overview :-

# Project Steps :-
- Create 1 ec2 instance :
2-Tier-App-Docker    : AWS Linux-2, t2 micro
<br></br>
![image](https://github.com/rutikdevops/DevOps-Project-5/assets/109506158/a2e3083b-e690-4709-9904-7c5549d78295)
<br></br>
- Goto Security-> security group-> Edit inbound rules-> Add rule-> choose All Traffic
![image](https://github.com/rutikdevops/DevOps-Project-5/assets/109506158/c33dcb98-c446-4d35-b9e0-b24cfedbb1b7)



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
vi Dockerfile

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
```bash
docker build . -t flaskapp          #  Create a docker image from Dockerfile
docker images
```
```bash
docker network create twotier       #  Now, make sure that you have created a network using following command
```

- Attach both the containers in the same network, so that they can communicate with each other
```bash
docker run -d -p 3306:3306 --network=twotier -e MYSQL_DATABASE=myDb -e MYSQL_USER=admin -e MYSQL_PASSWORD=admin -e MYSQL_ROOT_PASSWORD=admin --name=mysql mysql:5.7

docker run -d -p 5000:5000 --network=twotier -e MYSQL_HOST=mysql -e MYSQL_USER=admin -e MYSQL_PASSWORD=admin -e MYSQL_DB=myDb --name=flaskapp flaskapp:latest

docker ps
docker network ls
docker network inspect twotier
```

# 3. Create the messages table in your MySQL database:
```bash
docker ps
docker exec -it <paste here mysql container-id> bash
mysql -u root -p
admin                       # enter password = admin
show databases;
use myDb;
# Paste this code in myDb
CREATE TABLE messages (
    id INT AUTO_INCREMENT PRIMARY KEY,
    message TEXT
);

# Now run the app in browser
![image](https://github.com/rutikdevops/DevOps-Project-5/assets/109506158/c2a5d9bf-a7bb-4eb3-840c-eb776be6c824)
select * from messages;      # To show the data entered in App
```

# 4. Push Docker Image in DockerHub :-
```bash
docker login
# Enter username & password
docker tag flaskapp:latest <github_username>/flaskapp:latest
docker images
docker push <github_username>/flaskapp:latest
```
![image](https://github.com/rutikdevops/DevOps-Project-5/assets/109506158/cc96151a-601f-4a99-b448-9c295c1d6fdb)


# 5. Use docker-compose
- Run App using one single command instead of running both the containers  :-
```bash
apt install docker-compose -y
vi docker-compose.yml

///
version: '3'
services:
  
  backend:
    build:
      context: .
    ports:
      - "5000:5000"
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: admin
      MYSQL_PASSWORD: admin
      MYSQL_DB: myDb
    depends_on:
      - mysql

  mysql:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: myDb
      MYSQL_USER: admin
      MYSQL_PASSWORD: admin
    volumes:
      - ./message.sql:/docker-entrypoint-initdb.d/message.sql   # Mount sql script into container's /docker-entrypoint-initdb.d directory to get table automatically created
      - mysql-data:/var/lib/mysql  # Mount the volume for MySQL data storage

volumes:
  mysql-data:
///

docker ps
docker kill (paste here container-id)
docker rm (paste here container-id)
docker-compose up -d
# Now your docker-compose app is running
```
![image](https://github.com/rutikdevops/DevOps-Project-5/assets/109506158/3a3ca0c9-9cda-42b7-9c43-27e32df53533)





