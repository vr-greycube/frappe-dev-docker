


### Development env in Docker on Ubuntu
- update and  git , curl, vscode

```
sudo apt update && sudo apt upgrade && sudo apt autoremove

sudo apt install git -y
sudo apt install curl -y
sudo apt install gh -y
```




#### install vscode
https://code.visualstudio.com/docs/setup/linux 

Download [.deb package (64-bit)](https://go.microsoft.com/fwlink/?LinkID=760868)
sudo apt install ./<file>.deb 



### [setup ssh](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

```
ssh-keygen -t ed25519 -C "your_email@example.com"
eval "$(ssh-agent -s)"
```
- add ssh key.pub to github 



### Install docker-compose
https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-ubuntu-22-04
```
mkdir -p ~/.docker/cli-plugins/
curl -SL https://github.com/docker/compose/releases/download/v2.3.3/docker-compose-linux-x86_64 -o ~/.docker/cli-plugins/docker-compose
chmod +x ~/.docker/cli-plugins/docker-compose
docker compose version
```

Alternative reference [https://docs.oracle.com/en/learn/ol-podman-compose/#run-podman-compose](https://docs.oracle.com/en/learn/ol-podman-compose/#run-podman-compose)

### Install Podman (alternative for Docker)

- install Podman and Podman Desktop from flatpak
```
sudo apt-get -y install podman

sudo apt install -y podman-docker

-- verify podman
systemctl --user enable --now podman.socket
systemctl --user status podman.socket
podman info | grep -i remotesocket -A2

sudo apt install flatpak
flatpak remote-add --if-not-exists --user flathub https://flathub.org/repo/flathub.flatpakrepo

flatpak install --user flathub io.podman_desktop.PodmanDesktop
flatpak run io.podman_desktop.PodmanDesktop
```

- you may get a possible warning.. restart machine for Podman Desktop to appear in launcpad

```
'/var/lib/flatpak/exports/share'
'/home/vijay/.local/share/flatpak/exports/share'

are not in the search path set by the XDG_DATA_DIRS environment variable, so
applications installed by Flatpak may not appear on your desktop until the
session is restarted.

```

- you may get an error docker.sock not found. ignore it because docker is not installed



### GC frappe docker 
(based on frappe_docker https://github.com/frappe/frappe_docker)

```
git clone https://github.com/vr-greycube/custom_files.git frappe-dev
cd frappe-dev
```


#### Compose files and directory for each bench
- each folder contains the compose yaml and apps.json for that bench. the container will be named same as the folder
- create a new folder for every other bench you need
- the apps in apps.json are installed by the installer after creating bench

### Create container

```
docker compose -p version-15 up
```

- Once the container is up and running copy frappe container id from docker ps and connect into container to setup bench.. 

``` 
docker exec -it <containder-id> /bin/bash
# give permissions to frappe user to create bench
chmod -R 777 .
```

### Create bench and site
- installer.py is a util that 
    - creates bench with given params; by running bench init 
    - creates a site with given params; by running new-site

- Enter inside the container, cd into workspace dir and run installer commmand to create bench and site
``` 
docker exec -it <container-id> /bin/bash
cd /workspace
```

#### version 15
copy installer.py into version-15
``` 
$ python installer.py -j version-15/apps.json -b bench-15 -s demo15.localhost -r https://github.com/frappe/frappe -t version-15 \
-p 3.11.6 -n 18.18.2 -a 123 -d mariadb -m demo15
```

#### version 14
copy installer.py into version-14

``` 
$ python installer.py -j version-14/apps.json -b bench-14 -s demo14.localhost -r https://github.com/frappe/frappe -t version-14 \
-p 3.10.13 -n 16.6.0 -a 123 -d mariadb -m demo14
```


### Change docker compose to start bench automatically when container starts
- in compose.yaml file  comment the ```command: sleep infinity``` line and uncomment ```bash -c "cd bench-?? && bench start && cat"```
- do docker compose down and docker compose up from the host machine



### If using Docker instead of Podman

- install docker cli, docker desktop, docker compose
https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-20-04
https://docs.docker.com/desktop/install/ubuntu/

```
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
apt-cache policy docker-ce
sudo apt install docker-ce
sudo systemctl status docker
#
sudo usermod -aG docker ${USER}
su - ${USER}
```