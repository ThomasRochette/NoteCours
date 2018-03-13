# DIA

---

---
## [Docker](http://cours.sewatech.fr/docker/slides/#2)
---
### Fonctionnement

Docker créer une image docker dans un conteneur docker avec toutes les dépendances nécessaire pour l'application.

#### Architecture
1 application = 1 conteneur

**Virtual Machine vs Docker :**
Docker tourne directement sur le système machine au lieu de tourner sur le système d'exploitation

#### En développement

Peut être utiliser pour un service tiers (ex : base de données) permet de ne pas poluer l'environnement avec les dépendances.

#### Ecosystème

Docker engine le reste sont des des outils proposés par la société docker pour aller autour.

### Premiers pas

#### Installation

Voir sur le site de [docker](https://www.docker.com/)

Sous windows : windows containers reproduit des conteneurs.

Verification de la version :

    docker version

Donne la version du client et du serveur

### Conteneurs

#### Démarrer un conteneurs

Télécharger depuis Docker Hub
<!-- sudo usermod -a -G docker $USER -->

    docker image pull hello-world

Démarrer

    docker container run hello-world

#### Run interactif

    docker container run -it debian

Le  -it permet de démarrer un terminal interactif (Iteractive Terminal = it) sans, le conteneur s'arrête instantanément. Il est possible de lancer directement une commande après le debian. La dernière commande lance le bash de debian par défaut. Autre exemple :

    docker container run -it debian echo "hello world"

#### Run en background
D'autres option après le run peuvent être utiles comme -d qui permet de faire tourner en background.

    docker container run -d httpd

#### Lister les conteneurs

Pour avoir la liste des conteneurs

    docker container ls -a

Le ls donne les conteneurs démarés le -a donne tout les conteneurs.

#### Arrêt et supression des conteneurs

Pour arrêter un conteneur 2 possibilités :

    docker container stop
    docker container kill

Suivi du nom du conteneur ou de l'id du conteneur.

Supprimer un conteneur

    docker containeur rm $(docker container ls -f status=exited)
    docker container prune

### Images

#### Build

Construction :
Nécessite un Dockerfile

    docker image build -t sw/app .

Image locale

    docker image run -d sw/app

#### Gérer les Images

Liste des Images

    docker image ls

Supprimer une image

    docker image rm
    docker image prune    // pour les images sans tags
    docker image prune -a // pour prendre aussi les images avec tags

### Réseau

#### Exposition de ports

association explicite :

    docker container run -p 8888:80 httpd

#### Réseaux personalisés

    docker network create frontend
    docker container run -d --net=frontend --name web sewatech/httpd
    docker container run -d --net=frontend --name app sewatech/tomcat

### Volume

#### Partage de répertoire

    docker container run -d -v ${PWD}/web-content:/usr/local/apache2/htdocs --name web httpd

#### Volumes nommés

    docker container run -it -v data-vol:/data --name cli debian // nom choisi
    docker container run -it -v /data --name cli debian          // nom généré

#### Création de Volume

    docker volume create --name data-vol

### Multi Conteneurs

Docker compose s'utilise avec python.
