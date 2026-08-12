``` ### Gestión de imágenes

docker build -t nombre-imagen .        → construye una imagen a partir del Dockerfile (el punto = "usa el Dockerfile de esta carpeta")
docker images                          → lista todas las imágenes que tienes en local
docker rmi nombre-imagen               → elimina una imagen
docker pull nombre-imagen              → descarga una imagen desde un registry
docker push nombre-imagen              → sube una imagen a un registry
docker tag origen destino              → renombra/etiqueta una imagen (ej. antes de subirla a un registry)

### Gestión de contenedores

docker run nombre-imagen               → crea y arranca un contenedor a partir de una imagen
docker run -d nombre-imagen            → lo arranca "en segundo plano" (detached)
docker run -p 8080:80 nombre-imagen    → mapea puertos (host:contenedor)
docker run -it nombre-imagen bash      → arranca el contenedor en modo interactivo (te mete dentro con una terminal)
docker ps                              → lista contenedores EN EJECUCIÓN
docker ps -a                           → lista TODOS los contenedores (también parados)
docker stop id_contenedor              → detiene un contenedor
docker start id_contenedor             → reinicia un contenedor detenido
docker restart id_contenedor           → reinicia un contenedor
docker rm id_contenedor                → elimina un contenedor
docker logs id_contenedor              → muestra los logs/salida del contenedor
docker exec -it id_contenedor bash     → entra dentro de un contenedor que ya está corriendo (para inspeccionar)

### Docker compose (varios contenedores)

docker-compose up                      → levanta todos los servicios definidos
docker-compose up -d                   → lo mismo, en segundo plano
docker-compose down                    → para y elimina todos los servicios
docker-compose logs                    → logs de todos los servicios
docker-compose ps                      → estado de los servicios

### Limpieza - mantenimiento

docker system prune  -> limpia contenedores o imagenes no usadas
docker volume ls       -> lista volumenes (almacenamiento persistente)
docker network ls       -> lista redes de Docker
´´´ 

### Comandos más usados
docker build -t mi-app .
docker run mi-app
docker ps
docker logs id_contenedor
<<<<<<< HEAD

```
=======
>>>>>>> 379b8a7cf678e82fca2186f0e8372562eb74072e
