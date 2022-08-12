
A 3 tier application can be deployed in multilple ways. For simplicity, I am planning to use docker containers to build the same.

Three Tier architecture contains below components,
1. Frontied tier: It will host the web application
2. Middle tier: This will host the API
3. Database tier: It will host the database.

A web application that will display a list of movies and the hero's in a plain old html table. This applications is going to get this data from a rest api which in turn will fetch it from a postgresql database.

Database Tier:

To create table structure,

create table movie_hero (
  movie text,
  hero text
);

Middle Tier:

In Middle tier, i am using simple express framwork which is readily available. It's a simple app listening on 33001. I am using express and pg packages. I am simply fetching data from the movie_hero table and sending all the rows as response when we get a GET request on the /data route. The connection string is being picked from the environment variable CONNECTION_STRING. It is of the format postgres://username:password@host/dbname.

We are building this in layers. We are basing our image from a docker alpine node image. We are then setting /usr/src/app as the working directory for the next two commands. We issue COPY command to copy package.json and package-lock.json to the working directory and then do a npm install. This will install all the dependencies.  Then we copy the directory contents to the image. We expose the 33001 port and then do a node index.js command using the docker CMD command.

Frontend Tier:

App is calling the REST api we created earlier using request package and displaying the data as a table. The api url is taken from a environment variable. The Dockerfile for this app is exactly the same from the middle tier but for the port. We are using the port 3000.


We are going to run these two containers and a postgresql database container using docker-compose. Note that docker-compose is a separate tool that needs to be installed separately. This tool works with docker to orchestrate containers and services.

The code available in github contains below folder structure:

|
+---frontend
|     +--index.js
|     +--package.json
|     +--package-json.lock
|     +--Dockerfile
+---backend
|    +--index.js
|    +--package.json
|    +--package-json.lock
|    +--Dockerfile
+---init_sql_scripts
|     +--init.sql
+--.dockerignore
+--docker-compose.yml

frontend: has the frontend code and its Dockerfile.
backend: has the api code and its Dockerfile
init_sql_scripts: has the sql scripts to populate the database.
.dockerignore: similar to .gitignore, has entries that the docker will ignore when creating the images.


To test the package, we can below commands 

To build : docker-compose up --build

To see the list of containers: docker ps

To test the application, access below link from browser or do a curl test from machine:

http://localhost:3300

or 
curl http://localhost:3300

if you try on 33001 it won't work as we haven't given access from frontend to 33001 and it is dedicated from middleware tier to database tier.




