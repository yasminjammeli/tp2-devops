# Étape 1 : Construire l'application React
FROM node:lts-alpine AS build

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Étape 2 : Servir les fichiers avec un serveur léger
FROM node:lts-alpine
WORKDIR /app

# Installer "serve" pour héberger les fichiers React
RUN npm install -g serve

# Copier les fichiers de build depuis l'étape précédente
COPY --from=build /app/build ./build

# Exposer le port 3000
EXPOSE 3000

# Lancer le serveur
CMD ["serve", "-s", "build", "-l", "3000"]
