# Stage 1: Build and Test
# Pourquoi le multi-stage build est pertinent ? 
# 1. Sécurité : L'image finale ne contient pas les outils de build (compilateurs, etc.) ni les tests.
# 2. Taille d'image : On passe d'une image "heavy" (Debian) à une image "alpine" ultra légère.
FROM node:20 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
# On execute les tests ici pour faire échouer le build si les tests ne passent pas
RUN npm test

# Stage 2: Production
FROM node:20-alpine
WORKDIR /app
# On ne copie que les dépendances de production et le code source de l'étape builder
COPY --from=builder /app/package*.json ./
RUN npm install --only=production
COPY --from=builder /app/server.js .
EXPOSE 3000
CMD ["npm", "start"]
