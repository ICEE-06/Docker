
## Etape1: Obtenir l'image docker de Haproxy

```
docker pull haproxy
```


## Etape2: Création du fichier de configuration

```
mkdir hap_conf
cd hap_conf
nano hap_conf.cfg
```

```
#---------------------------------------------------------------------
# Process global settings
#---------------------------------------------------------------------
global
  stats socket /var/run/hapee-3.1/hapee-lb.sock user hapee-lb group hapee mode 660 level admin
  log stdout format raw local0 info

#---------------------------------------------------------------------
# Common defaults that the 'backend' section will
# use if not designated in their block
#---------------------------------------------------------------------
defaults
  mode               http
  log                global
  timeout connect    10s
  timeout client     300s
  timeout server     300s

#---------------------------------------------------------------------
# main frontend which forwards to the backend
#---------------------------------------------------------------------
frontend  fe_main
  bind :80           # direct HTTP access
  default_backend    web_servers

#---------------------------------------------------------------------
# default round-robin balancing in the backend
#---------------------------------------------------------------------
backend web_servers
  balance          roundrobin
  server s1        172.16.0.11:80 check
  server s2        172.16.0.12:80 check


```


## Etape3: Création des fichiers index.html pour chaque serveur web

```
cd ..
mkdir public-html-web1
cd public-html-web1
nano index.html
```

```
<html><body><h1>It works! Web server 1 received your request this time.</h1></body></html>
```


```
cd ..
mkdir public-html-web2
cd public-html-web2
nano index.html
```

```
<html><body><h1>It works! Web server 2 received your request this time.</h1></body></html>
```


## Docker compose

créer un fichier `docker-compose.yml` à la racine du projet

```
---
networks:
  my_custom_network:
    driver: bridge
    ipam:
      config:
        - subnet: 172.16.0.0/16
          gateway: 172.16.0.1



services:
  hapee-3.1:
    image: haproxy
    ports:
      - "80:80"
    volumes:
      - "./hap_conf:/etc/hap_conf"
    networks:
      my_custom_network:
        ipv4_address: 172.16.0.10
    container_name:
        hapee-3.1
  web1:
    image: httpd
    volumes:
      - "./public-html-web1:/usr/local/apache2/htdocs/"
    networks:
      my_custom_network:
        ipv4_address: 172.16.0.11
    container_name:
        web1
  web2:
    image: httpd
    volumes:
      - "./public-html-web2:/usr/local/apache2/htdocs/"
    networks:
      my_custom_network:
        ipv4_address: 172.16.0.12
    container_name:
        web2


```

démarrer le **docker compose**
```
docker-compose up -d --build
```


## Etape4: Envoyez le trafic de demande vers des serveurs Web via un équilibreur de charge Haproxy

Taper dans le navigateur : `http://localhost:80`