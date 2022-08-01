# atomic-fruit-service project- quarkus-remote demo

The purpose of this demo is to showcase Quarkus' remote dev capabilities
and the power of Quarkus.
This project uses Quarkus, the Supersonic Subatomic Java Framework.

If you want to learn more about Quarkus, please visit its website: https://quarkus.io/ .

##Openshift Setup

First create a namespace for yourself to use 
```
export PROJECT_NAME=<insert project name>
oc new-project ${PROJECT_NAME}
```
next set up a postgres db with a user,password, and database
```
oc new-app -e POSTGRESQL_USER=ingmar -e POSTGRESQL_PASSWORD=secret -e POSTGRESQL_DATABASE=FRUITSDB \
  centos/postgresql-10-centos7 --as-deployment-config=true --name=postgresql-db -n ${PROJECT_NAME}
```
add labels to the resources 
```
oc label dc/postgresql-db app.kubernetes.io/part-of=fruit-service-app -n ${PROJECT_NAME} && \
  oc label dc/postgresql-db app.openshift.io/runtime=postgresql --overwrite=true -n ${PROJECT_NAME}
```
## Deploying the code to openshift
```
gradle build -Dquarkus.kubernetes.deploy=true -x test -Dquarkus.profile=prod-postgresql -Dquarkus.container-image.build=true -Dquarkus.openshift.env-vars.quarkus-launch-devmode.value=true -Dquarkus.openshift.env-vars.quarkus-profile.value=prod-postgresql
```
Verify that your route was created, then head into src/main/resources/application.properties
to uncomment the openshift route and paste the one that was created inside it.

## Connecting to remote dev
```
./gradlew quarkusRemoteDev -Dquarkus.profile=prod-postgresql
```
you should see 'connected to remote server' and once that's done your ready to start coding with live updates to your cluster.


