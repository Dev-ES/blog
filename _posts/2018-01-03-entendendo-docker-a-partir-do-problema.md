---
layout: post
title: 'Entendendo o que é docker a partir do problema que quero resolver'
description: Uma leve introdução do docker partindo dos nossos problemas de cotidiano e o que ele se propõe a resolver
categories: docker
author: Clayton Silva
---

Vou iniciar um papo com vocês a partir do problema que quero resolver e não da tecnologia em si e dizendo o que ele faz, se ele dá cambalhota, se Sharon Stone usa e talz. Nada de modinha, vamos partir do problema

# Quem sou eu

Meu perfil é desenvolvedor, tenho como responsabilidade desenvolver funcionalidades de acordo com a demanda de meu cliente e devo me concentrar cada vez mais na feature do cliente que na camada de tecnologia que o envolve (claro que não vou ser tapado e ignorar o mundo a minha volta, mas vou aprender sempre algo que diminua meu tempo de desenvolvimento).

Tenho alguns clientes, que variam de pequenas agências, a pequenos e-commerces, alguns estão tomando escalabilidade maior e não consigo mais manter na minha hospedagem compartilhada do provedor xyz, pois ele necessita cada vez mais de um gerenciamento personalizado.

Quando é compartilhado, eu poderia usar o git pra mandar o fonte via push (tópico avançado para muitos desenvolvedores, me procurem se quiserem discutir mais a respeito), ou posso trabalhar da forma mais antiga, o bom e velho ftp.

Agora tenho que gerenciar um server inteiro? caramba! como vou manter isso alinhado com o que desenvolvo?

# Levantamento de requisitos

## Sistema Operacional

Escolha o SO que o faz sentir confortável, de preferencia o mesmo do ambiente compartilhado se você faz entregas similares para vários clientes, pois se tem uma falha de segurança ou um upgrade, é menos esforço pra tratar todo mundo.

## Servicos base

Muitas vezes você depende de um pacote base pra fazer a entrega do seu aplicativo, exemplo do apache ou nginx que gerencia a entrada http e direciona para arquivos estáticos ou sua aplicação final, mantenha sempre na mesma versão pelos mesmos problemas que encontramos no SO.

[Esse](https://www.digitalocean.com/community/tutorials/como-instalar-o-nginx-no-ubuntu-16-04-pt) artigo ilustra bem os passos pra instalar e configurar uma versão xyz de um nginx no sistema operacional, bem são sete a oito passos, e devem ser revisados toda vez que voce fizer update, um a um, em cada máquina que você tiver, já imaginou mantendo isso em 10 clientes? como você vai focar na funcionalidade e ao mesmo tempo manter qualidade do seu serviço?

Se eu te mostrar um comando que substitui isso?

```bash

docker run --name nginx -p 80:80 -p 443:443 -d nginx:x.y.z

```

Pra que sofrer?

## Seu serviço

Vamos enumerar aqui o roteiro que você faz toda vez que tem um serviço novo pro seu cliente?

* editar o config e colocar o nome do cliente e o ip do banco de dados
* copiar os arquivos da pasta x pra destino /var/destino-final
* rodar o comando `npm build` pra gerar os arquivos estáticos
* checar se o nginx está apontando corretamente pra ele
* restartar o serviço

Você pode ter um arquivo bash `maroto` que possa resolver isso, bem inteligente da sua parte se tiver, você é programador e tem um propósito, `programar tudo que é passo repetido pra que se faça uma vez só`, não seja carregador de pianos.

Vou te vender o peixe que tem um cara que pega seu código fonte, e vai colocar tudo lá dentro e executar esse seu comandinho maroto do bash

```Dockerfile
FROM node

WORKDIR /app

ADD .

RUN npm install

CMD ["npm","start"]
```

Deixe ele na raíz do seu projeto, toda vez que você terminar a versão, faça isso:

```bash

docker build -t meunome/meuprojeto:vX.Y.Z .

```

Sempre tenha uma versão distinta X.Y.Z pra cada entrega que fizer, você não é o senhor das entregas perfeitas e ninguém será, e possivelmente você pode errar e vai ter que voltar versão, pra quê ficar se matando no `control+z` igual um mané enquanto o cliente está gritando com você no telefone dizendo que não faz dinheiro com serviço parado.

pra mandar você manda igual git:

```bash

docker push meunome/meuprojeto:vX.Y.Z

```

e pra pegar lá do servidor também!

```bash

docker pull meunome/meuprojeto:vX.Y.Z

```

pra rodar junto com seu nginx é tão simples quanto

```bash

docker run --rm --name app -d meunome/meuprojeto:vX.Y.Z
docker run --rm --name nginx -p 80:80 -p 443:443 --volumes-from=app -d nginx:x.y.z

```

Lembrando que antes você tinha que além de arrumar o fonte tinha que rodar o bash `maroto`
na máquina do cliente, com risco de alguma coisa não sair do planejado no servidor do seu cliente, aumentando o tempo de downtime, e estragando sua relação com seu cliente.

Se eu te falar que esse processo todo é *mais* manual que existe do ecossistema que estou te apresentando? você se interessaria em saber que ecossistema é esse?

## Que problema eu resolvo

Os comandos estão bem resumidos nesse caso de uso, que tem por objetivo ilustrar o quanto você pode simplificar seu processo de trabalho.

Vou te dar uns benefícios:

* Chega de ficar se matando de pensar qual a versão está o serviço no cliente.
* Chega de ficar se matando de resolver bug de n versões de serviços `bases` que você trabalha.
* Chega de ficar se matando no fonte quando a versão nova que você entrega dá errado e você tem que descobrir qual arquivo que cagou, volte a versão e seja feliz.
* Chega de gastar tempo no servidor quando você pode ficar nas funcionalidades do seu cliente, que é onde ele fica mais feliz.

Se interessou com isso, vamos embora estudar [Docker](http://blog.dev-es.org/introducao-ao-docker-parte-1/)?

# Conclusão

Uma tecnologia nova pode trazer mais benefícios do que trabalho, e pode reduzir sua carga de trabalho, mesmo que tenha uma curva que pareça que está tendo mais trabalho inicialmente. 

Pense no benefício que você pode conseguir ao dominar totalmente a tecnologia e na vantagem que pode ter em relação aos seus concorrentes, que gastam mais tempo que você para realizar a mesma tarefa. E que querem se manter 
`porquê não é o foco deles`. 

Mas falando de mercado nosso, concorrência é só com sobrinho do primeiro período que levanta o e-commerce da tia, entra no 
[Stack Overflow](https://pt.stackoverflow.com/) com problema de conexão com um banco de dados que não foi instanciado né :D.

Bjundas
