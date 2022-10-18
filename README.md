# TP part 01 - Docker | Léa PEISSELLON BERRUTI

## Introduction
L'objectif de ce TP est de prendre en main l'environnement Docker et plus si affinitée !

Le but de ce TP est de créer trois conteneurs :

- Un serveur Web : Apache
- Un serveur backend avec une API : Java
- Une base de données : PostgreSQL
- Un client pour la base de données : adminer


Les notions suivantes seront abordées : 

- Création d'un image personalisée avec Dockerfile
- L'exposition des ports d'un container
- Le mappage des ports d'un container
- Le mappage de volume pour bénéficier de la persistance
- L'utilisation d'entrypoint (mis à disposition par l'image)
- L'utilisation de docker-compose
- La configuration très basique d'un reverse proxy

## Base de donnée 

### Build de l'image

On build notre image :

```docker
docker build -t database .
```

* ```-t database``` : permet de spécifier un tag à l'image 
* ```.``` : spécifie le contexte
* ```-f Database.Dockerfile``` : permet de spécifier le nom du Dockerfile si le nom par défaut n'est pas utilisé

### Lancement d'un conteneur

Ensuite on run / exécute notre image :

```docker
docker run --rm \
    --name database \
    --env-file .env \
    # -p 5432:5432 \  Facultatif -> Utile si on souhaite exposer la BDD, dans notre cas non --> seulement à d'autre conteneur présent sur la même machine --> la solution : utilisé le nom du contenur directement !
    --network=bridge-app-network \ 
    database
```
Il faut avoir au préalable crée le network -> ```docker network create app-network```

Explication des paramètres :
* ```--rm``` : effectue un clean-up du container quand il est arrêté.
  * Càd : Docker supprime automatiquement le container (et tout ce qui est lié à celui-ci) quand il est arrêté et les volumes anonymes associés (équivalent de ```docker rm -v database```).
  * A noter : Pas utilisé en prod mais pratique pour tester/accomplir quelque chose dans un temps réduit -> tester, compiler une application au sein d'un conteneur, vérifier un bon fonctionnement et libérer de l'espace une fois fini.
  
* ```--name``` : permet de nommer le container pour l'identifier, par ex : utiliser son nom pour le lier à d'autres applications.
* ```-env-file``` : permet de spécifier le fichier qui contient les variables d'environnment.
  * A noter : on peut aussi spécifier directement des variables d'environement avec ```-e VAR1=TOTO``` (alias de ```-env```) mais cela devient fastidieux si l'on a beaucoup de variables d'environnement.
* ```-p``` : permet de mapper un ou plusieurs port de la machine hôte avec le conteneur
* ```-v``` : permet de mapper un ou plusieurs volume de la machine hôte avec le conteneur (pour bénéficier de données persistante -> la base de données ne sera pas vide à chaque redémarrage).


Ajouter adminer (facultatif) : 
```
docker run --network=app-network --link database:db -p 8081:8080 adminer
```
## Application Java

## Build de l'image

On build notre image :

```docker
docker build -t backend .
```
On lance le container:
```
docker run --network=app-network -p 8080:8080 --name backend_app backend
```

## Reverse-Proxy

On fait exactement la même chose que pour les étapes d'avant on build et on run l'image.

# Docker-Compose pour les rassembler tous !

Docker-Compose est un super outil pour configurer/définir/désigner des applications docker avec plusieurs conteneurs.
Celui-ci est au format YAML.

* Voici la documentation : https://docs.docker.com/compose/

Pour créer et lancer les conteneurs : ```docker-compose up``` et ajouter ```-d``` pour lancer en arrière plan

Pour arrêter et supprimer l'ensemble des éléments (volumes, netorks, containers, images) : ```docker-compose down```


