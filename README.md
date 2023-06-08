# extend-helper-cli

## Overview

A helper command line app for supporting AccelByte Extend use cases.

## Download

Latest builds can be downloaded from [releases](https://github.com/AccelByte/extend-helper-cli/releases) page.

## Prerequisites

1.  Access to `AccelByte Gaming Services` environment. Keep the `Base URL` e.g. for demo environment, it is https://demo.accelbyte.io.

2. [Create an OAuth Client](https://docs.accelbyte.io/guides/access/iam-client.html) with `confidential` client type with the following permissions. Keep the `Client ID` and `Client Secret`.

   - ADMIN:NAMESPACE:{namespace}:EXTEND:REPOCREDENTIALS [READ]

3. Create a user. Keep the `Username` and `Password`. 

4. Docker.

## Setup

Set the required environment variables by this command line app.

```shell
# Base URL of AccelByte Gaming Services e.g. https://demo.accelbyte.io
export AB_BASE_URL='https://xxxxxxxxxx'
# Use Client ID and Client Secret from the Prerequisites section
export AB_CLIENT_ID='xxxxxxxxxx'               
export AB_CLIENT_SECRET='xxxxxxxxxx'              
# Use Username and Password from the Prerequisites section
export AB_USERNAME='xxxxxxxxxx'               
export AB_PASSWORD='xxxxxxxxxx'
```

OR

Save the required variables in an `.env` file in the directory where this command line app will be executed.

```
AB_BASE_URL=https://xxxxxxxxxx
AB_CLIENT_ID=xxxxxxxxxx             
AB_CLIENT_SECRET=xxxxxxxxxx             
AB_USERNAME=xxxxxxxxxx              
AB_PASSWORD=xxxxxxxxxx
```

## Usage

### Getting Credentials for Pushing Extend App Images

To get credentials for pushing Extend App image to AccelByte Extend container registry,
use the following command. Note that the username is always `AWS` and the repository URL
is obtained from the Admin Portal after creating the Extend App. In the following example, 
the Extend App name created is `chatfilter01` and the repository URL is `342674635073.dkr.ecr.us-west-2.amazonaws.com`.

```shell
# Using -p option to print only password and pipe it to docker login
extend-helper-cli dockerlogin --namespace my-game --app chatfilter01 -p | docker login -u AWS --password-stdin 342674635073.dkr.ecr.us-west-2.amazonaws.com
```

OR

Get the credentials in JSON format to be used with `docker login` later.

```shell
# Print out a JSON containing docker username, password, and repository URL
extend-helper-cli dockerlogin --namespace my-game --app chatfilter01
```

After `docker login` is successful, you can build your Extend App and tag the image
according to the repository URL. Continuing from the above example, for `chatfilter01`,
it will be `342674635073.dkr.ecr.us-west-2.amazonaws.com/accelbyte/justice/development/extend/my-game/chatfilter01`.

```shell
# Build new image
docker build -t 342674635073.dkr.ecr.us-west-2.amazonaws.com/accelbyte/justice/development/extend/my-game/chatfilter01:v1.0.0 .
# Push the image
docker push 342674635073.dkr.ecr.us-west-2.amazonaws.com/accelbyte/justice/development/extend/my-game/chatfilter01:v1.0.0
```