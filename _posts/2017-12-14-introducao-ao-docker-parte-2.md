---
layout: post
title: 'Introdução ao Docker - Parte 2'
description: Uma breve introdução ao Docker de como executar e criar seu primeiro container.
categories: docker
author: João Maia
---

Olá pessoal,

nesse post vamos abordar 3 tópicos com um exemplo prático de criar um Docker rodando o MySQL: 1) variáveis de ambiente; 2) exportar portas; e 3) volumes.

Usaremos a seguinte [imagem](https://hub.docker.com/_/mysql/) do MySQL no Docker Hub para usar nesse caso.  

Nesse tutorial vamos usar a versão 5 do Docker:

```
$ docker pull mysql:5
5: Pulling from library/mysql
f49cf87b52c1: Pull complete 
78032de49d65: Pull complete 
837546b20bc4: Pull complete 
9b8316af6cc6: Pull complete 
1056cf29b9f1: Pull complete 
86f3913b029a: Pull complete 
4cbbfc9aebab: Pull complete 
8ffd0352f6a8: Pull complete 
45d90f823f97: Pull complete 
ca2a791aeb35: Pull complete 
Digest: sha256:1f95a2ba07ea2ee2800ec8ce3b5370ed4754b0a71d9d11c0c35c934e9708dcf1
Status: Downloaded newer image for mysql:5
```

# Variáveis de ambiente

Na página do Docker Hub tem um tópico `Environment Variables` onde existe o nome e explicação de todas as varáveis que a imagem do MySQL suporta e sua função. Nesse primeira parte vamos usar apenas a `MYSQL_ROOT_PASSWORD` que define a senha do administrador do MySQL. Vamos ver como funciona na prática:

```
$ docker run --rm -d -e MYSQL_ROOT_PASSWORD=secret mysql:5
f5c149956eb1d48e46980fa7f554f02f6bbde88017b921733d8fd4c48fb12f13
$ docker ps 
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
f5c149956eb1        mysql:5             "docker-entrypoint..."   11 seconds ago      Up 9 seconds        3306/tcp            hopeful_hawking
```

Validando se a senha foi realmente configurada:

```
$ docker exec -it hopeful_hawking mysql -u root -psecret
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 4
Server version: 5.7.20 MySQL Community Server (GPL)

Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```

Observação, como eu não tenho o cliente do MySQL na minha máquina e seria complicado ver qual o IP do Docker também para fazer o acesso remoto aproveitei de um recurso do Docker de executar um comando em uma imagem em execução. `docker exec -it <DOCKER ID> <COMANDO>` a opção `-it` é para possibilidar a criação do shell virtual do seu Docker.

# Exportando portas

Sabemos que o MySQL utiliza a porta tcp/3306 para os clientes se conectarem e gostariamos de expor essa porta de alguma forma, bom, exitem 2 formas:

* exportar para uma porta randômica no host; e
* exportar para uma porta específica do host.

## Porta randômica

```
$ docker run --rm -d -e MYSQL_ROOT_PASSWORD=secret -p 3306 mysql:5
491faaed037cfabea746a1c25485c8ab2449a1137efdca41559ce41ff70df37d
$ docker ps 
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                     NAMES
491faaed037c        mysql:5             "docker-entrypoint..."   3 seconds ago       Up 1 second         0.0.0.0:32768->3306/tcp   tender_golick
```

O `-P` mapeia todas as portas conhecidas do serviço, ou seja que foi documentado pelo fornecedor da imagem e expoe em porta aleatoria.

## Porta específica

```
$ docker run --rm -d -e MYSQL_ROOT_PASSWORD=secret -p 3306:3306 mysql:5
2529472e6a6e7634bf3011f29af61d352f3cb4301b8a719026e21a4104996918
=> ~/Workspace/tmp/mysql
$ docker ps 
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
2529472e6a6e        mysql:5             "docker-entrypoint..."   3 seconds ago       Up 2 seconds        0.0.0.0:3306->3306/tcp   cranky_wescoff
```

Com a sintaxe de `PORTA:PORTA` eu consigo dizer a porta de ambos os lados que vão se conectar, o valor da esquerda é o Docker Host e o da direita do seu Docker.

# Trabalhando com volumes

Não comentei nos exemplos anteriores mas todas as vezes que desliguei o Docker eu acabei perdendo todos os dados por conta do `--rm`. Por sorte, não estou usando eles para nada crítico então não perdi nada. Imagine agora que você tenha feito a mesma coisa no seu ambiente de produção na AWS, por exemplo, e por algum motivo a instância tenha sido reiniciada? Seus dados teriam todos sidos perdidos. Esse é um comportamento que não gostariamos de ter no nosso ambiente de produção.

Trabalhar com volumes vem para resolver esse problema, podemos criar um ambiente de armazenamento compartilhado entre o Docker e o Docker Host. No caso do MySQL, por padrão, ele salva todos os dados na pasta `/var/lib/mysql`. Então, será ela que vamos ter que criar o compartilhamento com o Docker Host.

```
$ docker run --rm -d -e MYSQL_ROOT_PASSWORD=secret -p 3306:3306 -v $PWD/data:/var/lib/mysql mysql:5
acc534026e02b97c6e422ba811cc15b109630d07889254c0ccfdb767c0a35da1
=> ~/Workspace/tmp/mysql
$ ls data/
auto.cnf  ib_buffer_pool  ibdata1  ib_logfile0	ib_logfile1  ibtmp1  mysql  performance_schema	sys
=> ~/Workspace/tmp/mysql
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
acc534026e02        mysql:5             "docker-entrypoint..."   6 seconds ago       Up 6 seconds        0.0.0.0:3306->3306/tcp   brave_leakey
```

Por mais que eu desligue o Docker os dados estarão lá depois:

```
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
acc534026e02        mysql:5             "docker-entrypoint..."   6 minutes ago       Up 6 minutes        0.0.0.0:3306->3306/tcp   brave_leakey
=> ~/Workspace/tmp/mysql
$ docker kill brave_leakey 
brave_leakey
=> ~/Workspace/tmp/mysql
$ ls data/
auto.cnf    ca.pem	     client-key.pem  ibdata1	  ib_logfile1  mysql		   private_key.pem  server-cert.pem  sys
ca-key.pem  client-cert.pem  ib_buffer_pool  ib_logfile0  ibtmp1       performance_schema  public_key.pem   server-key.pem
```

Assim como foi exportar a porta, o valor da esquerda na opção `-v` é para o caminho completo ou relativo no Docker Host onde vai ser feito o compartilhamento com o diretório no Docker que fica a direita do `:`.

Abraços, João
