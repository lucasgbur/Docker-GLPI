# Docker-GLPI
Aplicação GLPI
Aplicação Cron

#Dentro da pasta raiz execute:
docker build -t glpi-docker .

#entrar na pasta Cron e execute: 
docker build -t glpi-cron .

#Criando volume do glpi /var: 
docker volume create --name glpi

#Criando container glpi: 
docker run -it -d -v glpi:/var/www/html/glpi -p 443:443 --name glpi-docker glpi-docker

#Ultimo comando: 
docker run -it -d --name glpi-cron --link glpi-docker --volume glpi:/var/www/html/glpi glpi-cron

#Start Cron: 
docker exec -i -t glpi-cron /tmp/startcron.sh

Obs: Caso utilize a portaa 443, crie um certificado e adicione no diretorio certificados (necessita do arquivo .crt e .key dentro da pasta.
como criar um certificado: https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-apache-in-ubuntu-20-04-pt

------------------------------------------------

Comandos Uteis

#Entrar no bash do docker:
docker exec -i -t (NOME DO CONTAINER) /bin/bash

#Parar container: 
Docker stop (NOME DO CONTAINER)

#remover container: 
Docker rm (NOME DO CONTAINER)

#Matar todos os containers ativos: 
docker kill $(docker ps -q)

#Status Docker: 
docker stats (Nome do container)

#atualizando total de memoria e swap do container: 
docker container update -m 5gb --cpus=1 --memory-swap 5000000000 glpi-docker

####CUIDADO#####
#Remover todas as imagens buildadas: 
docker system prune --all --force --volumes


#########################################################################################################################################

BD MYSQL Docker - Opcional

#Execute para usar a imagem do mysql e configurar manualmente
docker run -it -d --name glpi-mysql --link glpi-docker mysql/mysql-server

OU

#execute: 
docker run --name nome-do-container  \
-p 3306:3306 -p 33060:33060  \
-e MYSQL_ROOT_HOST='%' -e MYSQL_ROOT_PASSWORD='sua-senha'   \
-d mysql/mysql-server:latest

docker exec -it nome-do-container mysql -uroot -psua-senha

-------------------------------------------------

caso for configurar manualmente o mysql, deve verificar os logs do mysql
Comando> sudo docker logs glpi-mysql

[Entrypoint] GENERATED ROOT PASSWORD: (SENHA DO BD)

----------

Criando usuario manualmente no BD
#Opcional

create user 'USER'@'%' identified by 'SENHA';
OU
create user 'USER'@'localhost' identified by 'SENHA';
GRANT SELECT,CREATE VIEW,SHOW VIEW ON *.* TO 'USER'@'%';
OU
GRANT SELECT,CREATE VIEW,SHOW VIEW ON *.* TO 'USER'@'localhost';
flush privileges;


