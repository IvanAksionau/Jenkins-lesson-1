# Jenkins-lesson-1

- www.virtualbox.org - download and install Oracle VM virtualbox manager
- https://www.centos.org/download/ - download centos
- open OracleVMVirtualBox and create a new virtual machine based on centos iso
- https://www.putty.org/- download ssh client for Windows
- launch putty ssh client to connect via address hosted for virtual machine ( IP Address is 192.168.100.6 )

--------------------------------------------------------------
install docker on centos : https://docs.docker.com/engine/install/centos/#installation-methods 
$ sudo yum install -y yum-utils
$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
$ sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
$ sudo systemctl start docker - to start docker service++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
$ sudo usermod -aG docker user_name -  to grant access of session(also logout to apply settings)

--------------------------------------------------------------
- Install docker-compose:
$ sudo curl -L https://github.com/docker/compose/releases/download/1.20.1/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose - give executable permissions to a file
$ docker-compose --version
--------------------------------------------------------------
install docker Jenkins image(snapshot configuration) : 
$ docker pull jenkins/jenkins
$ docker images -  to check if image was downloaded
$ docker info | grep -i root  - to check where docker is saving files

--------------------------------------------------------------
Create Docker compose file for Jenkins: 
$ mkdir jenkins_data - create directory
$ cd jenkins_data/ - move to created directory
$ mkdir jenkins_docker_home - create directory for 'volumes'
$ vi docker-compose.yaml - to start file creation, then fill in the data below:
version: '3'
services:
  jenikins:
    container_name: jenkins
    image: jenkins/jenkins
    ports:
      - "8080:8080"
      volumes:
        - "$PWD/jenkins_docker_home:/var/jenkins_home"
      networks:
        - net
networks:
  net:
  
$ sudo chown 1000:1000 jenkins_docker_home -R  - approve permissions to write to folder(1000 is user id(check with '$ id'))

--------------------------------------------------------------
Start Docker container: 
$ docker-compose up -d    -to start container
$ docker logs -f jenkins - get logs of container (5f453115e526442d80b86b3b4d5f149d)
$ open jenkins host in browser http://192.168.100.6:8080/ (also you can difine host in C:\Windows\System32\drivers\etc\host
$ docker-compose stop - to stop the container
$ docker-compose restart jenkins - to restart the container

We created a docker container(and 'container_name: jenkins' has Jenkins service up and running)
- We can go inside container by:
$ docker exec -ti jenkins bash (exit - to exit)
$ docker cp script.sh jenkins:/tmp/script.sh    -  copy script.sh from container 'jenkins'


https://jenkinsci.github.io/job-dsl-plugin

mvn clean test "-Dbrowserstack.user=ivanaksionau_T0Ca2z" "-Dbrowserstack.key=APV8uvviFmVAWhUg71iY" "-Demulator.location=remote"

curl -u 'ivan_vOIhrM:qFr7kmY16GrsXyPhNwmN' -X POST 'https://api-cloud.browserstack.com/app-automate/upload' -F 'file=@C:\Users\ivan.aksionau\IdeaProjects\appium/src/main/resources/ApiDemos-debug.apk


---------------------JENKINS_FILE---------------------------------------------------- https://www.jenkins.io/doc/book/pipeline/
pipeline {
    agent any
    
    environment{
        secret = credentials('SECRET_VALUE')
        NAME = 'ivan'
        FULLNAME = 'AKS'
    }
    stages {
        stage('Build') { 
            steps {
                sh 'echo "Building"'
                sh 'echo $NAME $FULLNAME'
                sh 'echo $secret'
                sh '''
                   echo "Additional sh commands are applied"
                   ls -lah
                   '''
            }
        }
        stage('Test') { 
            steps {
                echo 'Testing' 
            }
        }
        stage('Timeout') { 
            steps {
                retry(3){
                    sh 'echo this will be retried 3 times in case of fail'
                }
                timeout(time: 3, unit: 'SECONDS'){
                    sh 'sleep 5'
                }
            }
        }
        stage('Deploy') { 
            steps {
                echo 'Deploying' 
            }
        }
    }
    post {
        always{
            echo 'will always be xececuted'
        }
        seccess{
            echo 'in case of seccess be xececuted'
        }
        failure{
            echo 'in case of failure be xececuted'
        }
        unstable{
            echo 'in case of unstable be xececuted'
        }
}
