> **Note:**
> Please read the deployment guide below before deploying to Azure

<br/>

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fsamelhousseini%2Fkm-openai%2Fmain%2Ftemplate.json)
<br />
<br />
<br />

# Deployment Guide
<br />

The ARM template is not fully automated (yet), so a few manual steps will have to be taken to set up your project:

1. At the deployment stage of the ARM template:
   * please deploy all resources in only one of the following 3 regions: West Europe, East US, South Central US
   * rename the instances randomly, a good idea would be to add your suffix to all resources, e.g. "-seh" and "seh" for the storage account
   * please make sure that the Storage account name does not have any hyphens or punctuations
    <br/>
    <br />
    <p align="center">
    <img src="images/suffix.jpg" width="600" />
    </p>
    <br/>
1. Once deployment is done, please update the local.settings.json in VS Code with all the keys and endpoints from all the resources that were just created.
1. Deploy the settings you've just filled in the local.settigns.json to the Function App. Please make sure that the "Azure Functions" extension is installed in your VS Code. Go to the right Function App under the "Resources" section and then right-click on "Application Settings" and "Upload Local Settings..."
    <br/>
    <br />
    <p align="center">
    <img src="images/local_settings.jpg" width="300" />
    </p>
    <br/>
1. Deploy the Azure Functions to the FuncApp. During functions deployment in VS Code. The deployment will ask for a storage account, please supply the one in the RG
1. Update the .env in VS Code for: 
   * Cognitive Search settings: endpoint and primary key
   * Cognitive Services settings: endpoint and primary key
   * Blob Connection String
   * Get the Azure Function URI of the "AzCogSearchDocCrackingFunc" from the Azure Portal, and write it in the "COG_SEARCH_CUSTOM_FUNC" field in .env
    <br/>
    <br />
    <p align="center">
    <img src="images/custom_skill.jpg" width="450" />
    </p>
    <br/>
1. Go to the FuncApp, and then click on one of the deployed functions, and then the “Monitor” tab. Then please create the application insights. This step is only needed for troubleshooting
1. Deploy 4 models in OpenAI:
   * Go to Deployments in your OpenAI resource
   * Add 4 models:
      1. text-davinci-003
      1. text-search-davinci-doc-001
      1. text-search-davinci-query-001
      1. text-embedding-ada-002
    <br/>
    <br />
    <p align="center">
    <img src="images/oai_deployments.jpg" width="500" />
    </p>

    <br/>
1. Upload the KB documents to the Blob Storage container “kmoaidemo”
1. Before running the "experiments.ipynb" notebook, a Conda environment will need to be created. Python 3.8 and 3.9 were tested and worked fine. The packages can either be installed from within the notebook, or run pip install after activating the new environment:
    `pip install -r requirements.py`
1. Start the search by running the third cells in “experiment.ipynb” notebook
    <br/>
    <br />
    <p align="center">
    <img src="images/run_ingest.jpg" width="400" />
    </p>
    <br/>
1. **(Optional)** "redis.yml" is also included with this repo. This is optional but might be needed if the user wants to use a Redis ACI instead of Redis Enterprise for development purposes. You can create the Redis container on ACI using the following command:<br/>
        `az container create --resource-group <YOUR_RG_NAME> --file redis.yml`
1. **(Optional)** Cosmos DB is added for development purposes as well. In the Function App settings, there is a field "DATABASE_MODE" that controls the behavior of the Custom Skill in Cognitive Search. If set to 0 (which is the default), it will save the documents contents in the Blob Storage. If it's set to 1, it will create records in Cosmos DB. Cosmos can then be accessed programmatically from a Python notebook to test around with embeddings and completions.

<br/>
<br/>

# Troubleshooting

1. If for whatever reason, there was an error in writing the .env settings, please remember to restart the kernel in the notebook after correcting them, in order for the changes to take effect.
1. If for whatever reason, there was an error in writing the settings in the Function App Configuration, then please restart the Function App from the Azure portal, in order for the changes to take effect.
1. Please install [Redis Insight](https://redis.com/redis-enterprise/redis-insight/) if you want to inspect the embeddings in Redis.
    <br/>
    <br />
    <p align="center">
    <img src="images/redis.jpg" width="600" />
    </p>
    <br/>

<br/>
<br/>

# Interfacing with the Solution: Chatbot and Postman

1. In this repo, there's a general guide "AzureOpenAIandPVAbot.pdf" to build chat bots using Power Virtual Agents. This guide can be adapted and used to build a bot that will interface with the already deployed "BotQnAHTTPFunc" Azure Function in the Function App. The returned link for the document below has been hidden:
    <br/>
    <br />
    <p align="center">
    <img src="images/chatbot.jpg" height="400" />
    </p>
    <br/>
1. Postman could also be used to connect to the deployed "BotQnAHTTPFunc" Azure Function in the Function App. The full URL for the function can be copied from the Function App in the Azure portal.
    <br/>
    <br />
    <p align="center">
    <img src="images/postman.jpg" width="600" />
    </p>
    <br/>
1. The following queries can be used to try with the current sample dataset to try the new service:
    * `In which classes did the Danish sailors qualify?`
    * `What are the reviews of the Lost City hotel?`
    * `what are the reviews of the Atlantis hotel?` -> this should give a negative answer as it is not included in the sample knowledge base.


<br/>
<br/>

# AI Rangers Solution Accelerators

Developed by the Microsoft AI Rangers Team, the AI Solution Accelerators are repeatable IP meant to provide developers with all the resources needed to quickly build an initial solution. The objective is to jump-start the development efforts and to learn the used technologies in as little time as possible. The AI Solution Accelerators should be considered as templates that are fully customizable to the user's unique business case. You can see the **list of accelerators** in the following link: [aka.ms/aiaccelerators](https://aka.ms/aiaccelerators). These accelerators can be deployed on the Azure platform. 

Please visit the official [Azure AI webpage](https://azure.microsoft.com/en-us/overview/ai-platform/) to learn more about the Azure AI solution offerings.

<br/>
<br/>


