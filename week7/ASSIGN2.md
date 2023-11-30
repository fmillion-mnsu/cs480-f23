# Sample for Deploying a Website

## Dockerfile

    FROM nginx
    COPY index.html /usr/share/nginx/html

### build command

    docker build -t web_image .

## docker-compose.yml

    version: '3'

    services:
      webserver:
        image: web_image
        labels:
        - host=group1-week7

    networks:
      default:
        name: cs480
        external: true