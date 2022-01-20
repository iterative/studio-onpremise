# DVC Studio On-Premise

This repository contains recipes for you to run DVC Studio by Iterative on your own
infrastructure, using `docker-compose` or `k8s` or one of its flavors.

The guide will walk you through the preparation, customization, and basic
deployment scenarios.

## Prerequisites

### Getting the OAuth apps

In order to run Studio on premise, you'll need to setup your own GitHub or
GitLab OAuth apps and provide their credentials to Studio.

#### GitHub App

You need to create your own [GitHub App](https://docs.github.com/en/developers/apps/getting-started-with-apps/about-apps#about-github-apps) for being able to authorize on the provider side.
Redirect URI should be **${API_URL}/complete/gitlab/**

#### GitLab OAuth app

Please follow [the official guide](https://docs.gitlab.com/ee/integration/oauth_provider.html) to set it up.  
Redirect URI should be **${API_URL}/complete/gitlab/**

## Deployment

Studio is distributed via Iterative's private Docker registry as a set of
pre-built images.

We assume that Docker Compose and K8s will be the most common environments
Studio will be deployed to on premise.

### Deploying using Docker Compose

1. Your infrastructure needs to have
   [docker-compose v1.25 or newer](https://docs.docker.com/compose/install/) to
   run Studio.
2. [Login to the private registry](https://docs.docker.com/engine/reference/commandline/login/)
   ```
   $ docker login -u <login> -p <password> docker.iterative.ai
   ```
3. Configure `GITHUB_APP_CLIENT_ID=.. GITHUB_SECRET_KEY=.. ./install.sh` with variables
   from previous steps.
   More info `./install --help`
4. Launch the stack `docker-compose up`

Please see [`docker-compose`](/docker-compose/) and generated `docker-compose.yaml` for more details.
