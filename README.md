# ProductionGo

	#####   #####    ####   #####   ##  ##   ####   ######  ######   ####   ##  ##           ####    ####  
	##  ##  ##  ##  ##  ##  ##  ##  ##  ##  ##  ##    ##      ##    ##  ##  ### ##          ##      ##  ## 
	#####   #####   ##  ##  ##  ##  ##  ##  ##        ##      ##    ##  ##  ## ###          ## ###  ##  ## 
	##      ##  ##  ##  ##  ##  ##  ##  ##  ##  ##    ##      ##    ##  ##  ##  ##          ##  ##  ##  ## 
	##      ##  ##   ####   #####    ####    ####     ##    ######   ####   ##  ##           ####    ####  


## General info

This Docker project build a complete continuous integration environement. 
When it is up, you can **publish your developpement code** on GitHub and **your users will see application automatically updated**, some minutes after on an Internet Url    

     `DEVELOPPER -> github -> jenkins -> sonar -> nexus -> tomcat <- nginx <- serveo <- USER`

| Container | Local Port | Internal Port | Comments
| --------- | ---- | ---- | ---------------------------------------------------------------------------- |
| jenkins   | 8080 | 8080 | Orchestrator to build, control and deploy application |
| sonar     | 9000 | 9000 | Control the quality of application artifact |
| nexus     | 8081 | 8080 | Store the application artifact and keep history |
| tomcat    | 8083 | 8080 | Application Container |
| nginx     | 8084 | 80   | Web Server |
| serveo    | XXXX | XXXX | Expose Web Server on internet (http://xxxx.serveo.net) |

Note: Need also, a github application which contains the source of your application and jenkins/shell files to execute (see: pokebible application on the same github!)


## Setup

### Launch command

$ docker-compose up --force-recreate 

Note: All the data of each containers will be permanentally store in the '/volumes' repository


### At first Launch

* Connect to nexus Server
  - Create a repository to put the artefact (Ex: com.pokebible)
* Connect to sonar Server
  - Create a project (Ex:pokebible) 
* Connect to jenkins Server
  - Create a pipeline in Jenkins and link it to your jenkinsfile on your Github project 


### Other commands

* To restart serveo only (after a shutdown/sleep of your computer) 

	$ docker-compose restart serveo