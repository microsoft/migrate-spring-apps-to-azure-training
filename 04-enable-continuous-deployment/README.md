# Enable Continuous Deployment

A key virtue of Microservices is the ability to continuously and independently test and deploy the changes to each one. In this section, we will set up pipelines to build and deploy the Microservices we migrated in Section 2.

## Create an Azure DevOps Project

Create a new Azure DevOps project. Commit the contents of the "Piggy Metrics" directory to the root of the Git repository. The resulting repository should look like this:

![Repository after initial commit](media/01-initial-commit.png)

## Create a build pipeline with Classic UI

Let's first create a build pipeline for `auth-service` using the classic UI, as it is often the most intuitive experience for new customers.

In Azure DevOps, navigate to "Builds" under "Pipelines", and click "New Pipeline". Click on the "Use the classic editor" link at the bottom.

Select the repository you created in the previous step as the source, and on the "Select a Tempalte" pane, click "Maven".

![Select the Maven build template](media/02-select-maven-template.png)

The build pipeline will be created. Click on the "Pipeline" header at the top.

Change the pipeline name to "auth-service-build". Under parameters, change "Maven POM file" to `auth-service/pom.xml`

![Maven build pipeline - classic UI](media/03-build-pipeline-classic.png)

Click "Save and Queue". The build should now run and complete successfully.

## Create a Release pipeline for Auth Service


## Optimizations

1. To make builds faster and more reliable, [Azure Artifacts Feeds can be configured](https://docs.microsoft.com/en-us/azure/devops/artifacts/maven/upstream-sources?view=azure-devops) to cache 3rd party dependencies instead of fetching them from Maven Central with every build.
