# 00 - Setup your environment

__This guide is part of the [Azure Spring Cloud Migration Lab](../README.md)__

Setting up all the necessary prerequisites in order to complete the lab in time.

---

To complete the lab in the time allotted, you should have all the pre-requisites ready.

## Local System Prerequisites

This training lab requires the following to be installed on your machine:
* [JDK 1.8](https://www.azul.com/downloads/zulu-community/?&version=java-8-lts&architecture=x86-64-bit&package=jdk)
* A text editor or an IDE. If you do not already have an IDE for Java development, we recommend using [Visual Studio Code](https://code.visualstudio.com/) with the [Java Extension Pack](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack).
* [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).
* The `spring-cloud` extension for Azure CLI. You can install this extension after installing Azure CLI by running `az extension add -n spring-cloud -y`.
* [MySQL CLI](https://dev.mysql.com/downloads/)
* The Azure Spring Cloud CLI extension  (install via `az extension add --name spring-cloud`).

The environment variable `JAVA_HOME` should be set to the path of `javac` in the JDK installation.

### Using Docker

A docker image conaining all of the above dependencies is available. With docker installed, run (in bash or PowerShell with administrator privileges)

```bash
docker run -d azurejavalab.azurecr.io/azurejavalab:2020.01
```

Then, start Visual Studio Code and ensure the [Remote - Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) extension is installed. With the extension available, connect to the container in Visual Studio Code.

If you haven't pulled down the docker image in advance, proceed to [Creating Azure Resources](#creating-azure-resources) while you wait for it to download.

**Note**: The Docker repository credential provided is read-only. You will not be able to push any modifications you make to the docker image.

## Creating Azure Resources

To save time, we provide an ARM template for creating all the Azure resources you will need for this lab other than the Azure Spring Cloud instance itself.

```bash
az login # Log into your Azure account if necessary

az group create -g $RESOURCE_GROUP_NAME --location westus2 # Create a new resource group for this lab

az group deployment create -g $RESOURCE_GROUP_NAME --template-file azuredeploy.json --parameters 'mysql_admin_password=super$ecr3t' # Substitute something else for the password parameter
```

The resource provisioning will take some time. Once you have completed all other steps on this page, proceed to the next section.
