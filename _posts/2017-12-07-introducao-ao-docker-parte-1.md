---
layout: post
title: 'Introdução ao Docker - Parte 1'
description: Uma breve introdução ao Docker de como executar e criar seu primeiro container.
categories: docker
author: João Maia
---

Olá pessoal,

esse post é focado em pessoas que não tem experiência nenhuma em Docker ou equivalentes. A ideia é mostrar de forma bem simples os conceitos e dar os passos básicos para quem quer começar a usar Docker no seu dia a dia. Uma primeira coisa que precisamos saber que Docker **NÃO** é uma forma de virtualização. Na virtualização todo um novo sistema é criado, já no Docker existe um compartilhamento de recursos a serem comentados melhor no restante do post.

# Instalando

Primeiramente, você deve instalar o Docker Host, recomendo que siga a documentação oficial de instalação da versão [Community Edition](https://docs.docker.com/engine/installation/). Se você estiver no Linux recomendo [esses passos](https://docs.docker.com/engine/installation/linux/linux-postinstall/#manage-docker-as-a-non-root-user) após a instalação.

# Primeiros comandos

## Listando imagens

```
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
```

Como a instalação é nova não temos nenhuma imagem ainda.

## Obtendo a sua primeira imagem

Vamos fazer o download de uma imagem do Python com a versão 3.6.3, você pode conferir outras versões de imagens do Python [aqui](https://hub.docker.com/_/python/).

```
$ docker pull python:3.6.3
3.6.3: Pulling from library/python
85b1f47fba49: Pull complete 
ba6bd283713a: Pull complete 
817c8cd48a09: Pull complete 
47cc0ed96dc3: Pull complete 
4a36819a59dc: Pull complete 
db9a0221399f: Pull complete 
7a511a7689b6: Pull complete 
1223757f6914: Pull complete 
Digest: sha256:db9d8546f3ff74e96702abe0a78a0e0454df6ea898de8f124feba81deea416d7
Status: Downloaded newer image for python:3.6.3
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
python              3.6.3               79e1dc9af1c1        4 weeks ago         691MB
```

## Executando o Docker

Vamos rodar essa imagem com o HTTP Server padrão do Python em background para não bloquear o shell na porta 8080.

```
$ docker run -d python:3.6.3 python -m http.server 8080
325ecd181bbcbdcd96f093f1fa7a39f0d1d3986cb5761093480b508a4782c962
```

Entendendo o comando executado: `docker run [OPTIONS] IMAGE [COMMAND] [ARG...]`:

* No caso temos apenas uma `OPTIONS` que é o `-d` de `detach` que executa o Docker em background;
* A `IMAGE` no caso é o python:3.6.3 que fizemos o download no passo anterior; e
* Nosso comando que é `python -m http.server 8080` para executar o servidor HTTP.

O `325ecd181bbcbdcd96f093f1fa7a39f0d1d3986cb5761093480b508a4782c962` na saída do comando representa o ID do Docker executado.

Observações, a opção `-d` poderia não existir e o Docker teria executado travando o shell. Outra coisa, poderia ser passsada a opção `--rm` que ao fim da execução do Docker a imagem seria automaticamente removida.

## Observando o status do Docker Host

Executando um novo Docker e verificando o status:

```
$ docker run -d python:3.6.3 python -m http.server 8081
4948aa0ce505310fe892bcc5f4bd84670cc898fa9950d81ce2f115c80fc5a717
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
4948aa0ce505        python:3.6.3        "python -m http.se..."   11 seconds ago      Up 9 seconds                            upbeat_johnson
```

Entendendo a saída do `docker ps`:

* `CONTAINER ID`: ID do Docker em execução;
* `IMAGE`: imagem usada na execução do Docker;
* `COMMAND`: comando executado no Docker;
* `CREATED`: tempo de criação do Docker;
* `STATUS`: status do Docker;
* `PORTS`: portas exportadas do Docker; e
* `NAMES`: apelido do Docker em execução.

## Inspecionando em detalhes o Docker

Com seu Docker já executando você em algum momento vai ter interesse em monitorar o uso de recursos do mesmo. Então, para conseguir esse tipo de informação basta utilizar o comando `stats`, veja o exemplo:

```
$ docker stats 4948aa0ce505
CONTAINER           CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
4948aa0ce505        0.03%               11.47MiB / 7.663GiB   0.15%               16.5kB / 0B         0B / 0B             1
```

Como podemos ver é bem simples, temos informações de uso de CPU, memória, I/O de rede e disco. Repare que no `stats` passei como argumento parte do hash que identifica o Docker, semelhante ao git.

Outras informações podem ser inspecionadas do seu Docker, existe um metadado que é utilizado para executar o seu Docker, vou mostrar aqui um exemplo simplificado com apenas alguns dados, por exemplo, ID do Docker, data de criação, comando utilizado, status e IP.

```
$ docker inspect upbeat_johnson 
[
    {
        "Id": "4948aa0ce505310fe892bcc5f4bd84670cc898fa9950d81ce2f115c80fc5a717",
        "Created": "2017-12-06T02:29:44.83358631Z",
        "Path": "python",
        "Args": [
            "-m",
            "http.server",
            "8081"
        ],
        "State": {
            "Status": "running",
            ...
        },
        "Image": "sha256:79e1dc9af1c1d276a3cae7c1c56290f2101c0aedbf70cbbb322e1a6b16149bd5",
        ...
        "Name": "/upbeat_johnson",
        ...
        "HostConfig": {
            ...
        },
        "GraphDriver": {
           ...
        },
        "Mounts": [],
        "Config": {
            "Hostname": "4948aa0ce505",
            ...
            "Cmd": [
                "python",
                "-m",
                "http.server",
                "8081"
            ],
            "Image": "python:3.6.3",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {}
        },
        "NetworkSettings": {
            ...
            "Gateway": "172.17.0.1",
            "IPAddress": "172.17.0.2",
            "Networks": {
                "bridge": {
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    ...
                }
            }
        }
    }
]
```

## Finalizando um Docker

Uma hora será preciso desligar o Docker executado, para isso  existe o `kill` que vai encerrar o Docker via `signal` aKa `Ctrl+C`.

```
$ docker kill upbeat_johnson 
upbeat_johnson
$ docker ps 
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                        PORTS               NAMES
4948aa0ce505        python:3.6.3        "python -m http.se..."   19 minutes ago      Exited (137) 19 seconds ago                       upbeat_johnson
```

Perceba que o Docker ainda existe no seu sistema pois foi inicializado sem o `--rm`.

## Apagando Docker finalizados

Caso você não tenha mais interesse em algum Docker finalizado que não tenha sido removido, existe o comando `rm` que deleta esses Dockers mortos, veja:

```
$ docker rm upbeat_johnson 
upbeat_johnson
=> ~/Workspace/github/Dev-ES/blog
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

Esse comando pode ser muito útil um dia em que derepente o espaço em disco do seu computador acabar misteriosamente.

Dica para deletar todos os Dockers finalizados: 

```
docker rm `docker ps -aq -f status=exited`
```

# Deletando uma imagem

Para deletar uma imagem uma condição precisa ser respeitada, não podem existir Dockers sendo executados ou mortos com essa imagem. Tendo essas condições atendidads basta usar o comando `rmi` para deletar a imagem:

```
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
python              3.6.3               79e1dc9af1c1        4 weeks ago         691MB
$ docker rmi python:3.6.3
Untagged: python:3.6.3
Untagged: python@sha256:db9d8546f3ff74e96702abe0a78a0e0454df6ea898de8f124feba81deea416d7
Deleted: sha256:79e1dc9af1c1d276a3cae7c1c56290f2101c0aedbf70cbbb322e1a6b16149bd5
Deleted: sha256:e8d69b2276bdf8f07439fd98bf4024e0ebe90ff39cc6f9b07322428a2350fad6
Deleted: sha256:fcc590344cd33a06a55bfb1bcbca58ba93fd33705f62ab6603c0a6ee052d0380
Deleted: sha256:88d6163c63717c1f8227085b97d84f2ea6e20c9a5f53a8a85a50fd09fe963155
Deleted: sha256:b7529f573c93b914569a5c7ff1627cdede25bd531dc09772298240e48eedd200
Deleted: sha256:fdf92e76f2ef13c28b6318ac6183b785179469c4e86b2239b967153b3c321c4d
Deleted: sha256:f61fd8cdf87b657eb06f872da96f835feb670d98e22a3460ddca702f330f1cf7
Deleted: sha256:25072a4e50193a8d2dc199c08cbb2446630294a03e1791e3c2a84fb932cc8d97
Deleted: sha256:c01c63c6823dd8a296c2134ea94d31121069f1c52f8f8789836bc7ea2e5cdc93
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
```

# Considerações finais

Espero que você agora consiga entender e executar os conceitos básicos de Docker e aplicar no seu dia.

Abraços, João
