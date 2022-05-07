FROM debian:jessie


RUN apt-get update && apt-get install -y \
    nginx



RUN usermod -u 1000 www-data
COPY /app/*.* /var/www/mts/
RUN touch /var/www/mts/.env
CMD ["nginx", "-g", "daemon off;"]


EXPOSE 80
EXPOSE 443