# WIK-DPS-TP03

On crée le fichier docker-compose.yaml, puis on configure l'api sous la version 3.8 de docker compose. On commence par le service de l'api que l'on va nommer web, que l'on va build avec Dockerfile.2 (multi-stage), sous 4 répliques, puis on va faire en sorte que les répliques redémarrent toujours, et on va créér la variable d'environnement PORT pour pouvoir exposer le port 8080 avant de limiter la connexon au réseau front-network avec :
version: '3.8'
services:
  web:
      build:
        context: .
        dockerfile: Dockerfile.2
      deploy:
        replicas: 4
      restart: always
      environment:
        - PORT= 8080
      networks:
        - front-network
  
On définit le service reverse-proxy, qui utilise comme image nginx sous sa dernière version, comme volume le fichier nginx.conf, sous le port 8081:80, qui dépend du service web(ne se lance pas tant que web n'est pas lancé), sous le réseau front-network avec :

  reverse-proxy:
    image: nginx:latest
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
    ports:
      - 8081:80
    depends_on:
      - web
    networks:
      - front-network 
      
Et enfin, on dfinit le service networks pour créer le réseau front-network avec 
networks:
  front-network:
    driver: bridge
