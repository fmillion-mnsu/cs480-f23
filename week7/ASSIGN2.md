# Sample for Deploying a Website

## Dockerfile

    FROM nginx
    COPY index.html /usr/share/nginx/html/

### build command

    docker build -t web_image .

## docker-compose.yml

    version: '3'

    services:
      webserver:
        image: group1-week7
        labels:
        - host=group1-week7

    networks:
      default:
        name: cs480
        external: true

## GitHub workflow

    name: Build and deploy container
    on: 
      - push
      - workflow_dispatch

    jobs:
      build-container:
        runs-on: ["self-hosted","docker"]
        name: Build the docker container
        steps:
          - name: Checkout code
            uses: actions/checkout@v3
          - name: Build the dockerfile
            run: docker build -t week7-group1 .
          - name: Run the deployment action
            uses: fmillion-mnsu-ga-test/action-test@v1