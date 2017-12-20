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

Essa instrução indica apartir de qual imagem a sua vai se basear, no nosso caso como vasmos subir uma aplicação Python já peguei uma imagem com o Python instalado para evitar esse processo. Estou usando uma das imagens oficiais do Docker. Nota, sempre que possível use imagens oficiais do Docker, evite usar imagem qualquer por ai na internet e verifique sempre as imagens que você for utilizar. Nada impede que uma pessoa crie uma imagem com software malicioso e divulgue publicamente.

### MAINTAINER


### COPY


### RUN


### ENV


### EXPOSE


### ENTRYPOINT


### CMD


# Conclusão

Com o fim dessa introdução ao Docker espero que você seja capaz se criar na sua máquina de trabalho réplica de parte ou toda sua infra de produção, por exemplo, criar um Dockerfile que gera a imagem do seu Docker com sua aplicação em NodeJS e subir um bando MongoDB e para ser usado pela aplicação. Recomendo agora que você pratique mais o que foi visto aqui e olhe a documentação pois muitas coisas foram omitidas por conta do escopo da introdução.

Abraços, João
