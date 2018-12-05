[![Build Status](https://dev.azure.com/VicDemos/techsummit2018/_apis/build/status/deploy)](https://dev.azure.com/VicDemos/techsummit2018/_build/latest?definitionId=19)

# MS TechSummit Oslo 2018 demo  

## About demo

Inspired by my collegue's blog post: 
https://open.microsoft.com/2018/11/16/terraform-jamstack-azure-gatsby-azure-pipelines-git/
How to configure and deploy pls look in provided link. This repository is updated with Azure Key Vault and WhiteSource Bolt plugin in deploy pipelines.

## Architecture of workflow

![flow](https://open.microsoft.com/wp-content/uploads/2018/11/JAMStack-workflow_image-1-v2.png)

## Application architecture

The application is based on JAMstack solution. It is a modern web development architecture based on client-side JavaScript, reusable APIs, and prebuilt Markup. No backend logic or persistence layer access — everything is encapsulated in the API. More info at [JAMstack homepage](https://jamstack.org/).
Website is based on [Gatsby](gatsbyjs.org)

## Actions

### Website running locally in dev mode

```bash
npm install --global gatsby-cli
gatsby new techsummit2018 https://github.com/texnokot/techsummit2018
cd techsummit2018
gatsby develop
```

### Action points for the demo

1. Connect Github repository with Azure DevOps Services
2. Build pipeline to cook website:
    * Prepare blog static files for deploy
3. Make Terraform state storage account
4. Tune Terraform variables and connect to Azure subscription
5. Build pipeline to deploy infrastructure and code:
    * Make storage account where blog will be hosted
    * Enable Static Website option on storage account
    * Deploy Azure CDN profile and endpoint with pointing to Storage account
    * Upload static assets to container in storage account
6. Connect Azure Key Vault to Variable groups and enable triggers for CD
7. Run both pipelines and enjoy website
8. Add security checks for packages and libraries used into Build pipeline with Whitespace Bolt help

### Step by step instructions

#### Connect Azure DevOps Services to the subscription

In Azure DevOps portal:
Project settings -> Pipelines -> Service connections -> New service connection -> Azure Resource Manager
Choose Managed Identity authentication. Choose your subscription and allow for the whole subscription since Terraform script will be responsible for Resource Group creation.

#### Enable Azure pipelines in Github

![pipeline enable](https://open.microsoft.com/wp-content/uploads/2018/11/Azure-Pipelines_image6-1024x373.png)

#### Make a build pipeline

Make a build pipeline from deploy/build.yaml file
![add build pipeline](https://publicbw.blob.core.windows.net/techsummit/makebuildpipeline.gif)

You can run it and as result artifacts will be available after a successful build. Drop folder contains generated static content for the website.
![artifacts](https://publicbw.blob.core.windows.net/techsummit/artifacts.gif)

Drop folder will be used in deploy pipeline. But before that...

### Organize Terraform environment

To able work with Terraform and keep state remotely for this demo there are two steps involved:

1. Create state storage in Azure. [How to do this](https://docs.microsoft.com/en-us/azure/terraform/terraform-backend)
2. Set up Terraform access to Azure. [How to do this](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/terraform-install-configure?toc=%2Fen-us%2Fazure%2Fterraform%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json#set-up-terraform-access-to-azure)

Remember Terraform environment variables as they will be needed in the next step.

### Create Key Vault storage and put variables

We don't want to store secrets in the deployment pipeline, so let's put them in Azure Key Vault.

1. Create Azure Key Vault. [How to do this](https://docs.microsoft.com/en-us/azure/key-vault/quick-create-portal)
2. Place Terraform variables. I also added blob name (for the website). Just for fun.
![keys](https://publicbw.blob.core.windows.net/techsummit/keys.png)

### Make a deploy pipeline

Azure pipelines Release pipeline construct is not supporting YAML yet. But we want to follow the "pipeline as code" principle,so let's make deploy in the deployment pipeline. Create and save, but don't run.
![add deploy pipeline](https://publicbw.blob.core.windows.net/techsummit/makedeploypipeline.gif)

### Connect Azure Key Vault and add variables to variable group

1. [How to do this](https://docs.microsoft.com/en-us/azure/devops/pipelines/library/variable-groups?view=vsts&tabs=yaml)
2. Name variable group as "secrets"
3. Include all variables saved in Azure Key Vault

### Link variable group secret to Deploy pipeline

![link variable group](https://publicbw.blob.core.windows.net/techsummit/linkvariables.gif)

### Set up triggers

To enable Continious integration after each build or if infrastructure has changed we should enable triggers.
In the deploy pipeline enable CI and link Build completion to be sure that Deploy pipeline is triggered after build completion.

![enable triggers](https://publicbw.blob.core.windows.net/techsummit/triggers.gif)

Now you can run and website should be built and available for you at the address: https://"$(blobname)".azureedge.net/index.html

### Additional bonus: security

Since I'm a security girl, I always encourage people to have security checks. So we are going to add WhiteSource Bolt extension in build pipeline. This allows us to see how secure and updated are used libraries. Additionally, we can see their licenses. Good catch and for free. 
Get extension [here](https://marketplace.visualstudio.com/items?itemName=whitesource.ws-bolt)

To enable it in build pipeline add following in the end of YAML file:

```bash
- task: whitesource.ws-bolt.bolt.wss.WhiteSource Bolt@18
  displayName: 'WhiteSource Bolt'
```

Now after build run you will see security status of the project.
![bolt](https://publicbw.blob.core.windows.net/techsummit/bolt.png)

Uff, seems not so good! But that's another story..
