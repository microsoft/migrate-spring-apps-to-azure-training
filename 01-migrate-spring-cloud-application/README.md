# Migrate a Spring Cloud Application

__This guide is part of the [Azure Spring Cloud Migration Lab](../README.md)__

Provisioning an Azure Spring Cloud instance, configuring diagnostic settings and distributed tracing, building and deploying the application.

---

In this section, we're going to take a pre-existing Spring Cloud application, consisting of four microservices, and migrate it to Azure Spring Cloud in its entirety.

We will use this migrated application in the subsequent section to demonstrate the monitoring, scaling, and tracing capabilities of Azure Spring Cloud.

## Logging into Azure

Ensure your Azure CLI is logged into your Azure subscription.

💡If you intend to use the Docker container as recommended, do this inside the container bash session.

az login # Sign into an azure account
az account show # See the currently signed-in account.

Ensure your default subscription is the one you intend to use for this lab, and if not - set the subscription via az account set --subscription <SUBSCRIPTION_ID>

## Creating an Azure Spring Cloud instance

For expediency, let's create the Azure Spring Cloud instance from Azure CLI.

First, you will need to come up with a name for your Azure Spring Cloud instance.

- __The name must be unique among all Azure Spring Cloud Instances across all of Azure__. Consider using your username as part of the name.
- The name can contain only lowercase letters, numbers and hyphens. The first character must be a letter. The last character must be a letter or number. The value must be between 4 and 32 characters long.

To save minimize, set the variable `RESOURCE_GROUP_NAME` to the name of the resource group created in the previous section. Set the variable `SPRING_CLOUD_NAME` to the name of the Azure Spring Cloud instance to be created:

>🛑Be sure to substitute your own values for `RESOURCE_GROUP_NAME` and `SPRING_CLOUD_NAME` as described above. __`SPRING_CLOUD_NAME` must be globally unique.__

```bash
RESOURCE_GROUP_NAME=spring-cloud-lab
SPRING_CLOUD_NAME=azure-spring-cloud-lab
```

With these variables set, we can now create the Azure Spring Cloud Instance:

```bash
az spring-cloud create \
    -g "$RESOURCE_GROUP_NAME" \
    -n "$SPRING_CLOUD_NAME"
```

For the remainder of this workshop, we will be running Azure CLI commands referencing the same resource group and Azure Spring Cloud instance. So let's set them as defaults, so we don't have to specify them again:

```bash
az configure --defaults group=${RESOURCE_GROUP_NAME}
az configure --defaults spring-cloud=${SPRING_CLOUD_NAME}
```

## Configure Observability and Troubleshooting Features

Before we deploy microservices to the newly-created Azure Spring Cloud instance, we should enable log aggregation and distributed tracing. We do this upfront for two reasons:

1. Configuration changes take time to apply, so the sooner we register the changes, the sooner we can make use of them.

1. If one of the microservices fails to come up, perhaps due to a misconfiguration, being able to query its output in Log Analytics will help us troubleshoot it.

### Configure Distributed Tracing

Distributed tracing allows you to observe interaction among microservices and diagnose issues. We will see this feature in action in Section 5, but because its configuration requires some time to be applied, let's enable it now:

- Go to the [the Azure portal](https://portal.azure.com/).
- Go to the Azure Spring Cloud instance and click on "Distributed Tracing" (under Monitoring).
  - Click on "Edit Settings" and select the App Insights workspace created in Section 00 (named `sclab-ai-<unique string>`).
  - Once the Application Insights configuration is saved, click "Enable" at the top of the "Distributed Tracing" pane.

The configuration settings for distributed tracing take some time to apply, so we'll play with log aggregation next and come back to distributed tracing later.

### Configure log aggregation

There are actually three ways to access your application's logs: [Azure Storage](https://docs.microsoft.com/en-us/azure/storage/common/storage-introduction/), [Azure Events Hub](https://docs.microsoft.com/en-us/azure/event-hubs/), and [Log Analytics](https://docs.microsoft.com/en-us/azure/azure-monitor/log-query/get-started-portal). We will focus here on Log Analytics as it's the most common one, and as it's integrated into Azure Spring Cloud.

[Log Analytics](https://docs.microsoft.com/en-us/azure/azure-monitor/log-query/get-started-portal/) is part of [Azure Monitor](https://azure.microsoft.com/en-us/services/monitor/), which is well-integrated into Azure Spring Cloud, and which we will also use for metrics monitoring.

Having completed the setup in Section 00, you should have a Log Analytics workspace named `sclab-la-<unique string>`. We must now configure our Azure Spring Cloud  instance to send its data to this workspace.

- Go to the "Overview" page of your Azure Spring Cloud instance, and select "Diagnostic settings" in the "Monitoring" section of the navigation pane.
- Click on "Add diagnostic setting" and configure your instance to send all its logs to the Log analytics workspace that we just created.
- Fill in the values as shown here and click "Save".

![Send logs to the log analytics workspace](media/04-send-logs-to-log-analytics-workspace.png)

## Deploying the configuration server

In an individual Spring Boot microservice, the configuration is typically provided in an accompanying file called `application.yml` or `application.properties`. But if we were to deploy multiple Spring Boot microservices in this fashion, settings would have to be duplicated across multiple microservices.

Spring Cloud simplifies configuration management by centralizing configuration in a single configuration server. Azure Spring Cloud extends this functionality by provisioning and managing a Config Server directly from a git repository containing configuration.

> 💡 To save time, we host a public repository with the configuration for Piggy Metrics at `https://github.com/yevster/piggymetrics-config.git`. However, in real-world a private repository would be used, with the user name and access token provided to Azure Spring Cloud.

Run the following command to set the configuration repository:

```bash
az spring-cloud config-server git set --uri https://github.com/yevster/piggymetrics-config.git -n $SPRING_CLOUD_NAME
```

## Creating the apps

In the previous section, you provisioned an App, to host a single microservice, in the Azure Portal. We will now need to provision five apps: four for each of the microservices we plan to migrate, plus one for the gateway, which will host the UI and expose our microservices to the public. For expediency, we will perform these tasks from the Azure CLI:

```bash
# Create an app for the gateway + UI
az spring-cloud app create --name gateway --instance-count 1 --is-public true &

# Create an app for each of the microservices
az spring-cloud app create --name account-service --instance-count 1 &
az spring-cloud app create --name auth-service --instance-count 1 &
az spring-cloud app create --name statistics-service --instance-count 1 &
az spring-cloud app create --name notification-service --instance-count 1 &
```

While waiting for the creation to complete, you can move on to the next section, "Building the apps". Do not, however, move beyond it until all the commands above have finished executing (you can see the active tasks with the `jobs` command).

## Building the apps

### Get PiggyMetrics

Now that we have provisioned and configured our Azure Spring Cloud instance and the apps on it, we need some code to deploy. Run the following in your container:

```bash
git clone https://github.com/microsoft/migrate-spring-apps-to-azure-training 
mv migrate-spring-apps-to-azure-training/01-migrate-spring-cloud-application/piggymetrics .
rm -fR migrate-spring-apps-to-azure-training
```

### Build PiggyMetrics

Next, we need to build the microservices comprising the PiggyMetrics application into deployable JAR files. Run:

```bash
cd piggymetrics
mvn clean package -DskipTests -Denv=cloud
```

## Create the service binding for CosmosDB

Prior to the migration, the application connected to MongoDB at an explicitly provided endpoint with explicitly provided redentials. In Azure Spring Cloud, we can instead create flexible service bindings that will inject that configuration.

So in place of MongoDB, we will inject CosmosDB configuration into each of the apps we just created. For expediency, we will use Azure CLI to accomplish this task.

```bash
COSMOS_ACCOUNT_ID=$(az cosmosdb list --query '[].id' -o tsv)

az spring-cloud app binding cosmos add --api-type mongo --app account-service -n cosmos --resource-id "${COSMOS_ACCOUNT_ID}" --database-name account-db
az spring-cloud app binding cosmos add --api-type mongo --app auth-service -n cosmos --resource-id "${COSMOS_ACCOUNT_ID}" --database-name auth-db
az spring-cloud app binding cosmos add --api-type mongo --app notification-service -n cosmos --resource-id "${COSMOS_ACCOUNT_ID}" --database-name notification-db
az spring-cloud app binding cosmos add --api-type mongo --app statistics-service -n cosmos --resource-id "${COSMOS_ACCOUNT_ID}" --database-name statistics-db

```

## Deploying the apps

Some of the migrated applications require an RabbitMQ broker. We have deployed one in an Azure Containber Instance in the ARM template in Section 0. We need to set some environment variables and populate them in the deployed microservices:

```bash
# Obtain the password from the RABBIT MQ Container Instance. (This should not be possible in a production deployment!)
RABBITMQ_PASSWORD=$(az container list --query "[].containers[].environmentVariables[?name=='RABBITMQ_DEFAULT_PASS'].value" -o tsv)
RABBITMQ_HOST=$(az container list --query '[0].ipAddress.fqdn' -o tsv)
RABBITMQ_PORT=5672
RABBITMQ_USERNAME=$(az container list --query "[].containers[].environmentVariables[?name=='RABBITMQ_DEFAULT_USER'].value" -o tsv)

```

Deploy the webapps using the `az spring cloud deploy` command.

```bash

# Deploy gateway app
az spring-cloud app deploy --name gateway \
    --jar-path gateway/target/gateway.jar \

# Deploy auth-service app
az spring-cloud app deploy --name auth-service \
    --jar-path auth-service/target/auth-service.jar

# Deploy account-service app
az spring-cloud app deploy --name account-service \
    --jar-path account-service/target/account-service.jar \
    --env RABBITMQ_HOST=${RABBITMQ_HOST} \
          RABBITMQ_PORT=${RABBITMQ_PORT} \
          RABBITMQ_USERNAME=${RABBITMQ_USERNAME} \
          RABBITMQ_PASSWORD=${RABBITMQ_PASSWORD}

# Deploy statistics-service app
az spring-cloud app deploy --name statistics-service \
    --jar-path statistics-service/target/statistics-service.jar \
    --env RABBITMQ_HOST=${RABBITMQ_HOST} \
          RABBITMQ_PORT=${RABBITMQ_PORT} \
          RABBITMQ_USERNAME=${RABBITMQ_USERNAME} \
          RABBITMQ_PASSWORD=${RABBITMQ_PASSWORD}

# Deploy notification-service app
az spring-cloud app deploy --name notification-service \
    --jar-path notification-service/target/notification-service.jar \
    --env RABBITMQ_HOST=${RABBITMQ_HOST} \
          RABBITMQ_PORT=${RABBITMQ_PORT} \
          RABBITMQ_USERNAME=${RABBITMQ_USERNAME} \
          RABBITMQ_PASSWORD=${RABBITMQ_PASSWORD}

```

While waiting for the deployments to complete, this is a good time to navigate into the Azure Spring Cloud instance in Azure Portal. Click on "Apps" to see the status. Eventually, all Apps should have the status `Running`.

![All apps with status "Running"](media/02-all-apps-running.png)

Once all apps have the status `Running`, click on the Gateway app. In the PiggyMetrics application, this app also contains the UI. So copy the value in the `URL` field on `gateway`'s app page, and paste it into a browser.

You should see the front page of the PiggyMetrics app. Click "Create new account", and once you've created an account, play around with the application for a couple of minutes to generate some log and traffic data. Then, proceed to the next section.

![PiggyMetrics front page](media/03-piggymetrics-front-page.png)

---

⬅️ Previous section: [00 - Set Up Your Environment](../00-setup-your-environment/README.md)

➡️ Next section: [02 - Troubleshooting](../02-troubleshooting/README.md)