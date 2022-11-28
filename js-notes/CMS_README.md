![unit_test](https://qnetgit.cms.gov/QMARS/qmars-case-management-api/workflows/unit_test/badge.svg)
# Case Management API
The Case Management API will manage cases and their status.

<!-- TOC -->

- [Case Management API](#case-management-api)
- [What is this?](#what-is-this)
- [Documentation](#documentation)
  - [Setup](#setup)
    - [application-local.properties](#application-localproperties)
    - [Docker](#docker)
    - [Native](#native)
  - [Using this application](#using-this-application)
  - [Component Diagram](docs/images/component-diagram.png) The diagram is located and can be updated in Lucidchart in Team Folders / QMARS / C4 Component Diagrams / Case Management API
- [Testing](#testing)
  - [Unit Tests](#unit-tests)
  - [Postman](#postman)
    - [Setup](#setup-1)
    - [Use](#use)
  - [Swagger UI](#swagger-ui)
- [Sources and Links](#sources-and-links)
- [Entity Relationship Diagram](#entity-relationship-diagram)

<!-- /TOC -->

# What is this? 

This is an API to manage cases. It will:
- add new cases
- update existing cases
- delete old cases
- provide information about cases

# Contributing
To learn more about contributing to this project, please refer to [the contributing guide](CONTRIBUTING.md). 

# Documentation
## Setup
To run this application you must have Java JDK 11 installed. To find out what version of Java you have run `java --version` from your terminal. We recommend [OpenJDK](https://openjdk.java.net/) if you need to download it. You can also use [JavaEnv](https://github.com/protocol7/javaenv) to stay up to date.

When first cloning this repo you'll need to grab the schema repo as a git submodule. To do that run:

1. run `git submodule init`
2. run `git submodule update`

Additionally you will need [Docker](https://www.docker.com/) installed. You can run this w/out Docker, but that is not recommended.

### .env

Certain environment variables will be required for this application to run properly. To set these locally copy the `.env.example` file in the project root and rename the copy to `.env`.

***Note: If you are doing local development with docker, the `POSTGRES_HOST` should match the name of the service within the docker-compose file (e.q. db)***

#### JSON Web Token

To populate the `JWT_SECRET` environment variable follow these steps:

1) Go to https://jwt.io/
2) Scroll down to the JWT creation section.
3) Confirm that the header has this value 
`{
   "alg": "HS256",
   "typ": "JWT"
}`.
4) Update the payload to have a subject of `test` and any value for `contactEmail`. For example:
`{
  "sub": "test",
  "contactEmail": "test"
}`.
5) Replace the text `your-256-bit-secret` with a base64 encoded secret. 
6) Check the checkbox for `secret base64 encoded`.
7) Copy the encoded JWT token to use for sending requests.
8) Set the value of `JWT_SECRET` to be the base64 encoded secret that was provided for the JWT.

## Using this application

You have two options for using this application. You can run it on your local machine. Some instructions will be provided for that option. The recommended method for running this application and coding against it is the Docker method.

If using Docker a `Makefile` and additional `docker-compose.live-reload.yaml` has been included to allow for live-reloading. For this to work you will need [IntelliJ](https://www.jetbrains.com/idea/) and you will need the *Save Actions* plugin and you will need to set the *Compile files* option to selected. Once your local development environment is ready you may run the application using the following `make` commands:

- `make start`: This will run the application in a regular docker way and not live-reload.
- `make start-live-reload`: This will run the application and live-reload certain code changes. ***Requires the Save Actions IntelliJ plugin***
- `make stop`: This will bring down your environment.
- `make stop-clean`: This will bring down your environment and purge the networks cleanly.
- `make win-start`: This will run the application in a regular docker way for Windows machines. ***If you encounter an issue using `make win-start` try using `make win-start-live-reload` instead***
- `make win-start-live-reload`: This will run the application and live-reload certain code changes for Windows machines. ***Requires the Save Actions IntelliJ plugin***

If using this Repo on Windows Machine, use the following steps: 

***Note: Running this application on Windows is no longer supported.***

- Download the following:
  1) [dos2unix](https://versaweb.dl.sourceforge.net/project/dos2unix/dos2unix/7.4.2/dos2unix-7.4.2-win64.zip) or search for dos2unix
  2) [make](https://managedway.dl.sourceforge.net/project/ezwinports/make-4.3-without-guile-w32-bin.zip) or search for make for windows without-guile-w32-bin
  3) [JDK11](https://download.java.net/openjdk/jdk11/ri/openjdk-11+28_windows-x64_bin.zip)
  4) Docker
- Extract the zip files into individual folders and name them accordingly
- Include the location path of the bin folder of dos2unix and make to the System Variable Path as Environmental Variable -   Example:(C:\doc2unix\bin) (C:\make-4.3\bin)
- Include JAVA_HOME as System Variable with JDK11 folder Location in your Windows Machine
- Once the Repo is opened in IntelliJ Choose File - Project Structure - Choose Project SDK as JDK11 and Project Language Level as 11 - Local variable syntax for lambda  parameters

Execution Steps: 
- Keep Docker up and running
- Open Terminal in Intellij and run the following commands  
  1)`dos2unix mvnw`  
  2)`mvnw clean`  
  3)`make win-start-live-reload` 
  
### Generated Classes

The application makes use of Hibernate JPA model classes. These classes are generated in the target\generated-sources directory. For an IDE to see these classes they must be included as part of the source files. To do this in Intellij right-click on the target\generated-sources directory and in the context menu click on Mark Directory as > Generated Sources Root. It may be necessary to compile the project first to generate the model classes.
  
### Docker

Docker is used to connect a database to the application layer. By running `docker-compose` you will be bringing up Postgres and setting up the schema.

To run the application you can do it one of two ways:

- `make start`: This will run the applicaiton in a regular docker way and not live-reload
- `make start-live-reload`: This will run the application and live-reload certain code changes. ***Requires the Save Actions IntelliJ plugin***
- `make win-start`: This will run the application in a regular docker way for Windows machines
- `make win-start-live-reload`: This will run the application and live-reload certain code changes for Windows machines. ***Requires the Save Actions IntelliJ plugin***

You can run the code without the documentation features by running `make start-lite` or `make win-start-lite`.

When entering the HostName for the database (within the second tab) use `pgsql-server`.

## Native

If instead you're not having success with Docker and would rather run the application on your native OS after running `./mvnw clean package` you need to start a database instance locally or use Docker for just that. Then, once your instance is started you must create the `QMARS` database. Run the `scripts/postgres/1-schema.sql` against that database to create the `case_management` schema.

Once the database is ready, and your JDBC connection values match your environment, you can run the application by running: 

- `./mvnw clean package`
- `java -jar -Dspring.profiles.active=local target/api-1.0.0-SNAPSHOT.jar`

FYI you will need your environment variables set for the packaging. 

### Easy local Postgres with Docker

You can use `localhost` for your db solution by installing Postgres locally yourself (if you choose this path then I trust you know what you're doing...). If you'd like to do so with Docker super easily you can run:

```shell
docker run --name postgres -e POSTGRES_PASSWORD=<your pw> -p 5432:5432 -d postgres
```

# Dev tools

There are two devtools included for extra convenience. PGAdmin and Redocly.

## pgAdmin

In order to connect to the DB through pgAdmin, once you have started the application with `make start`, you can navigate to [http://localhost:8081](http://localhost:8081) to open pgAdmin.
Once there, login using the credentials detailed in the `.env` file. Next you will need to setup the db connection by again using the values in the `.env` file. The instructions are as follows:
- add the PostgreSQL server running as a Docker container, click on Servers, and then go to Create > Server…
- in the General tab, type in your server Name (can be anything you want)
- go to the Connection tab and type in pgsql-server as Host name/address, 5432 as Port, postgres as Maintenance database, admin as Username, secret as Password and check Save password? checkbox
- click on Save

## redocly

Using redocly is much less involved than pgAdmin. To access redocly and the API spec, simply visit [http://localhost:8082](http://localhost:8082) in your browser after running `make start`.

## Component Diagram
![Alt text](docs/images/component-diagram.png)

# Testing

## Unit Tests

All you need to do to test this application is run one of the following commands:
- `make test`
- `./mvnw test`
- `make win-test` for Windows
- `mvnw test` for Windows

When committing code unit tests will run as part of the pre-commit hooks. Code coverage should always be at least 80%. You will not be able to commit your code if test coverage does not meet this requirement.

## Postman

### Setup

- Run `git submodule update --remote` to make sure you have the latest shared Postman files.
- Import the following files into Postman:
  - `docs/openapi/shared/QMARS-Local.postman_environment.json`
  - `docs/openapi/shared/keycloak.postman_collection.json`
  - `docs/openapi/case-management-api/case_management_api.postman_collection.json`
- Start the [Keycloak service](https://qnetgit.cms.gov/QMARS/qmars-access-request-api#keycloak)
- Start the [Case Management API](#using-this-application)

The `api-version` env variable determines if requests will hit the original (non-v2) endpoints or the v2 endpoints. 

Original endpoints:
- Set `api-version` to 1
- Set the `api-key` with the JWT you got from [this step](#json-web-token)

v2 endpoints:
- Set `api-version` to 2
- Submit one of the authentication requests from the Keycloak collection. This will result in the `token` variable being set to the Keycloak access token from the response.

You should now have access to the remaining requests. However, keep in mind that for the v2 endpoints, Livanta and Kepro API clients only have access to the appeals and modified-appeals endpoints, respectively.

### Use

The primary GET requests for some of the resources will set environment variables for that resource. For example, submitting a GET `/patient` request will set `patientId` and `patientName` to the id and name of the first patient in the response. Some requests depend on these environment variables, like the POST `/case` endpoint - This request requires `caseTypeOptionId`, `stageTypeOptionId`, `patientId`, and `practitionerId` all to be set with existing values.

### Swagger UI

Testing the API with Swagger UI is done similarly to using Postman.
- You must provide a valid token by clicking the 'Authorize' button at the top, pasting either generated JWT or Keycloak token (depending on api version) in the `bearerAuth` field, and click 'Authorize' again.
- The `stage` field must correspond to the API version you wish to use - for original, use `local` and for v2 endpoints use `local/v2`.

An endpoint can be tested by opening the section for that operation, clicking on the `Try it out` button, then clicking on the `Execute` button. 

## Docker container scans
To run any scan you must be logged into Dockerhub. To do so run `docker login`.
To run the scan you must build the image locally first and then:
- `docker build . qmarscmapi`
- `make scan`

# Sources and Links

For further reference, please consider the following sections:

* [Official Apache Maven documentation](https://maven.apache.org/guides/index.html)
* [Spring Boot Maven Plugin Reference Guide](https://docs.spring.io/spring-boot/docs/2.4.2/maven-plugin/reference/html/)
* [Create an OCI image](https://docs.spring.io/spring-boot/docs/2.4.2/maven-plugin/reference/html/#build-image)
* [New Relic](https://docs.spring.io/spring-boot/docs/2.4.2/reference/html/production-ready-features.html#production-ready-metrics-export-new-relic)

# Entity Relationship Diagram
![Alt text](docs/er-diagram.png)The actual diagram exists in Lucid in the Team Folder / QMARS / ER Diagrams folder and can be updated directly from there. Make sure to update the `docs/er-diagram.png` as well. If larger updates are required, the relationships can be re-generated using the `scripts/entity-relationships/generate_er_csv.sh` script. After running the script you can import the resulting `entity_relationships.csv` into Lucid using the 'Import Data' tool. Once imported, you will be provided with a new selection of tables to replace any of the updated ones.
