# extend-helper-cli

## Overview

A helper command line app for supporting AccelByte Extend use cases.

## Download

Latest builds can be downloaded from [releases](https://github.com/AccelByte/extend-helper-cli/releases) page.

## Prerequisites

1.  Access to `AccelByte Gaming Services` environment. Keep the `Base URL` e.g. for demo environment, it is https://demo.accelbyte.io.

2. [Create an OAuth Client](https://docs.accelbyte.io/guides/access/iam-client.html) with `confidential` client type with the following permission. Keep the `Client ID` and `Client Secret`.

   - `ADMIN:NAMESPACE:{namespace}:EXTEND:REPOCREDENTIALS` [READ]

   If you use extend-helper-cli v0.0.3 or lower, create a user if you don't have any with the following permission. Keep the Username and Password.

   - `ADMIN:NAMESPACE:{namespace}:EXTEND:REPOCREDENTIALS` [READ]

3. Docker.

## Setup

Set the required environment variables by this command line app.

```shell
# Base URL of AccelByte Gaming Services e.g. https://demo.accelbyte.io
export AB_BASE_URL='https://xxxxxxxxxx'
# Use Client ID and Client Secret from the Prerequisites section
export AB_CLIENT_ID='xxxxxxxxxx'               
export AB_CLIENT_SECRET='xxxxxxxxxx'              
# (This is for extend-helper-cli v0.0.3 or lower) Use Username and Password from the Prerequisites section
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

### Getting Credentials for Pushing Extend App Images

This command will get credentials for pushing Extend App Container image to AccelByte Extend container registry.

The credentials can be use only for specified game namespace and extend app name, you need to re-login for other apps.

Recommended way using `--login` flag to automatically run docker login

```shell
extend-helper-cli dockerlogin --namespace my-game --app chatfilter01 --login
```

After docker login successfully you can now push extend app container image

> Note: after this step you can immediately proceed to `Push Container Image` section

OR

Notice the `--login` flag omitted, that way will only print out docker credentials as json

```shell
extend-helper-cli dockerlogin --namespace my-game --app chatfilter01
```

OR

You can manually docker login by specifying `-p` flag to output only password and pipe it to docker command. The username is always `AWS` and the repository URL
is obtained from the Admin Portal after creating the Extend App. In the following example, 
the Extend App name created is `chatfilter01` and the repository URL is `342674635073.dkr.ecr.us-west-2.amazonaws.com`.

```shell
# Using -p option to print only password and pipe it to docker login
extend-helper-cli dockerlogin --namespace my-game --app chatfilter01 -p | docker login -u AWS --password-stdin 342674635073.dkr.ecr.us-west-2.amazonaws.com
```

### Push Container Image

After `docker login` is successful, you can build the Extend App, tag the image
according to the repository URL, and push it.

```shell
# Build new image
docker build -t 342674635073.dkr.ecr.us-west-2.amazonaws.com/accelbyte/justice/development/extend/my-game/chatfilter01:v1.0.0 .
# Push the image
docker push 342674635073.dkr.ecr.us-west-2.amazonaws.com/accelbyte/justice/development/extend/my-game/chatfilter01:v1.0.0
```

## Troubleshooting

### docker login: error storing credentials `The stub received bad data.`

This issue has something to do with the token size being larger than most credential managers can handle. Likely, you will encounter this in Windows OS.

```text
Error saving credentials: error storing credentials - err: exit status 1, out: `error storing credentials - err: exit status 1, out: `The stub received bad data.`
```
Possible workaround:
```text
# remove all
C:\Program Files\Docker\Docker\resources\bin\docker-credential-*.exe
```
For more [discussion here](https://stackoverflow.com/questions/60807697/docker-login-error-storing-credentials-the-stub-received-bad-data).