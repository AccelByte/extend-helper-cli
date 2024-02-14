# extend-helper-cli

## Overview

A helper command line app for supporting `AccelByte Gaming Services` Extend use cases.

:exclamation: **This repository contains binary release only.**

## Download

Latest builds can be downloaded from [releases](https://github.com/AccelByte/extend-helper-cli/releases) page.

## Prerequisites

1. Docker v23.x

2.  Access to `AccelByte Gaming Services` environment. Keep the `Base URL` e.g. https://test.accelbyte.io.

3. [Create an OAuth Client](https://docs.accelbyte.io/guides/access/iam-client.html) with `confidential` client type with the following permission. Keep the `Client ID` and `Client Secret`.

   - `ADMIN:NAMESPACE:{namespace}:EXTEND:REPOCREDENTIALS` [READ]

   **For extend-helper-cli <= v0.0.3 only**: Create a user if you don't have any with the following permission. Keep the Username and Password.

   - `ADMIN:NAMESPACE:{namespace}:EXTEND:REPOCREDENTIALS` [READ]

## Setup

Set the required environment variables by this command line app.

```shell
# Base URL of AccelByte Gaming Services e.g. https://demo.accelbyte.io
export AB_BASE_URL='https://xxxxxxxxxx'
# Use Client ID and Client Secret from the Prerequisites section
export AB_CLIENT_ID='xxxxxxxxxx'               
export AB_CLIENT_SECRET='xxxxxxxxxx' 

# For extend-helper-cli <= v0.0.3 only: use Username and Password from the Prerequisites section
export AB_USERNAME='xxxxxxxxxx'               
export AB_PASSWORD='xxxxxxxxxx'
```

OR

Save the required variables in an `.env` file in the directory where this command line app will be executed.

```
AB_BASE_URL='https://xxxxxxxxxx'
AB_CLIENT_ID='xxxxxxxxxx'             
AB_CLIENT_SECRET='xxxxxxxxxx'
```

## Usage

### Getting credentials for pushing Extend App container images

Use `dockerlogin` command to get the required credentials for pushing `Extend App` 
container images to `Extend App` container registry. The credentials can be used only 
for a specific game namespace and `Extend App`. For different game namespace and 
`Extend App`, you will need to use this command again.


```shell
extend-helper-cli dockerlogin --namespace <my-game-namespace> --app <my-extend-app> --login
```

### Pushing an Extend App container image

After `dockerlogin` command successfully, you can build your `Extend App`, 
tag the image according to the repository URL, and push it. For example:

```shell
# Build new image
docker build -t 342674635073.dkr.ecr.us-west-2.amazonaws.com/accelbyte/justice/development/extend/<my-game-namespace>/<my-extend-app>:v1.0.0 .

# Push the image
docker push 342674635073.dkr.ecr.us-west-2.amazonaws.com/accelbyte/justice/development/extend/<my-game-namespace>/<my-extend-app>:v1.0.0
```

## Troubleshooting

### docker login: error storing credentials `The stub received bad data.`

This issue has something to do with the token size being larger than most credential managers can handle. Likely, you will encounter this in Windows OS.

```
Error saving credentials: error storing credentials - err: exit status 1, out: `error storing credentials - err: exit status 1, out: `The stub received bad data.`
```
Possible workaround:
```
# Remove all
C:\Program Files\Docker\Docker\resources\bin\docker-credential-*.exe
```
More [discussions here](https://stackoverflow.com/questions/60807697/docker-login-error-storing-credentials-the-stub-received-bad-data).