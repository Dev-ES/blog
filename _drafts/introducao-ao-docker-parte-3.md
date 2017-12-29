---
layout: post
title: 'Introdução ao Docker - Parte 3'
description: Uma breve introdução ao Docker de como executar e criar seu primeiro container.
categories: docker
author: João Maia
---

Olá pessoal,

esse é o fim da introdução ao Docker e vamos terminar mostrando como criar o seu próprio Docker. Vamos exemplificar o processo subindo uma aplicação com Flask (Python) que só tem um endpoint e que é o raíz e respode o nome do host que executa a aplicação.

# Aplicação

Não é o foco aqui mas vou mostrar rapidamente como funciona a aplicação. O código fonte se encontra [aqui](https://github.com/jvrmaia/flask-docker-example).

```python
import socket
from flask import Flask
from decouple import config

app = Flask(__name__)

VERSION = config('VERSION', 'dev', cast=str)

@app.route("/")
def index():
    return socket.gethostname() + " - " + VERSION

if __name__ == '__main__':
    app.run(debug=True,host='0.0.0.0')
```

Basicamente, na raíz da aplicação retornamos o hostname e versão da aplicação e se a versão não for informada vai aparecer `dev`. Caso queira rodar local as instruções estão no README do repositório da aplicação.

# Dockerfile

O dockerfile é um documento de texto declarativo de construção da sua imagem Docker que será executado em ordem. Vamos ver o dockerfile do nosso projeto em questão:

```dockerfile
FROM python:3.6.3
MAINTAINER Joao Maia "joao@joaovrmaia.com"

COPY . /app

WORKDIR /app

RUN pip install -r requirements.txt

ENV VERSION=0.0.1

EXPOSE 5000

ENTRYPOINT ["python"]

CMD ["app.py"]
```

Vamos analisar cada item do Dockerfile:

### FROM

Essa instrução indica a partir de qual imagem a sua vai se basear, no nosso caso como vamos subir uma aplicação Python. Então, já peguei uma imagem com o Python instalado para evitar esse processo de instalação que pode ser muito complicado dependendo de qual imagem você usar como base. Imagina a complicação de pegar uma imagem crua e ter que instalar todas as dependências para instalar o Python, fazer o download do fonte, compilar e configurar as variáveis de ambiente.

Estou usando uma das imagens oficiais do Docker. Nota, sempre que possível use imagens oficiais do Docker, evite usar imagem qualquer por ai na internet e verifique sempre as imagens que você for utilizar. Nada impede que uma pessoa crie uma imagem com software malicioso e divulgue publicamente. Outra coisa importante, não publique suas imagens que possam conter informação privada, é interessante dentro da infra da sua empresa ter um servidor próprio de registro de imagens mas isso é conversa para outro post.

### MAINTAINER

É muito comum encontrar essa entrada no Dockerfile mas ela não vai existir mais em breve. O recomendado agora é usar LABEL. Veja:

```
LABEL maintainer="joao@joaovrmaia.com"
```

### COPY

O `COPY` copia arquivos do seu sistema de arquivos para o Docker bem parecido com o `cp` do terminal do linux. A diferença que ele aceita uma lista igual ao `CMD` do nosso exemplo, para alguns casos. Seu dever de casa é estudar o `ADD` e aprender as diferenças dele para o `COPY`. Observe que no nosso projeto de exemplo tem o arquivo `.dockerignore`, ele é parecido com o `.gitignore` só que para o Docker.

### RUN

Aqui você consegue rodar um ou mais comandos no seu Docker no tempo de criação, por exemplo, no nosso caso uso para instalar as dependências do nosso projeto.

### ENV

O `ENV` permite que você crie variáveis de ambientes padrão, no nosso caso estou colocando a versão da nossa imagem.

### EXPOSE

O `EXPOSE` permite que você torne visível portas do seu Docker para o máquina host e ainda escolha o protocolo. Isso vai permitir que ao executar o `docker run` com o argumento `-P` sua portas descritas no `EXPOSE` sejam exportadas fazendo uma ponte entre o host e Docker.

Por padrão o protocolo usado é o TCP caso vocÊ informe apenas o valor da porta, no caso de UDP seria:

```dockerfile
EXPOSE 5000/udp
```

### ENTRYPOINT

É o comando base onde serão apendados os comandos que você enviar no ínicio do Docker. Vamos ver um exemplo, suponha que seu Dockerfile tenha o ENTRYPOINT o comando `ls`. Ao rodar o Docker ele vai lidar tudo que tiver no `WORKDIR` mas se você colocar um comando de `*.txt` ele só vai listar os arquivos que tem extensão `.txt`. Exemplo:

```
$ docker run --entrypoint ls --workdir /etc/apt flask-example:0.0.1
apt.conf.d
preferences.d
sources.list
sources.list.d
trusted.gpg.d
$ docker run --entrypoint ls --workdir /etc/apt flask-example:0.0.1 sources.list
sources.list
```

### CMD

O `CMD` serão os argumentos passados ao seu `ENTRYPOINT`, no nosso caso é apenas no nome do arquivo Python que vamos executar pois o`ENTRYPOINT` é apenas `python`.

# Executando o Dockerfile

```
$ docker build -t flask-example:0.0.1 .
Sending build context to Docker daemon  8.192kB
Step 1/9 : FROM python:3.6.3
 ---> a8f7167de312
Step 2/9 : MAINTAINER Joao Maia "joao@joaovrmaia.com"
 ---> Running in 3ca347b1ba55
Removing intermediate container 3ca347b1ba55
 ---> a7c43fccf59d
Step 3/9 : COPY . /app
 ---> fbbfd46dc756
Step 4/9 : WORKDIR /app
Removing intermediate container 4ffd4b376f2c
 ---> 0ca7c80a6bc2
Step 5/9 : RUN pip install -r requirements.txt
 ---> Running in 708c43e02cd5
Collecting click==6.7 (from -r requirements.txt (line 1))
  Downloading click-6.7-py2.py3-none-any.whl (71kB)
Collecting Flask==0.12.2 (from -r requirements.txt (line 2))
  Downloading Flask-0.12.2-py2.py3-none-any.whl (83kB)
Collecting itsdangerous==0.24 (from -r requirements.txt (line 3))
  Downloading itsdangerous-0.24.tar.gz (46kB)
Collecting Jinja2==2.10 (from -r requirements.txt (line 4))
  Downloading Jinja2-2.10-py2.py3-none-any.whl (126kB)
Collecting MarkupSafe==1.0 (from -r requirements.txt (line 5))
  Downloading MarkupSafe-1.0.tar.gz
Collecting python-decouple==3.1 (from -r requirements.txt (line 6))
  Downloading python-decouple-3.1.tar.gz
Collecting Werkzeug==0.13 (from -r requirements.txt (line 7))
  Downloading Werkzeug-0.13-py2.py3-none-any.whl (311kB)
Building wheels for collected packages: itsdangerous, MarkupSafe, python-decouple
  Running setup.py bdist_wheel for itsdangerous: started
  Running setup.py bdist_wheel for itsdangerous: finished with status 'done'
  Stored in directory: /root/.cache/pip/wheels/fc/a8/66/24d655233c757e178d45dea2de22a04c6d92766abfb741129a
  Running setup.py bdist_wheel for MarkupSafe: started
  Running setup.py bdist_wheel for MarkupSafe: finished with status 'done'
  Stored in directory: /root/.cache/pip/wheels/88/a7/30/e39a54a87bcbe25308fa3ca64e8ddc75d9b3e5afa21ee32d57
  Running setup.py bdist_wheel for python-decouple: started
  Running setup.py bdist_wheel for python-decouple: finished with status 'done'
  Stored in directory: /root/.cache/pip/wheels/ee/7a/3f/d4d0a9913d86c1b9cd8445716d7ea989bfcb7be840257a58e0
Successfully built itsdangerous MarkupSafe python-decouple
Installing collected packages: click, Werkzeug, MarkupSafe, Jinja2, itsdangerous, Flask, python-decouple
Successfully installed Flask-0.12.2 Jinja2-2.10 MarkupSafe-1.0 Werkzeug-0.13 click-6.7 itsdangerous-0.24 python-decouple-3.1
Removing intermediate container 708c43e02cd5
 ---> ca01887d9156
Step 6/9 : ENV VERSION=0.0.1
 ---> Running in d25791d75e6b
Removing intermediate container d25791d75e6b
 ---> 6f62469c3366
Step 7/9 : EXPOSE 5000
 ---> Running in 50f9f6af9f18
Removing intermediate container 50f9f6af9f18
 ---> e729df55a256
Step 8/9 : ENTRYPOINT ["python"]
 ---> Running in 4de09f10c11a
Removing intermediate container 4de09f10c11a
 ---> 7437f1c7da05
Step 9/9 : CMD ["app.py"]
 ---> Running in 90b2682371c3
Removing intermediate container 90b2682371c3
 ---> 4d34885cdcb5
Successfully built 4d34885cdcb5
Successfully tagged flask-example:0.0.1
$ docker images
REPOSITORY                                   TAG                 IMAGE ID            CREATED              SIZE
flask-example                                0.0.1               4d34885cdcb5        About a minute ago   701MB
python                                       3.6.3               a8f7167de312        2 weeks ago          691MB
```

# Executando nossa imagem

```
$ docker run --rm -d -p 8080:5000 flask-example:0.0.1
bed4ed93779ae589c643d86b2f34f1cb085c79d4123e76677b6f7d1793376d5b
$ curl localhost:8080
bed4ed93779a - 0.0.1
$ docker ps
CONTAINER ID        IMAGE                 COMMAND             CREATED              STATUS              PORTS                    NAMES
bed4ed93779a        flask-example:0.0.1   "python app.py"     About a minute ago   Up About a minute   0.0.0.0:8080->5000/tcp   distracted_northcutt
```

# Conclusão

Com o fim dessa introdução ao Docker espero que você seja capaz de criar na sua máquina de trabalho réplica de parte ou toda sua infra de produção, por exemplo, criar um Dockerfile que gera a imagem do seu Docker com sua aplicação em Python e subir um banco MongoDB para ser usado pela aplicação. Recomendo agora que você pratique mais o que foi visto aqui e olhe a documentação pois muitas coisas foram omitidas por conta do escopo da introdução.

Abraços, João
