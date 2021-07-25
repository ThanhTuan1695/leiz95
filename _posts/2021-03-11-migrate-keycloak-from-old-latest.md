---
layout: post
title: Migrate H2 database from oldest to latest version for docker container on Keycloak.
subtitle: Keycloak with docker container
thumbnail-img: /assets/img/oauthandoidc.png
tags: [research, OIDC, 0Auth, 0Auth2, Keycloak, docker]
# comments: true
---

If you have worked with Keycloak, you might know that if you are not specific an database type, the Keycloak will automatically build up with H2 database. During the time to time. when the data in database is grow up and You would like bring all the data from H2 to another database such as Postgresql, MySql,... But we are only have the document migrate for the lib file. It's not user for docker. I also searched for the article talking about it but not found anything. That's why i created this articel to do step by step about it. 


 In case, you had run the Keycloak with H2 database on docker enviroment. You want to migrate your whole Keycloak database from one environment to another or migrate to a different database (for example from H2 to Postgresql). Export and import is triggered at server boot time and its parameters are passed in via Java system properties. It is important to note that because import and export happens at server startup, no other actions should be taken on the server or the database while this happens. You can do as the below:



# Step by step

##  Export all data from Keycloak on VM

Access to Keycloak container via docker exec cmd and change the work directory to /opt/jboss/keycloak. Then, run the command to export all data including realm and users from keycloak.

```bat

    bin/standalone.sh -Dkeycloak.migration.action=export -Dkeycloak.migration.provider=singleFile -Dkeycloak.migration.file=keycloak-export.json -Djboss.http.port=8888 -Djboss.https.port=9999 -Djboss.management.http.port=7777

```

After exporting is done. The keycloak directory will appear keycloak-export.json file. Then copy that file from Docker container to stored place. Stop Keycloak container.

## Run Keycloak Latest and Another DB via docker. 

I have prepared the docker-compose file which is latest Keycloak and Postgresql.

```yaml
version: "3"
services:
  postgres:
      image: postgres
      volumes:
        - ./data:/var/lib/postgresql/data #should be created data folder in VM
      environment:
        POSTGRES_DB: keycloak
        POSTGRES_USER: keycloak
        POSTGRES_PASSWORD: password
      ports:
        - 5432:5432
  keycloak:
      image: jboss/keycloak:latest
      environment:
        DB_VENDOR: POSTGRES
        DB_ADDR: postgres
        DB_DATABASE: keycloak
        DB_USER: keycloak
        DB_SCHEMA: public
        DB_PASSWORD: password
        KEYCLOAK_USER: admin
        KEYCLOAK_PASSWORD: admin
        #Uncomment the line below if you want to specify JDBC parameters. The parameter below is just an example, and it shouldn't be used in production without knowledge. It is highly recommended that you read the PostgreSQL JDBC driver documentation in order to use it.
        #JDBC_PARAMS: "ssl=true"
      ports:
        - 8080:8080 # change port if any
      depends_on:
        - postgres

```
You can run command `docker-compose up --build` to building up the docker container.

## Import the exported data to latest Keycloak

After the keycloak latest and postgres running successfully. 

Access to Latest keycloak container, Copy the keycloak-export.json file from stored place into /opt/jboss/keycloak again. Then, run the cmd to import:

```
# copy keycloak-export.json from vm to container
docker cp ./keycloak-export.json ID_CONTAINER:/opt/jboss/keycloak/
# run command to import data
bin/standalone.sh -Dkeycloak.migration.action=import -Dkeycloak.migration.provider=singleFile -Dkeycloak.migration.file=keycloak-export.json -Dkeycloak.migration.strategy=OVERWRITE_EXISTING -Djboss.http.port=8888 -Djboss.https.port=9999 -Djboss.management.http.port=7777

```

Wait for all press finish. Go to admin console to view result.

You can restart the keycloak container.

## Reference:

https://www.keycloak.org/docs/latest/server_admin/#_export_import