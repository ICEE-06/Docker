# Les définitions de bases

- **Conteneur Docker**: c'est une unité légère, portable, et isolé qui contient tout ce qu'il faut pour exécuter une application
- **Image Docker**: c'est un modèle figé qui permet de créer un ou plusieurs conteneur

# Docker compose :

- Avec **Docker-compose** , on peut executer plusieurs **container** en même temp.
- Le **Docker compose** utilise la syntaxe **YAML**. On peut utiliser les éxtension **.yml** ou **.yaml** .
- On crée un fichier nommé : **docker_copose.yml** :
```
#run
docker compose up -d

#suprimer
docker compose down
```


# Docker Swarm

- **Swarm** est un outil intégré à Docker à fin de **Gérer les container** (Travail d'orchestration)
- **Cluster swarm**: ensemble de minimum 2 machines docker!