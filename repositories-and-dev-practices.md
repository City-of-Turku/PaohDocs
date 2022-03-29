# Repositories and development practices <!-- omit in toc -->

### Contents <!-- omit in toc -->

- [1. Development practices](#1-development-practices)
  - [1.1 Repository branches](#11-repository-branches)
  - [1.2 Pull Requests and branch merging](#12-pull-requests-and-branch-merging)
  - [1.3 Testing and deployment](#13-testing-and-deployment)
- [2. Repositories](#2-repositories)
  - [2.1 Webservice repositories](#21-webservice-repositories)
    - [2.1.1 WebFrontend repository](#211-webfrontend-repository)
  - [2.2 Chatbot repositories](#22-chatbot-repositories)
    - [2.2.1 RasaForBotfront repository](#221-rasaforbotfront-repository)
    - [2.2.1 RasaPlatform repository](#221-rasaplatform-repository)
    - [2.2.2 Botfront repository (fork of Botfront platform)](#222-botfront-repository-fork-of-botfront-platform)
    - [2.2.3 BotfrontChatbotProject repository](#223-botfrontchatbotproject-repository)
  - [2.3 Service Recommender repositories](#23-service-recommender-repositories)
    - [2.3.1 ServiceMatchEngine repository](#231-servicematchengine-repository)
    - [2.3.2 ServiceDataImport repository](#232-servicedataimport-repository)
  - [2.4 Azure Infra repository](#24-azure-infra-repository)

# 1. Development practices

These general practices apply for all the repositories. If some repository has more specific practices they will be introduced in the repository's README file.

## 1.1 Repository branches

- main branch
  - Main branch integrated to production environment
- dev branch
  - Branch integrated to testing environment
- feat/[USER STORY ID]-name-of-branch
  - Development branches for new features from user stories. Intended to be short-lived.
- fix/name-of-branch
  - Development branches for bug fixes. Intended to be short-lived. Branch name could have some ticket ID if we had ticketing system for issues.

## 1.2 Pull Requests and branch merging

When your development branch (e.g. "feat/XXX") is finished in your mind, create Pull Request in DevOps to dev branch. Add suitable person (e.g. from the list below) from the team as Reviewer so they can review your PR. After reviewer has approved the PR, you can merge the PR into dev branch. Use "Squash commit" as your merge strategy to avoid the main branch getting cluttered with commits. You can also delete the development ("feat/XXX") branch after merging.

Pull Request reviewers: <br>
Developer Aapo -> Reviewer Joonas <br>
Developer Joonas -> Reviewer Aapo <br>
Developer Ossian -> Reviewer Aapo/Joonas <br>

## 1.3 Testing and deployment

Most of the components can be also run locally with Docker containers or Azure function tools. Check `README.md` file of the repository how to set up components to run them locally.

After changes have been merged into `dev` branch those can also be tested in the proper test environment. Deployment happens automatically using Azure Pipelines. Ensure in this environment that everything works properly before deploying to production.

# 2. Repositories

In the [architecture](/arkkitehtuuri.md#palveluohjausalustan-tarkempi-arkkitehtuuri-c4-level-2) there are three main software services included in the whole system: Webservice, Chatbot and Service Recommender. Below, you can see all repositories for those three services.

Also, there is repository for Azure's infrastructure and resources in infra-as-code (IaC) format to be used in creating the Azure infra automatically. It is explained more in the section 2.4.

## 2.1 Webservice repositories

There is one repository for the webservice called [WebFrontend](https://github.com/City-of-Turku/PaohWebFrontend)

### 2.1.1 WebFrontend repository

This repository contains React frontend and Node backend for the web service. Instructions for local development and deploying the service are available in the repository.

## 2.2 Chatbot repositories

There are four repositories for the chatbot:

- [RasaForBotfront](https://github.com/City-of-Turku/PaohRasaForBotfront)
- [RasaPlatform](https://github.com/City-of-Turku/PaohRasaPlatform)
- [Botfront](https://github.com/City-of-Turku/PaohBotfront)
- [BotfrontChatbotProject](https://github.com/City-of-Turku/PaohBotfrontChatbotProject)

The chatbot is developed and maintained using an open source graphical UI tool called [Botfront](https://botfront.io/docs/rasa/getting-started/). Thus, we can separate the development of the Botfront tool itself from the actual development of the chatbot project into two different repositories (Botfront and BotfrontChatbotProject)

Under the hood, Botfront uses open source chatbot engine called [Rasa](https://rasa.com/). For Botfront, the original Rasa has been forked with a few modifications. That is done in the repo RasaForBotfront. In this Turku project, we have furthed added a few lighter add-ons to the Rasa and also built Rasa Actions to be used in the chatbot which all are in their own repository called RasaPlatform.

### 2.2.1 RasaForBotfront repository

This repository contains Dockerfile to start the Botfront's fork of Rasa. Docker build of this repo acts as the "base" image for RasaPlatform repo's Rasa Docker build. Docker build of this repo takes longer and this repo needs less mofidications so that's why it acts as the "base" image. RasaPlatform repo's Rasa contains a few addons for Rasa which are more actively developed and quicker to build so that repo will finally deploy the Rasa to Azure AKS environment.

### 2.2.1 RasaPlatform repository

This repository contains Dockerfiles and docker-compose file to start our modified Rasa and its Actions locally with Docker. Using this repository, we can for example develop custom API connectors to the chatbot so that the chatbot can request the Service Recommender APIs or other APIs like AuroraAI API.

### 2.2.2 Botfront repository (fork of Botfront platform)

This repository was forked from [Botfront platform's GitHub repository](https://github.com/botfront/botfront).

### 2.2.3 BotfrontChatbotProject repository

This repository contains the chatbot project files exported from the Botfront. Under the hood, Botfront uses open source chatbot technology called [Rasa](https://rasa.com/docs/rasa/) so the project files contain Rasa formatted files and a few Botfront config files. This enables you to import the chatbot project into Rasa if you wish. Files contain for example all the training data examples and responses of the chatbot.

## 2.3 Service Recommender repositories

There are two used repositories for the service recommender called [ServiceMatchEngine](https://github.com/City-of-Turku/PaohServiceMatchEngine) and [ServiceDataImport](https://github.com/City-of-Turku/PaohServiceDataImport)

There is also repository [YTRServiceDataImport](https://github.com/City-of-Turku/PaohYTRServiceDataImport) which was used to developt the Service Data Importer for Kompassi-YTR but it isn't in use anymore since integration to YTR was abandoned.

### 2.3.1 ServiceMatchEngine repository

This repository contains resources to bring up a docker containers and an Azure function that provide API that can be used to get information of available services and to get service recommendations based on text data or some other criteria. Service Matcher API, NLP vectorizer, Lexical Text Search and Service Data Processor are different components that together produce the Service Match Engine.

Rasa actions and web frontend are able to ask these recommendations from Service match API which integrates it to the actual chatbot. This uses MongoDB as the source of its service data.

### 2.3.2 ServiceDataImport repository

This is a repository that contains an Azure function to fetch and update service and service channel data in Mongo database in a scheduled manner. This uses Time Trigger to run once a day to update service data to MongoDB. Currently this only uses PTV as source data but in the future YTR and possible other sources can be used.

## 2.4 Azure Infra repository

[AzureInfra](https://github.com/City-of-Turku/PaohAzureInfra) repository contains Bicep templates to be used as infra-as-code (IaC) for configuring and deploying Azure resources as needed in the [Azure architecture](/azure-arkkitehtuuri.md). Using these IaC templates you can create almost the whole Azure infrastructure and needed resources from the ground up automatically.
