
```bash
docker pull nginx
docker images

docker run -d -p 80:80 --name web nginx

docker ps
docker ps -a

docker logs web
docker exec -it web bash

docker stop web
docker start web
docker restart web

docker rm web
docker rmi nginx

docker volume ls
docker network ls

docker compose up -d
docker compose down

docker system prune -a
```