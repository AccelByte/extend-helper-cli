# extend-helper-cli

## Overview

A helper command line app for supporting `AccelByte Gaming Services` Extend use cases.

:exclamation: **This repository contains binary release only.**

## Download

Latest builds can be downloaded from [releases](https://github.com/AccelByte/extend-helper-cli/releases) page.

## Prerequisites

1. Docker (Docker Engine v23.0+)

   ```
   docker version

   ...
   Server: Docker Desktop
      Engine:
      Version:          24.0.5
   ...
   ```

   :exclamation: **In Linux, you need to be able to run Docker commands without root.** Add your user to `docker` group: `sudo usermod -aG docker $USER`.

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
# Builds and pushes the image
extend-helper-cli image-upload --namespace <my-game-namespace> --app <my-extend-app>
    --image-tag v1.0.0
    --work-dir <path-to-directory-containing-service-dockerfile>
```

> :bulb: You can also use the `--login` flag to execute `dockerlogin` beforehand.

```shell
# Uses `dockerlogin --namespace <my-game-namespace> --app <my-extend-app> --login` and then; builds and pushes the image
extend-helper-cli image-upload --namespace <my-game-namespace> --app <my-extend-app>
    --image-tag v1.0.0
    --work-dir <path-to-directory-containing-service-dockerfile>
    --login
```

or you could build and push it manually:

```shell
# Build new image
docker build -t xxxxxxxxxxxx.dkr.ecr.us-west-2.amazonaws.com/accelbyte/justice/development/extend/<my-game-namespace>/<my-extend-app>:v1.0.0 .

# Push the image
docker push xxxxxxxxxxxx.dkr.ecr.us-west-2.amazonaws.com/accelbyte/justice/development/extend/<my-game-namespace>/<my-extend-app>:v1.0.0
```

### Getting App Info

```shell
extend-helper-cli get-app-info --namespace <my-game-namespace> --app <my-extend-app>
```

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

If you want to only query a specific field use `--path` and pass in a valid [JSON pointer](https://datatracker.ietf.org/doc/html/rfc6901).

```shell
extend-helper-cli get-app-info --namespace <my-game-namespace> --app <my-extend-app> --path /appName
```

```text
<my-extend-app>
```

---

```shell
extend-helper-cli get-app-info --namespace <my-game-namespace> --app <my-extend-app> --path /appRepoUrl
```

```text
xxxx.xxxx.xxxx.xxxxxxxxx.xxxx.xxxx/xxxx/xxxx/xxxx/xxxx/xxx-xxxx-xxxx/xxxx
```

## Troubleshooting

### docker login: error storing credentials `The stub received bad data.`

When pushing an Extend app container image to AGS, `extend-helper-cli dockerlogin ...` command returns the following error.

```
Error saving credentials: error storing credentials - err: exit status 1, out: `error storing credentials - err: exit status 1, out: `The stub received bad data.`
```

This issue has something to do with the token size being larger than most credential managers can handle.

Depending on your operating system, a possible workaround is to delete either `"credsStore": "desktop"` or `"credsStore": "desktop.exe"` from your Docker `config.json`.

    - Linux or WSL2: $HOME/.docker\config.json
    - Windows: %USERPROFILE%\.docker\config.json


More disscussions [here](https://stackoverflow.com/questions/60807697/docker-login-error-storing-credentials-the-stub-received-bad-data).
