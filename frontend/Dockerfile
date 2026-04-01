# Dockerfile Frontend
# Utilisation de nginx:stable-alpine pour une image légère et éprouvée
FROM nginx:stable-alpine
COPY index.html /usr/share/nginx/html/index.html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
