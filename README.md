# Studio Selfhosted

This repository contains an installation script to deploy Iterative Studio on your infrastructure using `docker-compose`.

The guide will walk you through the preparation, customization, and basic
deployment scenarios.

## Studio components 

Studio is built from a few components:

1. UI
2. API
3. Workers
4. Services: Beat/Admin/Minio

## Prerequisites

### Minimum hardware requirements

- Memory: 4GB
- Cpu: 2
- Disk: 20Gb

### Getting the OAuth apps

In order to run Studio on premise, you'll need to setup your own GitHub or
GitLab OAuth apps and provide their credentials to Studio.

#### GitHub App

You need to create your own [GitHub App](https://docs.github.com/en/developers/apps/getting-started-with-apps/about-apps#about-github-apps) for being able to authorize on the provider side.
Redirect URI should be **${API_URL}/complete/github-app/**

**Github App scopes**:

- Contents: RW
- Issues: RW
- Metadata: R
- PullRequests: RW
- Webhooks: RW
- Email addresses: R

#### GitLab OAuth app

Please follow [the official guide](https://docs.gitlab.com/ee/integration/oauth_provider.html) to set it up.  
Redirect URI should be **${API_URL}/complete/gitlab/**

Example: If you deploy to AWS, the redirect URI may look like this
`https://<FQDN>/api/complete/gitlab/`, where `<FQDN>` is the public DNS name that you can find in the instance summary.
So for example `https://ec2-3-232-133-53.compute-1.amazonaws.com/api/complete/gitlab/`

**OAuth scopes**:

- api
- email
- profile
- read_api
- read_user
- read_repository

### Bitbucket

**OAuth scopes**:

- account:email
- account:read
- projects:read
- repositories:read
- repositories:write
- pullrequests:read
- pullrequests:write
- webhooks:readandwrite

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
   from previous steps (or any other provider). You may also put this variables in `.env` file.  
   More info `./install --help`
4. Run `./install.sh`
5. Launch the stack `docker-compose up`

Please see [`docker-compose`](/docker-compose/) and generated `docker-compose.yaml` for more details.

Tips:
* If you specify `--url` argument during the installation, for example like this
`./install.sh --url https://ec2-3-232-133-53.compute-1.amazonaws.com`
you do not need to separately specify `UI_URL` and `API_URL` environment variables
as they will be automatically set up on ports 3000 and 8000, respectively.

## Customization

### CA certificates

If you need to use additional CA certificates, whether it's Studio itself or for
connecting to your self-hosted SCM, you'll need to pass the directory containing the 
certificates as an additional argument to the install script:

```
./install.sh --tls-ca-directory /path/to/ca-certificates
```

**Note:** The CA certificate files stored in the directory _must_ have a .crt extension.

### TLS certificate

To set up Studio with a TLS certificate, you must pass three additional arguments
to the install script.

| argument         | description                             |
| ---------------- | --------------------------------------- |
| --url            | Studio URL                              |
| --tls-cert-file  | Path to your server certificate (X.509) |
| --tls-key-file   | Path to your server private key         |

Example:

```
./install.sh --url <studio url> \
    --tls-cert-file <tls cert> \
    --tls-key-file <tls key>
```

**Note:** If you're using a custom CA certificate for Studio, you'll need to set up your SCM provider so that it's
able to verify Studio's CA certificate. 
