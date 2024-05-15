#Notre fichier est construite en multi-stage pour la construction de notre image
#Partie 1: Nous allons construire le dossier build de notre appli frontend (dossier dist) qui aussi la code source de notre appli
FROM node:lts-alpine as build  
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build-only 

#Partie 2: Nous allons récupérer ce dossier constuit à la Partie 1 pour la positionner dans une image nginx. Et c'est cette image nginx que nous allons exécuter
#Dans cette image nginx, on aura la build de notre appli Vuejs(--from=build) et un fichier de configuration nginx.conf
FROM nginx:stable-alpine
COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD [ "nginx", "-g", "daemon off;" ]