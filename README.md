# extend-helper-cli

## Overview

A command line app for supporting AccelByte Gaming Services (AGS) Extend use cases.

:exclamation: **This repository contains binary release only.**

## Download

Latest builds can be downloaded from [releases](https://github.com/AccelByte/extend-helper-cli/releases) page.

> :warning: **We recommend to always use the latest version available:** For new projects, please avoid using v0.0.3 and below.

## Prerequisites

1. Docker (Docker Desktop 4.30+/Docker Engine v23.0+)
   
      - On Linux Ubuntu:

         1. To install from the Ubuntu repository, run `sudo apt update && sudo apt install docker.io docker-buildx docker-compose-v2`.
         2. Add your user to the `docker` group: `sudo usermod -aG docker $USER`.
         3. Log out and log back in to allow the changes to take effect.

      - On Windows or macOS:

         Follow Docker's documentation on installing the Docker Desktop on [Windows](https://docs.docker.com/desktop/install/windows-install/) or [macOS](https://docs.docker.com/desktop/install/mac-install/).

         ```
         docker version

         ...
         Server: Docker Desktop
            Engine:
            Version:          24.0.5

2.  Access to AGS environment. Keep the `Base URL`.

    - Example for AGS Shared Cloud: `https://spaceshooter.prod.gamingservices.accelbyte.io`
    - Example for AGS Private Cloud: `https://dev.customer.accelbyte.io`

3. [Create an OAuth Client](https://docs.accelbyte.io/guides/access/iam-client.html) with `confidential` client type with the following permission. Keep the `Client ID` and `Client Secret`.  
  
    For AGS Private Cloud customers:
   - `ADMIN:NAMESPACE:{namespace}:EXTEND:APP [CREATE, READ, UPDATE, DELETE]`
   - `ADMIN:NAMESPACE:{namespace}:EXTEND:DEPLOYMENT [CREATE]`
   - `ADMIN:NAMESPACE:{namespace}:EXTEND:REPOCREDENTIALS [READ]`
   - `ADMIN:NAMESPACE:{namespace}:EXTEND:SECRET [CREATE, READ, UPDATE]`
   - `ADMIN:NAMESPACE:{namespace}:EXTEND:VARIABLE [CREATE, READ, UPDATE]`  
  
    For AGS Shared Cloud customers:
   - Extend > App Management (Read, Create, Update, Delete)
   - Extend > Deployment Management (Create)
   - Extend > Extend app image repository access (Read)
   - Extend > Configuration Secret Management (Read, Create, Update)
   - Extend > Configuration Variable Management (Read, Create, Update)

## Setup

Set the variables required by this command line app.

### Option 1: using environment variables

Execute the following commands depending on your operating system.

For Linux, Windows (WSL2), and macOS:

```shell
# Base URL of AccelByte Gaming Services e.g. https://test.accelbyte.io
export AB_BASE_URL='https://xxxxxxxxxx'
# Use Client ID and Client Secret from the Prerequisites section
export AB_CLIENT_ID='xxxxxxxxxx'
export AB_CLIENT_SECRET='xxxxxxxxxx'
```

For Windows (Command Prompt):

```bat
rem Base URL of AccelByte Gaming Services e.g. https://test.accelbyte.io
set AB_BASE_URL=https://xxxxxxxxxx
rem Use Client ID and Client Secret from the Prerequisites section
set AB_CLIENT_ID=xxxxxxxxxx  
set AB_CLIENT_SECRET=xxxxxxxxxx
```

### Option 2: using .env file

Put the variables in a `.env` file in the directory where this command line app will be executed.

```
AB_BASE_URL='https://xxxxxxxxxx'
AB_CLIENT_ID='xxxxxxxxxx'             
AB_CLIENT_SECRET='xxxxxxxxxx'
```

## Usage

### Creating an Extend App

Use `create-app` command to create an Extend App.

```shell
extend-helper-cli create-app --namespace <my-game-namespace> --app <my-extend-app> --scenario service-extension --confirm
```

The output will look like the following.

```text
{
  "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appName": "<my-extend-app>",
  "appRepoArn": "",
  "appRepoUrl": "",
  "basePath": "xxx-xxxx-xxxx",
  "CPU": {
    "cpuLimit": 1550,
    "requestCPU": 1000
  },
  "createdAt": "2024-01-01T00:00:00.000Z",
  "deletedAt": "",
  "deploymentCreatedAt": "",
  "deploymentId": "",
  "deploymentImageTag": "",
  "memory": {
    "memoryLimit": 3300,
    "requestMemory": 350
  },
  "message": "",
  "replica": {
    "maxReplica": 10,
    "minReplica": 1,
    "replicaLimit": 60
  },
  "scenario": "service-extension",
  "servicePublicURL": "https://xxxx.accelbyte.io/xxx-xxxx-xxxx",
  "serviceURL": "",
  "updatedAt": "2024-01-31T00:00:00.000Z"
}
```

> :bulb: You can also add `--wait` (and `--wait-interval <duration-in-seconds:10>`, `--wait-limit <duration-in-seconds:300>`)
to wait for the app to be ready for image upload/deployment.

### Getting credentials to push an Extend App container image

Use `dockerlogin` command to get the required credentials to push an Extend App container image.

```shell
extend-helper-cli dockerlogin --namespace <my-game-namespace> --app <my-extend-app> --login
```

> :warning: **The credentials can be used only for a specific game namespace and Extend App:** 
For different game namespace and Extend App, you will need to use this command again.

The output of a successful login looks like the following.

```shell
INFO[0000] signing in to https://dev.accelbyte.io 
INFO[0001] getting docker credentials...                
WARNING! Your password will be stored unencrypted in /home/xyz-abc/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

### Pushing an Extend App container image

To build your Extend App container image, tag it, and push it to the Extend App container registry, use `image-upload` command.

```shell
extend-helper-cli image-upload --namespace <my-game-namespace> --app <my-extend-app>
    --image-tag v1.0.0
    --work-dir <path-to-directory-containing-service-dockerfile>
```

> :bulb: You can also use the `--login` flag to automatically execute `dockerlogin` beforehand
so that you do not have to execute it separately.

```shell
extend-helper-cli image-upload --namespace <my-game-namespace> --app <my-extend-app>
    --image-tag v1.0.0
    --work-dir <path-to-directory-containing-service-dockerfile>
    --login
```

> :bulb: You can also add `--retry-limit` (and `--retry-interval <duration-in-seconds:1.0>`, `--retry-rate <duration-in-seconds:2.0>`)
to tell the CLI to retry the upload whenever it fails.

```shell
extend-helper-cli image-upload --namespace <my-game-namespace> --app <my-extend-app>
    --image-tag v1.0.0
    --work-dir <path-to-directory-containing-service-dockerfile>
    --retry-limit 3
```

### Getting an Extend App information

Use `get-app-info` command to get a specific Extend App information.

```shell
extend-helper-cli get-app-info --namespace <my-game-namespace> --app <my-extend-app>
```

The output will look like the following.

```text
{
  "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appName": "<my-extend-app>",
  "appRepoArn": "arn:aws:ecr:xxxxxxxxx:xxxxxxxxxxxx:xxxx/xxxx/xxxx/xxxx/xxxx/xxx-xxxx-xxxx/xxxx",
  "appRepoUrl": "xxxx.xxxx.xxxx.xxxxxxxxx.xxxx.xxxx/xxxx/xxxx/xxxx/xxxx/xxx-xxxx-xxxx/xxxx",
  "appStatus": "app-undeployed",
  "app_release_status": "U",
  "basePath": "xxx-xxxx-xxxx",
  "createdAt": "2024-01-01T00:00:00.000Z",
  "scenario": "service-extension",
  "updatedAt": "2024-01-31T00:00:00.000Z"
}
```

If you only need to query a specific field, use `--path` and pass in a valid [JSON pointer](https://datatracker.ietf.org/doc/html/rfc6901).

For example, to get the `appName` only:

```shell
extend-helper-cli get-app-info --namespace <my-game-namespace> --app <my-extend-app> --path /appName
```

Another example, to get `appRepoUrl` only:

```shell
extend-helper-cli get-app-info --namespace <my-game-namespace> --app <my-extend-app> --path /appRepoUrl
```

### Deploying an Extend App

Use `deploy-app` command to create a deployment for an Extend App.

```shell
extend-helper-cli deploy-app --namespace <my-game-namespace> --app <my-extend-app> --image-tag v1.0.0
```

The output will look like the following.

```text
{
  "deploymentId": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
}
```

> :bulb: You can also add `--wait` (and `--wait-interval <duration-in-seconds:10>`, `--wait-limit <duration-in-seconds:300>`)
to wait for the app to finish deployment.

### Starting/Stopping an Extend App

Use the `start-app` or `stop-app` command to start or stop an Extend App.

```shell
extend-helper-cli start-app --namespace <my-game-namespace> --app <my-extend-app>
```

```shell
extend-helper-cli stop-app --namespace <my-game-namespace> --app <my-extend-app>
```

> :bulb: You can also add `--wait` (and `--wait-interval <duration-in-seconds:10>`, `--wait-limit <duration-in-seconds:300>`)
to wait for the app to be fully started/stopped.

### Creating/Updating Extend App Variables/Secrets

Use the `update-var` or `update-secret` command to create new or modify existing Extend App variables or secrets.

```shell
extend-helper-cli update-var --namespace <my-game-namespace> --app <my-extend-app> --key REQUEST_TIMEOUT --value 100
```

```shell
extend-helper-cli update-secret --namespace <my-game-namespace> --app <my-extend-app> --key API_KEY --value <api-key>
```

> :bulb: You can also add `--force` to force the command to create the variable or secret if it does not exist yet.

### Creating a Tunnel to Access NoSQL Databases

Use the `tunnel` command to create a secure tunnel to access the databases through a local port.

```shell
extend-helper-cli tunnel --resource-name <resource-name> --local-port <local-port> --namespace <my-game-namespace>
```

Or use the short form:

```shell
extend-helper-cli tunnel -r <resource-name> -p <local-port> -n <my-game-namespace>
```

For example, to tunnel to a DocumentDB cluster created for your Extend App:

```shell
extend-helper-cli tunnel -r extend-nosql-accelbyte-development -p 27019 -n my-game-namespace
```

You can find the `<resource-name>` in the Admin Portal's Extend Service Extension page. Open the detail of your Extend app, click the `NoSQL Database` tab, and find the information under the `Database URL` field.

The tunnel will listen on the specified local port and forward connections to the remote resource. You can then connect your database client to `localhost:<local-port>`.

### Deleting an Extend App

Use `delete-app` command to delete an Extend App.

```shell
extend-helper-cli delete-app --namespace <my-game-namespace> --app <my-extend-app> --confirm
```

> :bulb: You can also add `--wait` (and `--wait-interval <duration-in-seconds:10>`, `--wait-limit <duration-in-seconds:300>`)
to wait for the app to be fully deleted.

## Troubleshooting

### docker login: error storing credentials `The stub received bad data.`

When pushing an Extend app container image to AGS, `extend-helper-cli dockerlogin ...` command returns the following error:

```
Error saving credentials: error storing credentials - err: exit status 1, out: `error storing credentials - err: exit status 1, out: `The stub received bad data.`
```

This issue may be due to the token size being larger than most credential managers can handle. See [here](https://stackoverflow.com/questions/60807697/docker-login-error-storing-credentials-the-stub-received-bad-data) for more information.

The workaround for this issue depends on your operating system.

For Linux:

1. Remove `"credsStore": "desktop.exe"` from `$HOME/.docker\config.json`.

2. Try `extend-helper-cli dockerlogin ...` command again.

For Windows (WSL2):

1. In Windows file system:

   - Remove `"credsStore": "desktop.exe"` from `$HOME/.docker\config.json`.

2. In WSL2 file system:

   - Remove `"credsStore": "desktop.exe"` from  `%USERPROFILE%\.docker\config.json`.
   - Rename the following files in `C:\Program Files\Docker\Docker\resources\bin`.
     - `docker-credential-desktop.exe` to `docker-credential-desktop.exe.old`
     - `docker-credential-wincred.exe` to `docker-credential-wincred.exe.old`

3. Try `extend-helper-cli dockerlogin ...` command again.

> :warning: You may need to periodically apply this workaround. Docker may restore `"credsStore": "desktop"` in the `config.json` file when it is restarted. 
See the discussion [here](https://github.com/docker/for-win/issues/9843#issuecomment-2173724429) for more information.

