# Docker_Compose

1.	Create a folder ‘Docker-Compose’ and open it in vscode
2.	Install docker plugin in vscode
3.	Create a docker-compose.yml file under the folder and create the yaml file-
                    services:
                      web:
                        image: nginx
                      rcache:
                        image: redis:latest
                      db:
                        image: mysql:latest
4.	Also open powershell as admin and get into the docker-compose folder and check for the version
5.	Right click in vscode and select compose-up to create and run the container for the service or type 'docker compose up' or 'docker compose up –d'  in cli.
6.	The service is started now and we can check it in the docker server
                      docker images
                      docker ps
                      docker ps –a
                      docker network ls
                      docker volume ls
                      docker compose config
7.	Now to stop and remove the service, type  'docker compose down'
8.	To remove volume, type 'docker compose down --volumes'
9.	To use environment variable in docker-compose.yml file-
Create a file under the same folder ‘.env’ and type-
                      TAG= latest
                       //docker-compose.yml
                      services:
                        web:
                          image: nginx
                        database:
                          image: redis:${TAG} 
10.	Type 'docker compose up' or 'docker compose up –d'  in cli  
11.	SETTING ENVIRONMENT VARIABLE/ATTRIBUTE IN SERVICE CONTAINER

   	                    //.env
                        TAG= latest
                        //docker-compose.yml
                        services:
                          web:
                            image: nginx
                          cache:
                            image: redis:${TAG} 
                          db:
                            image: mysql
                            environment:
                             - MYSQL_ROOT_PASSWORD=root      OR
                             # MYSQL_ROOT_PASSWORD: root
13.	USE ENVIRONMENT FILE IN SERVICE CONTAINER

   	                      //.env
                          TAG= latest
                          
                          //mysqlconfig.env
                           		MYSQL_ROOT_PASSWORD=root      
                          	//docker-compose.yml
                          services:
                            web:
                              image: nginx
                            cache:
                              image: redis:${TAG} 
                            db:
                              image: mysql
                              env_file:
                                - mysqlconfig.env

15.	SET PROFILE NAME FOR SERVICE //we specify when we want to run a specific image

   	                    //.env
                        TAG= latest
                        //mysqlconfig.env
                         		MYSQL_ROOT_PASSWORD=root      
                        	//docker-compose.yml
                        
                        services:
                          web:
                            image: nginx
                          cache:
                            image: redis:${TAG} 
                            profiles:
                              - rediscache
                          db:
                            image: mysql
                            env_file:
                              - mysqlconfig.env
On typing 'docker compose up –d' , this code will only create and start the web and db servers, for which no profile is set.  Inorder to create and run the redis cache profile, we type the command-
'docker compose –profile rediscache up –d'

to delete the service with the profile, type ---
'docker compose –profile rediscache down –volumes'

14.	TO EXPOSE PORTS 
                    // if you are hitting the port 8000 on your host machine, you are hitting on the port 80 of nginx

   	               //.env
                    TAG= latest

   	                //mysqlconfig.env
                     		MYSQL_ROOT_PASSWORD=root      

   	              //docker-compose.yml
                    services:
                      web:
                        image: nginx
                        ports:
                         - 8000:80
                      cache:
                        image: redis:${TAG} 
                        profiles:
                          - rediscache
                      db:
                        image: mysql
                        env_file:
                          - mysqlconfig.env
You can type localhost:8000 in your browser and check whether your nginx server is up and running

15.	SET CUSTOM NETWORK AND NETWORK DRIVERS


   	            //.env
                TAG= latest
   	
                //mysqlconfig.env
                 		MYSQL_ROOT_PASSWORD=root
   	  
            //docker-compose.yml   
                services:
                  web:
                    image: nginx
                    ports:
                      - 7000:80
                    networks:
                      - mynetwork
                  cache:
                    image: redis:${TAG} 
                     #profiles:
                       #- rediscache
                  db:
                    image: mysql
                    #environment:
                     #- MYSQL_ROOT_PASSWORD=root
                     # MYSQL_ROOT_PASSWORD: root
                    networks:
                      - mynetwork
                    env_file:
                      - mysqlconfig.env
                
                networks:
                  mynetwork:
                    driver: bridge

17.	DEPENDS ON: SET ORDER OF CONATAINER CREATION AND REMOVAL

              //.env
              TAG= latest
              
              //mysqlconfig.env
               		MYSQL_ROOT_PASSWORD=root     
                 
              //docker-compose.yml
              services:
                web:
                  image: nginx
                  ports:
                    - 7000:80
                  networks:
                    - mynetwork
                  depends_on:
                    - db
                    - cache
                    
                cache:
                  image: redis:${TAG} 
                   #profiles:
                     #- rediscache
                  networks:
                    - mynetwork
              
                db:
                  image: mysql
                  #environment:
                   #- MYSQL_ROOT_PASSWORD=root
                   # MYSQL_ROOT_PASSWORD: root
                  networks:
                    - mynetwork
                  env_file:
                    - mysqlconfig.env
              
              networks:
                mynetwork:
                  driver: bridge

17.	MULTIPLE COMPOSE FILES
Sometimes we may need to create multiple compose files while working in different environments.

Suppose the current compose file is for production. Now I need a separate file for development

Let us create a custom compose file with the name docker-compose.dev.yml
                      
                      services:
                        web:
                          image: nginx
                          ports:
                            - 7000:80
                        db:
                          image: mysql
                          env_file:
                            - mysqlconfig.env

Now we run it as---
'docker compose –f docker-compose.dev.yml up –d'
and stop and remove it using-
'docker compose –f docker-compose.dev.yml down --volumes'
18.	USE CUSTOM IMAGE DOCKERFILE WITHIN COMPOSE FILE

i.	Create a new folder ‘docker-compose-1’, open vscode from there and create a dockerfile.

                  #Use the ubuntu base image
                  FROM ubuntu:22.04
                  
                  #Set the working directory inside the container 
                  WORKDIR /app

ii.	Create a docker-compose.yml file

                  services:
                    app:
                      build: 
                        dockerfile: Dockerfile
                    web:
                      image: nginx
                  
                  
                    db:
                      image: mysql
                      environment:
                       - MYSQL_ROOT_PASSWORD=root

19.	EXECUTING COMMAND INSIDE SERVICE

If you want to get into the mysql database using the password, type-
docker compose exec db mysql -u root -p
