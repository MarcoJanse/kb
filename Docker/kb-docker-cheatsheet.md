# Docker cheat sheet

## Basic commands

* Show running containers
  * `docker ps`
* List only stopped containers
  * `docker ps --filter "status=exited"`
* Show all docker containers
  * `docker ps --all`
* Run commands interactively in the container
  * `docker run -it IMAGE_ID`
* Check installed images
  * `docker images`
* Remove images
  * `docker rmi <IMAGE_ID>`
