[![Build Status](https://dev.azure.com/VicDemos/techsummit2018/_apis/build/status/build)](https://dev.azure.com/VicDemos/techsummit2018/_build/latest?definitionId=18)
[![Build Status](https://dev.azure.com/VicDemos/techsummit2018/_apis/build/status/deploy)](https://dev.azure.com/VicDemos/techsummit2018/_build/latest?definitionId=19)
# MS TechSummit Oslo 2018 demo: Minimal Blog deployed thru Azure DevOps with Terraform and Github usage


## About demo

Inspired by my collegue's blog post: 
https://open.microsoft.com/2018/11/16/terraform-jamstack-azure-gatsby-azure-pipelines-git/

__Updated and modified for the MS Oslo TechSummit 2018 demo purpose (demo date: 06.12.2018)__


## Architecture of workflow
![flow](https://open.microsoft.com/wp-content/uploads/2018/11/JAMStack-workflow_image-1-v2.png)

## Application architecture

The application is based on JAMstack solution. It is a modern web development architecture based on client-side JavaScript, reusable APIs, and prebuilt Markup. No backend logic or persistence layer access — everything is encapsulated in the API. More info at [JAMstack homepage](https://jamstack.org/). 
Website is based on [Gatsby](gatsbyjs.org)

## Actions

### Website running locally in dev mode

```bash
npm install --global gatsby-cli
gatsby new techsummit2018 git@github.com:texnokot/techsummit2018.git
cd techsummit2018
gatsby develop
```

### Action points

1. Connect Github repository with Azure DevOps Services
2. Build pipeline to cook website: 
    * Prepare blog static files for deploy
3. Build pipeline to deploy infrastructure and code:
    * Make storage account where blog will be hosted
    * Enable Static Website option on storage account
    * Deploy Azure CDN profile and endpoint with pointing to Storage account
    * Upload static assets to container in storage account
4. Don't forget to make Terraform state storage account
5. Tune Terraform variables and connect to Azure subscription (Azure KeyVault example)
6. Run both pipelines and enjoy website
7. Add security checks for packages and libraries used into Build pipeline with Whitespace Bolt help


### Going into details








