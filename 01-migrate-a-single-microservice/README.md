# Migrating Single Microservice

While Spring Cloud is steadily growing in popularity, there are many Spring Boot Microservices that do not yet leverage Spring Cloud technologies.

Because every Spring Microservice can be a Spring Cloud Microservice, we shall first Migrate one such Microservice to Azure Spring Cloud.

## Configuring a Spring Microservice

Attached to this section is a single containerized Spring Microservice. We also include a containerized Mongo DB deployment and a `docker-compose` orchestration to make the two work together.

* Open `auth-service/src/main/resources/application.yml`.

This file provides the configuration for the microservice. This configuration often includes connection information for the backing database. These settings could be externalized to another configuration file or to environment variables, but ultimately, with ordinary Spring microservices, it is the responsibility of the developer/deployer to provide this configuration.

When we deploy this Microservice to Azure Spring Cloud, the platform will inject the database information automatically. It will also take care of exposing the web interface of the service. So go ahead and delete the `application.yml` file. Where we are going, we don't need it.

## Creating an Azure Spring Cloud instance
