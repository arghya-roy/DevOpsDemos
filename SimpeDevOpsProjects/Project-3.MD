## Simple DevOps Project -3 


1. Launch an EC2 instance for Docker host

2. Install docker on EC2 instance and start services 
  ```sh 
  yum install docker
  service docker start
  ```

3. create a new user for Docker management and add him to Docker (default) group
```sh
useradd dockeradmin
passwd dockeradmin
usermod -aG docker dockeradmin
```
4. allow password authentication
```
vi /etc/ssh/sshd_config
service sshd restart 
```


5. Create a folder to write a Docker file under /opt/docker
```
mkdir /opt/docker
```
6. give ownership of that folder to dockeradmin user, by default it is owned by root
```
chown -R dockeradmin:dockeradmin /opt/docker
```

7. Create dockerfile
```
vi Dockerfile
```
8. Write file
```
From tomcat:8-jre8  
MAINTAINER "valaxytech"  
COPY ./webapp.war /usr/local/tomcat/webapps
```

9. Login to Jenkins console and add Docker server to execute commands from Jenkins  
Manage Jenkins --> Configure system -->  Publish over SSH --> add Docker server and credentials
```
Name - which name is given under name tag
Hostname - pvt ip of ec2
username - name of the user (ex-user1 / dockeradmin...)
check on Use password authentication, or use a different key
Passphrase / Password - password of that username
```


10. Create Jenkins job 

A) Source Code Management  
 Repository : https://github.com/arghya-roy/hello-world.git  
 Branches to build : */master  

B) Build
 Root POM: pom.xml  
 Goals and options : clean install package  
 
C) send files or execute commands over SSH
 Name: docker_host  
 Source files	: `webapp/target/*.war`
 Remove prefix	: `webapp/target`
 Remote directory	: `//opt//docker`  
 Exec command[s]	: 
  ```sh
  docker stop valaxy_demo;
  docker rm -f valaxy_demo;
  docker image rm -f valaxy_demo;
  cd /opt/docker;
  docker build -t valaxy_demo .
  ```

D) send files or execute commands over SSH  
  Name: `docker_host`  
  Exec command	: `docker run -d --name valaxy_demo -p 8090:8080 valaxy_demo`  

11. Login to Docker host and check images and containers. (no images and containers)

12. Execute Jenkins job

13. check images and containers again on Docker host. This time an image and container get creates through Jenkins job

14. Access web application from browser which is running on container
```
<docker_host_Public_IP>:8090/webapp/
```
