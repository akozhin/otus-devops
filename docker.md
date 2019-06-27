# Docker
## Основное ДЗ
### Создание docker host

### Создание своего образа
### Работа с Docker Hub

## Полезные ссылки
- [gcloud config list](https://cloud.google.com/sdk/gcloud/reference/config/list)

## Дополнительные материалы
- [] [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)


## Docker machine

*docker-machine* - встроенный в докер инструмент для создания хостов и установки на них docker engine. Имеет поддержку облаков и систем виртуализации (Virtualbox, GCP и др.)
Команда создания - `docker-machine create <имя>`. Имен может быть много, переключение между ними через `eval $(docker-machine env <имя>)`. Переключение на локальный докер - `eval $(docker-machine env --unset)`. Удаление - `docker-machine rm <имя>`.

Все докер команды, которые запускаются в той же консоли после `eval $(docker-machineenv <имя>)` работают с удаленным докер демоном в GCP

## History
```bash
gcloud config list           
[core]
account = akozhingcp@gmail.com
disable_usage_reporting = False
project = docker-244419

Your active configuration is: [default]

Updates are available for some Cloud SDK components.  To install them,
please run:
  $ gcloud components update

#Создаем docker-host в GCP

export GOOGLE_PROJECT=docker-244419
docker-machine create --driver google \
    --google-machine-image https://www.googleapis.com/compute/v1/projects/ubuntu-os-cloud/global/images/family/ubuntu-1604-lts \
    --google-machine-type n1-standard-1 \
    --google-zone europe-west1-b \
    docker-host

Running pre-create checks...
(docker-host) Check that the project exists
(docker-host) Check if the instance already exists
Creating machine...
(docker-host) Generating SSH Key
(docker-host) Creating host...
(docker-host) Opening firewall ports
(docker-host) Creating instance
(docker-host) Waiting for Instance
(docker-host) Uploading SSH Key
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env docker-host

#Проверяем, что  docker-хост успешно создан
docker-machine ls          
NAME          ACTIVE   DRIVER       STATE     URL                        SWARM   DOCKER     ERRORS
default       -        virtualbox   Stopped                                      Unknown    
docker-host   -        google       Running   tcp://34.77.240.168:2376           v18.09.7

#Переключаемся на созданный хост
eval $(docker-machine env docker-host)

#Смотрим какие переменные установлены
printenv|grep "DOCKER"

DOCKER_TLS_VERIFY=1
DOCKER_HOST=tcp://34.77.240.168:2376
DOCKER_CERT_PATH=/Users/akozhin/.docker/machine/machines/docker-host
DOCKER_MACHINE_NAME=docker-host

#Создать правило firewall
gcloud compute firewall-rules create reddit-app \
 --allow tcp:9292 \
 --target-tags=docker-machine \
 --description="Allow connections" \
 --direction=INGRESS 
 

```