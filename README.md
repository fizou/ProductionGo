# ProductionGo

	#####   #####    ####   #####   ##  ##   ####   ######  ######   ####   ##  ##           ####    ####    `       
	##  ##  ##  ##  ##  ##  ##  ##  ##  ##  ##  ##    ##      ##    ##  ##  ### ##          ##      ##  ##   `      
	#####   #####   ##  ##  ##  ##  ##  ##  ##        ##      ##    ##  ##  ## ###          ## ###  ##  ##   `      
	##      ##  ##  ##  ##  ##  ##  ##  ##  ##  ##    ##      ##    ##  ##  ##  ##          ##  ##  ##  ##   `      
	##      ##  ##   ####   #####    ####    ####     ##    ######   ####   ##  ##           ####    ####    `      

## Table of contents

## General info

This Docker project build a complete continuous integration environement. 
When it is up, you can **publish your developpement code** on GitHub and **your users will see the result automatically**, some minutes after on an Internet via a www.domain.com Url  

         `USER -> serveo -> nginx -> tomcat <- nexus <- sonar <- jenkins <- github <- DEVELOPPER`
| -------------   | ---| ---------------------------------------------------------------------------- |
| Execute jenkins |8080| Orchestrator to build, control and deploy war |
| Execute sonar   |9000| Control the quality of war artifact |
| Execute nexus   |8081| Store the war artifact and keep history |
| Execute tomcat  |8083| Application Container |
| Execute nginx   |8084| Web Server |
| Execute serveo  |XXXX| Expose nginx server on internet (http://xxxx.serveo.net) |

Need also, a github application: Source of application with jenkins/shell files to execute (see: pokebible on the same github!)
 
## Setup

$ docker-compose up --force-recreate 

## Other information

## Tips
* Create a pipeline in Jenkins and link it to your jenkinsfile in your GIThub Project 
* Create a reporsitory in sonar to put the artefact 

## Commands

docker-compose restart serveo
