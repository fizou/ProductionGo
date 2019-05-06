ProductionGo:

 #####   #####    ####   #####   ##  ##   ####   ######  ######   ####   ##  ##           ####    ####          
 ##  ##  ##  ##  ##  ##  ##  ##  ##  ##  ##  ##    ##      ##    ##  ##  ### ##          ##      ##  ##         
 #####   #####   ##  ##  ##  ##  ##  ##  ##        ##      ##    ##  ##  ## ###          ## ###  ##  ##         
 ##      ##  ##  ##  ##  ##  ##  ##  ##  ##  ##    ##      ##    ##  ##  ##  ##          ##  ##  ##  ##         
 ##      ##  ##   ####   #####    ####    ####     ##    ######   ####   ##  ##           ####    ####          

         USER -> serveo -> nginx -> tomcat <- nexus <- sonar <- jenkins <- github <- DEVELOPPER

Execute jenkins (8080) ... Orchestrator to build, control and deploy war  
Execute sonar   (9000) ... Control the quality of war artifact      
Execute nexus   (8081) ... Store the war artifact and keep history 
Execute tomcat  (8083) ... Application Container
Execute nginx   (8084) ... Web Server
Execute serveo  (XXXX) ... Expose nginx server on internet (http://xxxx.serveo.net) 

Need also, a github application: Source of application (Ex: pokebible) with jenkins/shell files to execute
 
---- Automatic Run

docker-compose up --force-recreate
docker-compose restart serveo

docker stats

---- Manual Run

docker network create pgnetwork

docker run -d --name jenkins --net pgnetwork -p 8080:8080 -p 50000:50000 -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\jenkins:/var/jenkins_home jenkins/jenkins

docker run -d --name nexus --net pgnetwork -p 8081:8081 -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\nexus:/nexus-data sonatype/nexus3

docker run -d --name sonar --net pgnetwork -p 9000:9000 -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\sonar\data:/opt/sonarqube/data -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\sonar\logs:/opt/sonarqube/logs sonarqube

docker run -d --name tomcat --net pgnetwork -p 8083:8080 -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\tomcat\conf\tomcat-users.xml:/usr/local/tomcat/conf/tomcat-users.xml -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\tomcat\conf\Catalina:/usr/local/tomcat/conf/Catalina -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\tomcat\logs:/usr/local/tomcat/logs tomcat

docker run -d --name nginx --net pgnetwork -p 8084:80 -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\nginx\html:/usr/share/nginx/html -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\nginx\etc\nginx.conf:/etc/nginx/nginx.conf -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\nginx\etc\conf.d:/etc/nginx/conf.d -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\nginx\log:/var/log/nginx nginx

docker run -it --name serveo --net pgnetwork alpinewithopenssh /bin/sh -c "ssh -R 80:nginx:80 -o 'StrictHostKeyChecking no' serveo.net"

---- NOTES :

** Roadmap

Commit ProductionGo on GITHub
Translation of item (Zanata)
Automated test 
Table Leaf vs REACT
Correction Major issues sonar on Pokebible (Commentary / name fr)

** To Begin with docker:

Run Alpine (5mo) : docker run -it alpine /bin/sh
Run Ubuntu (88Mo) : docker run -it ubuntu /bin/bash
Run Debian (101Mo) : docker run -it debian /bin/bash

docker images
update all images
docker images |grep -v REPOSITORY|awk '{print $1}'|xargs -L1 docker pull 

docker ps
docker rm -f "mystifying_shockley"

docker exec -it "tomcat" /bin/bash
docker run -it "alpinewithopenssh" /bin/sh -c "echo a && echo b"

docker system df
docker system prune

apt-get update && apt-get install bash
apt-get update && apt-get install -y vim


Create file test123 by vi :
#!/bin/bash
echo olivier

Change autorization
chmod 777 test or chmod u+x

Execute Shell :
sh test123 or bash test123 -x or ./test123 or test123

Advanced :
Run With Volume : docker run -it -v C:\:/vol ubuntu /bin/bash
docker run -it -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes:/vol ubuntu /bin/bash

manuelement:  "export PATH=$PATH:/vol;cd /vol;chmod 777 test123;test123" 

** Dockerfile

Create Dockerfile
FROM ubuntu
RUN apt-get update && \
    apt-get install -y vim 

docker build -t productiongomaster:latest .
docker run -it productiongomaster:latest /bin/bash
docker run -it -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes:/vol productiongomaster:latest /bin/bash

** to set up docker-compose 

docker-compose restart serveo

docker-compose-restart(){
	docker-compose stop $@
	docker-compose rm -f -v $@
	docker-compose create --force-recreate $@
	docker-compose start $@
}

depends_on:
      rabbit:
        condition: service_healthy
    links: 
        - rabbit


** Jenkins 
admin 9bce9de30b354793abc89b3756bf2d79 (ne pas mettre -d pour l'obtenir c'est dans les logs)
 
http://localhost:49001/
http://2997be36.ngrok.io/
docker run -p 49001:8080 jenkins/jenkins
docker run -p 49001:8080 -p 50000:50000 -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\jenkins:/var/jenkins_home jenkins/jenkins

** To Set github  :
Manage Jenkins / Configure Credentials : add Username and password AND secretkey
Manage Jenkins / Confgure System : add github server with credential (user + generate key)

pipeline {
    agent any
    stages {
        stage('Begin compilation') {
            steps {
                echo 'Hello world !'
            }
        }
        stage('End compilation') {
            steps {
                echo 'good bye !'
            }
        }
    }
}

** To Set Pipe line docker  :
Manage Jenkins / Configure system : Add docker label = 

** to setup NEXUS:

docker run -d -p 8081:8081 --name nexus sonatype/nexus3
docker run -d -p 8081:8081 --name nexus -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\nexus:/nexus-data sonatype/nexus3

admin / admin123
172.17.0.3

** to set up network

docker network ls

bridge est le reseau par default 

docker network inspect bridge : permet de connaitre les adresses ip des containeurs.

apt-get update && apt-get install dnsutils

docker network create <network name>
docker run --net <network name> --name test busybox nc -l 0.0.0.0:7000
docker run --net <network name> busybox ping test


** to setup tomcat 

docker run -it --name tomcat -p 8080:8080 tomcat
docker run -it -p 8080:8080 -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\jenkins\.m2\repository\com\pokebible\pokebible\1.0.9-SNAPSHOT\pokebible-1.0.9-SNAPSHOT.war:/usr/local/tomcat/webapps/pokebible.war tomcat

docker run -it -p 8080:8080 -v D:\Users\olfize\Documents\Programmation\NetBeansProjects\pokebible\target\pokebible-1.0.9-SNAPSHOT.war:/usr/local/tomcat/webapps/pokebible.war tomcat
docker run -it -p 8080:8080 -v D:\Users\olfize\Documents\Programmation\NetBeansProjects\pokebible\target\pokebible-1.0.9-SNAPSHOT.war:/usr/local/tomcat/webapps/ROOT.war -v D:\Users\olfize\Documents\Programmation\NetBeansProjects\pokebible\target:/usr/local/tomcat/webapps/ROOT tomcat

docker run -it -p 8080:8080 -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\jenkins\workspace\Pipe_Line_Build_Pokebible_master\target\pokebible-1.0.9-SNAPSHOT.war:/usr/local/tomcat/webapps/pokebible.war tomcat

docker run -it -p 8083:8080 -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\tomcat:/usr/local/tomcat tomcat

ne marche pas:
docker volume create --name tomcat-data -o type=none -o device=D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\tomcat -o o=bind
docker run -ti --rm -p 8888:8080 -v tomcat-data:/usr/local/tomcat/ tomcat

** To set up tomcat
docker run -it --name tomcat --net pgnetwork -p 8083:8080 -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\tomcat\conf\tomcat-users.xml:/usr/local/tomcat/conf/tomcat-users.xml -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\tomcat\conf\Catalina:/usr/local/tomcat/conf/Catalina -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\tomcat\logs:/usr/local/tomcat/logs tomcat


-v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\tomcat\webapps:/usr/local/tomcat/webapps tomcat

tomcat-users.xml

<tomcat-users>

  <role rolename="admin-gui"/>
  <user username="tomcat" password="s3cret" roles="manager-gui"/>

</tomcat-users>


** To set up Sonarqube

docker run -d --name sonar -p 9000:9000 sonarqube

    Login: admin
    Password: admin

token project test : d9660ad575c4f859e0bc1ad323fe031f09d1da4e

docker run -d --name sonarqube -p 9000:9000 -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\sonar\data:/opt/sonarqube/data -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\sonar\logs:/opt/sonarqube/logs -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\sonar\extensions:/opt/sonarqube/extensions sonarqube


               <goal>sonar:sonar</goal>
            </goals>
            <properties>
                <sonar.projectKey>test</sonar.projectKey>
                <sonar.host.url>http://localhost:9000</sonar.host.url>
                <sonar.login>d9660ad575c4f859e0bc1ad323fe031f09d1da4e</sonar.login>


** to setup nginx
docker run --name mynginx1 -p 8084:80 nginx
docker run --name mynginx2 -p 8084:80 -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\nginx\html:/usr/share/nginx/html nginx
docker run --name mynginx3 -p 8084:80 -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\nginx\html:/usr/share/nginx/html -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\nginx\etc\nginx.conf:/etc/nginx/nginx.conf -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\nginx\etc\conf.d:/etc/nginx/conf.d nginx
docker run --name mynginx4 -p 8084:80 -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\nginx\html:/usr/share/nginx/html -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\nginx\etc\nginx.conf:/etc/nginx/nginx.conf -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\nginx\etc\conf.d:/etc/nginx/conf.d -v D:\Users\olfize\Documents\Programmation\docker\ProductionGo\volumes\nginx\log:/var/log/nginx nginx

/var/log/nginx

/etc/nginx/conf.d/default.conf

docker exec -it "mynginx2" /bin/bash


** to setup serveo


1) Here is the Docker the docker-compose.yml

version: '2'

services:
  serveo:
    build: ./dockerfiles/alpinewithopenssh
    image: alpinewithopenssh:latest
    tty: true
    stdin_open: true
    # see https://serveo.net/ for more options
    command: "ssh -R 80:nginx:80 -o \"StrictHostKeyChecking no\" serveo.net"
  nginx:
    image: nginx:latest

2) Create a dockerfile in ./dockerfiles/alpinewithopenssh

FROM alpine:latest

RUN apk add --no-cache openssh

CMD ["/bin/sh"]


3) Run 
docker run -it alpinewithopenssh "/bin/sh"
docker run -it alpinewithopenssh /bin/sh -c "ssh -R 80:nginx:80 -o 'StrictHostKeyChecking no' serveo.net"



