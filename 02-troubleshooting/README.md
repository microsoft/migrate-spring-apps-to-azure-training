# Troubleshooting

Having migrated the application code to Azure Spring Cloud, we must now make the application easy to operate and troubleshoot.

## Configure Distributed Tracing

Distributed tracing allows you to observe interaction among microservices and diagnose issues. We will see this feature in action in Section 5, but because its configuration requires some time to be applied, let's enable it now:

- Go to the [the Azure portal](https://portal.azure.com/).
- Go to the Azure Spring Cloud instance and click on "Distributed Tracing" (under Monitoring).
  - Click on "Edit Settings" and select the App Insights workspace created in Section 00 (named `sclab-ai-<unique string>`).
  - Once the Application Insights configuration is saved, click "Enable" at the top of the "Distributed Tracing" pane.

The confriguration settings for distirbuted tracing take some time to apply, so we'll play with log aggregation next and come back to distributed tracing later.

## Configure log aggregation

There are actually three ways to access your application's logs: [Azure Storage](https://docs.microsoft.com/en-us/azure/storage/common/storage-introduction/), [Azure Events Hub](https://docs.microsoft.com/en-us/azure/event-hubs/), and [Log Analytics](https://docs.microsoft.com/en-us/azure/azure-monitor/log-query/get-started-portal). We will focus here on Log Analytics as it's the most common one, and as it's integrated into Azure Spring Cloud.

[Log Analytics](https://docs.microsoft.com/en-us/azure/azure-monitor/log-query/get-started-portal/) is part of [Azure Monitor](https://azure.microsoft.com/en-us/services/monitor/), which is well-integrated into Azure Spring Cloud, and which we will also use for metrics monitoring.

Having completed the setup in Section 00, you should have a Log Analytics workspace named `sclab-la-<unique string>`. We must now configure our Azure Spring Cloud  instance to send its data to this workspace.

- Go to the "Overview" page of your Azure Spring Cloud instance, and select "Diagnostic settings" in the "Monitoring" section of the navigation pane.
- Click on "Add diagnostic setting" and configure your instance to send all its logs to the Log analytics workspace that we just created.
- Fill in the values as shown here and click "Save".

![Send logs to the log analytics workspace](media/01-send-logs-to-log-analytics-workspace.png)

## Query application logs

Logs are now available in the "Logs" link in the "Monitoring" section in the navigation pane for your Azure Spring Cloud instance.  Click on "Logs". This is a shortcut to the Log Analytics workspace that was created earlier. If a tutorial appears, feel free to skip it for now.

This workspace allows to do queries on the aggregated logs, the most common one being to get the latest log from a specific application:

__Important:__ Spring Boot applications logs have a dedicated `AppPlatformLogsforSpring` type.

As we called the application in the [previous guide](../02-build-a-simple-spring-boot-microservice/README.md) "simple-microservice", here is how to get its 50 most recent logs of the `AppPlatformLogsforSpring` type for this application.  Insert this text in the text area that states "Type your queries here or click on of the example queries to start".  Click the text of the query, then click "Run".

```
AppPlatformLogsforSpring
| where AppName == "simple-microservice"
| limit 50
```

![Query logs](media/02-logs-query.png)