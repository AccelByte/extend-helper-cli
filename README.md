# extend-helper-cli

## Features

- Get temporary docker login credentials for managing container images in AccelByte AWS ECR.

## Usage

### Download

Download the cli program from latest GitHub release page https://github.com/AccelByte/extend-helper-cli/releases

### Setup Account

To use CLI you need AccelByte IAM Client and AccelByte Account (with permission `ADMIN:NAMESPACE:{namespace}:EXTEND:REPOCREDENTIALS` (READ)).

Before running the program please set these environment variables that will be required
at runtime:

- `AB_CLIENT_ID`

  AccelByte IAM client id with confidential client type

- `AB_CLIENT_SECRET`

  AccelByte IAM client secret

- `AB_USERNAME`

  AccelByte account username or email

- `AB_PASSWORD`

  AccelByte account password

- `AB_BASE_URL`

  Base AccelByte domain url e.g. https://development.accelbyte.io

Example set envars:

```shell
AB_CLIENT_ID=accelbyte-iam-client-id
AB_CLIENT_SECRET=accelbyte-iam-client-secret
AB_USERNAME=accelbyte-username-or-email
AB_PASSWORD=accelbyte-account-password
AB_BASE_URL=https://development.accelbyte.io
```

Those variables can be exported in current terminal or saved as `.env` file 
in current directory where the command will be run

### Getting Docker Credentials

Get credentials and immediately login to docker.
Note that the username for login is always `AWS` and
the repository url is obtained from extend app details page when creating the app in Admin Portal.

```shell
# using option -p to print only password and pipe it to docker login
extend-helper-cli dockerlogin --namespace my-game --app chatfilter01 -p | docker login -u AWS --password-stdin 342674635073.dkr.ecr.us-west-2.amazonaws.com
```

or, get docker credentials and print on terminal to be used later.

```shell
# get docker login credentials, if successful will printout json containing
# docker username, password, repository url
extend-helper-cli dockerlogin --namespace my-game --app chatfilter01
```

### Push Image

After successfully login you can build and tag the image based on the repository 
url path obtained from extend app details page in Admin Portal
then push to repository.

Example, in this case we have repository path for extend app 'chatfilter01'': `342674635073.dkr.ecr.us-west-2.amazonaws.com/accelbyte/justice/development/extend/my-game/chatfilter01`

```shell
# build new image
docker build -t 342674635073.dkr.ecr.us-west-2.amazonaws.com/accelbyte/justice/development/extend/my-game/chatfilter01:v1.0.0 .

# push the image
docker push -t 342674635073.dkr.ecr.us-west-2.amazonaws.com/accelbyte/justice/development/extend/my-game/chatfilter01:v1.0.0
```