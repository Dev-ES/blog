---
layout: post
title: Introdução ao Git e GitHub
description: breve tutorial sobre Git e GitHub, suas diferenças bem como funções básicas para todo dev.
categories: tutoriais
author: Gabriel Reis
---

Ei pessoal,

Git e GitHub são duas coisas que apesar do nome parecido são bem distintas e se a imagem abaixo parece familiar então
esse post vai ser uma baita mão na roda!

![Trabalho versão final](/images/git-tutorial/git-version.png)

Acredito que muita gente já deve ter feito isso em algum trabalho da faculdade ou arquivo importante que precisou editar
várias vezes. Tudo parece muito bom, afinal, o gmail, pendrive ou
dropbox sempre foram lugares confiáveis para guardar seus arquivos, até o dia em que começam as perguntas "não estou
achando mais isso", "eu podia jurar que tinha mandado pro email" ou "meu pendrive sumiu com meu tcc junto"

Pensando nisso e em uma maneira melhor de se organizar códigos o Git e o GitHub surgiram pre resolver esses problemas
com o versionamento de código, possibilitando o controle de todas as alterações realizadas em um arquivo e o
compartilhamento de arquivos entre uma equipe muito mais simples, o que é imprescindível em uma equipe de dev's!

Então, de maneira rápida, funciona assim:

* Git -> sistema de versionamento local e organização de arquivos
* GitHub -> Repositório remoto de projetos e rede social

## Primeiros passos com GitHub

De que adianta utilizarmos apenas o Git para controlar as coisas se todas as alterações ficam apenas na sua máquina
local? o GitHub existe como um repositório externo, podendo ser privado ou aberto, facilitando assim o acesso por mais
pessoas, além de servir também como um grande meio de apoiar toda a comunidade desenvolvedora com projetos open-source!

Primeiramente, se você utiliza Windows deve baixar o [git-bash](https://git-scm.com/download/), se for usuário de linux ou mac, que apesar de normalmente já terem git por padrão, pode-se facilmente instalar com sudo apt-get install git.

Com uma interface bem amigável, é possível criar um repositório no GitHub da seguinte forma:

![Criação de um repositório no GitHub](/images/git-tutorial/git-new-repo.png)

No final do processo, deve ser gerada uma url do tipo

```bash
   https://github.com/gabrielreisn/git-tutorial.git
```

Com o nosso repositório remoto criado, podemos 'importar' ele localmente da seguinte forma utilizando o git-bash ou o terminal:

```bash
   git clone
   https://github.com/gabrielreisn/git-tutorial.git
```

Onde será criada uma pasta 'git-tutorial' no diretório atual do bash. Nesse ponto todos os arquivos do projeto devem ser colocados nessa pasta e serão mapeados pelo git mais tarde. Assim, vou explicar todos os comandos usados de forma rápida e demonstrar um passo a passo logo depois

```bash
   git add .
```

adiciona todos os arquivos daquela pasta ao git, onde eles serão monitorados (também é possível adicionar apenas um arquivo usando 'git add file.txt')

```bash
   git commit -m "meu primeiro commit"
```

* Brotip: se você preferir, é possível executar os dois de uma vez só: git commit -am "meu commit"

cria uma nova versão para todos os arquivos que foram adicionados ao git, deixando uma mensagem como forma de identificação. Essa mensagem é muito importante para que outros desenvolvedores (ou você mesmo) possam entender o que foi alterado naquele ponto específico do projeto

```bash
   git push
```

Envia um 'pacote' contendo todas as modificações feitas e marcadas para o repositório remoto no github, para que ele possa ser visto propriamente por outras pessoas

Resumindo..Tudo ocorre da seguinte foma:

![Processo do git](/images/git-tutorial/git-process.png)

Ao final, podemos abrir o repositório criado anteriormente para verificar as alterações e esperamos o resultado como na imagem abaixo:

![Repositório remoto atualizado com sucesso](/images/git-tutorial/git-repo-with-file.png)

Reparem na imagem acima, no canto direito das linhas destacadas, sempre existe um "(master)" nelas, o que significa que estamos no branch, ou ramificação, principal do nosso repositório e apesar de isso funcionar e resolver alguns casos pode acabar gerando problemas de merge (conflito) quando duas pessoas ou mais alteram a mesma linha de um arquivo. Por isso, é considerado uma "má prática" realizar commit diretamente no master e foi criada a abordagem de git flow, conhecida por (fork, branch, pull request). Ela não é seguida sempre a risca, muitas equipes adotam suas próprias práticas (geralmente apenas branch e pull request(PR) ). Assim, usando branches é possível trabalhar em conjunto muito melhor sem atrapalhar a feature do amiguinho :)
