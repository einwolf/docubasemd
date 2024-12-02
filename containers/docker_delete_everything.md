# Docker delete everything

```bash
# Stop all containers
docker stop $(docker ps -qa)

# Remove all containers
docker rm $(docker ps -qa)

# Remove all images
docker rmi -f $(docker images -qa)

# Remove all volumes
docker volume rm $(docker volume ls -q)

# Remove all networks
docker network rm $(docker network ls -q)

# The following should not show any items
# docker ps -a
# docker images -a 
# docker volume ls

# The following should show only default networks
# docker network ls
```

```bash
# copy paste version
docker stop $(docker ps -qa)
docker rm $(docker ps -qa)
docker rmi -f $(docker images -qa)
docker volume rm $(docker volume ls -q)
docker network rm $(docker network ls -q)
docker system prune -f -a
```
