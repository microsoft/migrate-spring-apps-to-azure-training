---
page_type: tutorial
languages:
- java
products:
- azure
description: "Migrating Spring Applications to Azure Spring Cloud"
---

# Migrating Spring Applications to Azure Spring Cloud

<!-- 
Guidelines on README format: https://review.docs.microsoft.com/help/onboard/admin/samples/concepts/readme-template?branch=master

Guidance on onboarding samples to docs.microsoft.com/samples: https://review.docs.microsoft.com/help/onboard/admin/samples/process/onboarding?branch=master

Taxonomies for products and languages: https://review.docs.microsoft.com/new-hope/information-architecture/metadata/taxonomies?branch=master
-->

In this workshop, you will migrate a set of Spring microservices to Azure Spring Cloud. We will be using a modified version of the [Piggy Metrics](https://github.com/sqshq/PiggyMetrics), licensed under MIT license.

## What you should expect

This is not the official documentation but an opinionated training.

It is a hands-on training, and it will use the command line extensively. The idea is to get coding very quickly and play with the platform, from a simple demo to far more complex examples.

After completing all the guides, you should feel confident migrating real-world Spring microservices to Azure Spring Cloud, and implementing a comprehensive and modern DevOps process.

## Symbols

>🛑 -  __Manual Modification Required__. When this symbol appears in front of one or more commands, you will need to modify the commands as indicated prior to running them.

>🚧 - __Preview-specific__. This symbol indicates steps that are only necessary while Azure Spring Cloud is in preview.

>💡 - __Frustration Avoidance Tip__. These will help you avoid potential pitfalls.

## Contents

### [00 - Prerequisites and Setup](00-setup-your-environment/README.md)

Prerequisites and environment setup.

### [01 - Migrate a Spring Cloud Application](01-migrate-spring-cloud-application/README.md)

Provisioning an Azure Spring Cloud instance, configuring diagnostic settings and distributed tracing, building and deploying the application.

### [02 - Troubleshooting](02-troubleshooting/README.md)

Using Azure Log Analytics to understand what went wrong.

### [03 - Observability and Scaling](03-observability-and-scaling/README.md)

Using Distributed Tracing to understand microservice interactions, scaling out to meet demand.

### [04 - Enable Continuous Deployment](04-enable-continuous-deployment/README.md)

Creating automated build and release pipelines with Azure DevOps.

### [05 - Enable Blue-Green Deployment](05-enable-blue-green-deployment/README.md)

Deploying microservices for testing in production without impacting the user experience.

### [Conclusion](99-conclusion/README.md)

## Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.
