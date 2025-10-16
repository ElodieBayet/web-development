# Docker
# Sample Dockerfile : Ghost-CMS 
# From : https://github.com/OpenClassrooms-Student-Center/ghost-cms

# Image de base
FROM debian:9

# Commandes à exécuter
RUN apt-get update -yq \
   && apt-get install curl gnupg -yq \
   && curl -sL https://deb.nodesource.com/setup_10.x | bash \
   && apt-get install nodejs -yq \
   && apt-get clean -y

# Copier / Télécharger les fichiers vers un dossier dans l'image
ADD . /app/

# Changer de répertoire courant - Équivalent au 'cd'
WORKDIR /app

# Une nouvelle commande à exécuter
RUN npm install

# Définition du port d'écoute et du répertoire pour le Host
EXPOSE 2368
VOLUME /app/logs

# Lancement de l'application
CMD npm run start