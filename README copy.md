﻿# Docker Notes

docker build -t nopcommerce-image .
docker run -d -p 9090:9090 -p 9091:9091 --name itso-web-1-ui nopcommerce-image:latest


docker build -t nopcommerce-image .
docker run -d -p 9090:9090 --name itso-web-1-ui nopcommerce-image

docker service scale itso-web-ui_itso-web-1=3
docker stack deploy -c itso-web-ui.docker-compose.yml pull
docker stack deploy -c itso-web-ui.docker-compose.yml itso-web-ui -d


# Need to Create docker registry of images
docker tag itso-web-1-image registry.gitlab.com/<your-namespace>/<your-repo>:latest





# Docker Compose File:


version: '3.8'

services:
  nopcommerce:
    # Use the image you built with your Dockerfile
    image: your-nopcommerce-image:latest
    ports:
      # Expose port 80 of the service to port 8080 on the host nodes
      - "8080:80"
    volumes:
      # Map named volumes to the directories that need to be persistent
      - app_data:/app/App_Data
      - bin:/app/bin
      - logs:/app/logs
      - plugins:/app/Plugins
      - www_bundles:/app/wwwroot/bundles
      - www_db_backups:/app/wwwroot/db_backups
      - www_files:/app/wwwroot/files
      - www_images:/app/wwwroot/images
    secrets:
      # This makes the connection string available as a file inside the container
      - source: db_connection_string
        target: /run/secrets/db_connection_string
    environment:
      # Example of how you might tell the app where to find the secret
      - NOP_CONNECTION_STRING_FILE=/run/secrets/db_connection_string
    deploy:
      replicas: 3
      restart_policy:
        condition: on-failure

  # You would also define your database service here
  # db:
  #   image: mcr.microsoft.com/mssql/server:2022-latest
  #   ... (configuration for your database service) ...

# Define the secrets to be created in the Swarm
secrets:
  db_connection_string:
    # In a real scenario, you'd create the secret beforehand
    # docker secret create db_connection_string my_connection_string_file.txt
    external: true

# Define the volumes that Swarm will manage
volumes:
  app_data:
  bin:
  logs:
  plugins:
  www_bundles:
  www_db_backups:
  www_files:
  www_images:






