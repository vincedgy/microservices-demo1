Microservices demo
=====================

* Author : V. Dagoury
* Creation : 2017-04-23
* System used : Mac OSx

# Presentation

The application developped for this DEMO is a WebApp that use microservices with SpringBoot.
See Archi.pdf for an overview of the architecture.
We don't use any service discovery/management (like Zookeeper, Eureka Zuul) or any circuit breackers (like Hystrix or Turbine). Obviously I recommand to use them on a production environement and to refactor the application accordingly in this case. This demo is more a SpringBoot demo than e-anything else.


The applicaiton on Client side could be developped with any framework needed, even serves a MobileApp, but in this short demo we'll use Angular2 for a matter of speed.

The "Client" web application (written with AngularCLI) uses REST/JSON and HTTP to 3 misroservices : 
- a messaging web microservice named "WebMess" using also WebSocket,
"WebMess" serves JSON/REST & WebSocket payloads from RabbitMQ server queues. Admin push messages by CLI.
- a business web microservice named "WebBiz" (using SpringMVC) with a session management (store in H2 in memory db)
- BizServer uses JPA/Hibernate to serve data from an SQL database (MySQL or Postgresql) which contain business assets

# SpringBoot installation

SpringBoot using Maven or Gradle with Internet access is simple as getting a pox.xml and retrieve dependencies by building the project provided by the website 
http://start.spring.io/.
By doing so you will start up your developpement in minutes.

Download (zip file) each project after completing the form [here](http://start.spring.io/)
1. Open [http://start.spring.io/](http://start.spring.io/)
2. Complete Group, Artifact with respectivaly the package domain name 'net.demo' and Artifact with the name of the microservice
3. For "WebMess" choose following options : Web, Actuator, DevTools, AMQP
4. For "BizServer" choose following options : Web, Actuator, DevTools, JPA, Rest, MySQL drivers, Postgresql drivers
5. For "WebBiz" choose following options : Web, Actuator, DevTools, JPA, Rest, H2, Session, Stormpath (session, security invluded), Rest Repositories, Lombok, Res Repository HAL Browser, REST Docs, HATEOAS

The start.spring.io tools give us a zip file which contains pom.xml and script for developping our application.


# MariaDB driver as an alternative

```
 <dependency>
    <groupId>org.mariadb.jdbc</groupId>
    <artifactId>mariadb-java-client</artifactId>
    <version>1.5.7</version>
 </dependency>
```

# Initialize each projects

In a new directory with the name of the project, unzip the zip file from start.spring.io for the project and launch the following command :

```
$ ./mvnw spring-boot:run
```

Then launch your IDE within the directory

# MQ-Rabbit installation and configuration

This is a set of command for installation and configuration of the queue we'll use 
```
brew install rabbitmq
brew services start rabbitmq

curl -i -u guest:guest http://localhost:15672/api/overview

# Create an alias
alias rabmq=/usr/local/opt/rabbitmq/sbin/rabbitmqadmin

rabmq declare exchange name=my-new-exchange type=fanout
ramq declare exchange name=messages type=fanout
```

## Developping WebMess

WebMess consumes MQ Queues with the help of AMQP (which use exchange and queues)

https://spring.io/guides/gs/messaging-rabbitmq/