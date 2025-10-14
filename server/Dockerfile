#utiliser une image Node légére 
FROM node:lts-alpine

#définir le répertoire de travail dans le container
WORKDIR /app

#copier les fichiers package.json et package-lock.json dans le répertoire de travail
COPY package*.json ./

#installer les dépendances
RUN npm install

#copier le reste des fichiers de l'application dans le répertoire de travail
COPY . .

#exposer le port 9000
EXPOSE 9000

# definir la variable d'environnement pour MongoDB
ENV MONGODB_URI=mongodb://mongo:27017/mern_db

#demarrer l'application
CMD ["npm", "start"]