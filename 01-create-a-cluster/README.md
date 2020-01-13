# 01 - Create a cluster

__This guide is part of the [Azure Spring Cloud training](../README.md)__

Basics on creating a cluster and configuring the CLI to work efficiently.

---

## Install the Azure Spring Cloud CLI extension

__This is temporary, and will not be necessary when the service is released__

```bash
az extension add --name spring-cloud
```

## Create an Azure Spring Cloud instance

- [Click here](https://portal.azure.com/?WT.mc_id=azurespringcloud-github-judubois&microsoft_azure_marketplace_ItemHideKey=AppPlatformExtension#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/spring%20cloud) to access the cluster creation page.

![Cluster creation](media/01-create-azure-spring-cloud.png)

- Click on "Azure Spring Cloud" and then on "Create".
- Select your subscription, resource group name, name of the service and location.
- Once everything is validated, the cluster can be created.

![Cluster configuration](media/02-creation-details.png)

Creating the cluster will take a few minutes.

## Configure the CLI to use that cluster

Using the cluster's resource group and name by default will save you a lot of typing later:

```bash
az configure --defaults group=<resource group name>
az configure --defaults spring-cloud=<service instance name>
```

This is also a good time to ensure your Azure CLI is logged into your Azure subscription

```bash
az account show # See the currently signed-in account
az login # Sign into an azure account
```

---

⬅️ Previous guide: [00 - Set Up Your Environment](../00-setup-your-environment/README.md)

➡️ Next guide: [02 - Build a simple Spring Boot microservice](../02-build-a-simple-spring-boot-microservice/README.md)
