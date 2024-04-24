# EC2 Docker Deploy

# Usage

```yaml
- uses: cloudcoke/docker-deploy@v1
  with:
      # Name of docker image to use
      # For example: cloudcoke/sample-node:latest
      image: ""

      # Container port to use
      container-port: ""

      # Container name to use
      container-name: ""

      # Whether to use env file
      # Default: false
      # For example: true|false
      use-env-file: ""

      # Name of env file to use
      # Default: .env
      # For example: prod.env
      env-file-name: ""

      # SSH host address
      # For example: 8.8.8.8
      ssh-host: ""

      # SSH username
      # For example: ubuntu
      ssh-username: ""

      # Content of SSH private key
      ssh-private-key: ""

      # SSH port number
      # Default: 22
      ssh-port: ""
```

# Examples

## Default Deploy

```yaml
name: Deploy

on:
    push:
        branches: main

jobs:
    docker-deploy:
        runs-on: ubuntu-latest
        steps:
            - name: Docker EC2 Deployment
              uses: cloudcoke/docker-deploy@v1
              with:
                  image: cloudcoke/sample-node
                  container-port: ${{ secrets.IMAGE_PORT }}
                  container-name: ${{ secrets.CONTAINER_NAME }}
                  ssh-host: ${{ secrets.HOST }}
                  ssh-username: ${{ secrets.USERNAME }}
                  ssh-private-key: ${{ secrets.PRIVATE_KEY }}
```

## With ecr-docker-build-push

```yaml
name: Deploy

on:
    push:
        branches: main

jobs:
    docker-build-push-deploy:
        runs-on: ubuntu-latest
        steps:
            - name: Checkout Code
              uses: actions/checkout@v4

            - name: Docker Build and ECR Push
              uses: cloudcoke/ecr-docker-build-push@v1
              with:
                  aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
                  aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
                  repository: ${{ secrets.ECR_REPOSITORY }}

            - name: Docker EC2 Deployment
              uses: cloudcoke/docker-deploy@v1
              with:
                  image: ${{ env.docker_image}}
                  container-port: ${{ secrets.IMAGE_PORT }}
                  container-name: ${{ secrets.CONTAINER_NAME }}
                  ssh-host: ${{ secrets.HOST }}
                  ssh-username: ${{ secrets.USERNAME }}
                  ssh-private-key: ${{ secrets.PRIVATE_KEY }}
```

## Using Dot ENV File

```yaml
name: Deploy

on:
    push:
        branches: main

jobs:
    docker-deploy:
        runs-on: ubuntu-latest
        steps:
            - name: Make Dot ENV
              env:
                  ENV: ${{ secrets.ENV }}
              run: |
                  echo "${ENV}" > .env

            - name: Docker EC2 Deployment
              uses: cloudcoke/docker-deploy@v1
              with:
                  image: cloudcoke/sample-node
                  container-port: ${{ secrets.IMAGE_PORT }}
                  container-name: ${{ secrets.CONTAINER_NAME }}
                  use-env-file: true
                  ssh-host: ${{ secrets.HOST }}
                  ssh-username: ${{ secrets.USERNAME }}
                  ssh-private-key: ${{ secrets.PRIVATE_KEY }}
```
