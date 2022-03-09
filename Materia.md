# Docker: Criando e gerenciando containers
iniciado em 01/03/2022

terminado em 09/03/2022

[certificate](https://cursos.alura.com.br/certificate/2551c006-383c-4e48-8c4c-f5fabf27bae5) 

Table of contents
- [Docker: Criando e gerenciando containers](#docker-criando-e-gerenciando-containers)
  - [Conhecendo o problema](#conhecendo-o-problema)
    - [Maquinas virtuais](#maquinas-virtuais)
    - [Containers](#containers)
  - [Como containers funcionam](#como-containers-funcionam)
    - [Namespaces](#namespaces)
      - [Principais Namespaces](#principais-namespaces)
    - [como o container vai funcionar sem o sistema operacional](#como-o-container-vai-funcionar-sem-o-sistema-operacional)
    - [gerenciamento de recursos nos containers](#gerenciamento-de-recursos-nos-containers)
      - [CGroups](#cgroups)
  - [Instalando o Docker no Windows](#instalando-o-docker-no-windows)
    - [WSL](#wsl)
  - [Instalando o Docker no Linux](#instalando-o-docker-no-linux)
  - [Faça como eu fiz: Compreendendo containers](#faça-como-eu-fiz-compreendendo-containers)
  - [o que aprendemos?](#o-que-aprendemos)
  - [Conhecendo o Docker Hub](#conhecendo-o-docker-hub)
  - [Fluxo da criação de containers](#fluxo-da-criação-de-containers)
    - [docker ps](#docker-ps)
    - [docker container ls](#docker-container-ls)
    - [docker ps -a | docker container ls -a](#docker-ps--a--docker-container-ls--a)
      - [CONTAINER ID](#container-id)
      - [IMAGE](#image)
      - [COMMAND](#command)
      - [STATUS](#status)
      - [PORTS](#ports)
      - [NAMES](#names)
    - [comando 'sleep'](#comando-sleep)
    - [Processo de execuçao do docker](#processo-de-execuçao-do-docker)
  - [Outros comandos importantes](#outros-comandos-importantes)
    - [docker stop](#docker-stop)
    - [docker start](#docker-start)
    - [docker exec](#docker-exec)
    - [sair do container](#sair-do-container)
    - [docker pause](#docker-pause)
    - [docker unpause](#docker-unpause)
    - [parar um container instantaneamente com -t=0](#parar-um-container-instantaneamente-com--t0)
    - [docker rm](#docker-rm)
  - [Mapeando portas](#mapeando-portas)
    - [docker samples](#docker-samples)
    - [terminal detached](#terminal-detached)
    - [isolamento das interfaces de rede (localhost)](#isolamento-das-interfaces-de-rede-localhost)
    - [docker rm --force](#docker-rm---force)
    - [docker run -P (expor as portas)](#docker-run--p-expor-as-portas)
    - [docker port](#docker-port)
    - [mapear manualmente uma porta (-p 8080:80)](#mapear-manualmente-uma-porta--p-808080)
  - [o que aprendemos?](#o-que-aprendemos-1)
  - [Entendendo imagens](#entendendo-imagens)
    - [o que sao imagens?](#o-que-sao-imagens)
    - [docker images ou docker image ls](#docker-images-ou-docker-image-ls)
    - [docker inspect](#docker-inspect)
    - [docker history](#docker-history)
    - [imagens sao read only](#imagens-sao-read-only)
    - [container sao imagens com uma camada de R/W](#container-sao-imagens-com-uma-camada-de-rw)
  - [Criando a primeira imagem](#criando-a-primeira-imagem)
    - [docker build](#docker-build)
      - [projeto exemplo](#projeto-exemplo)
    - [Incrementando a imagem](#incrementando-a-imagem)
      - [parando todos os containers de uma vez (docker stop $(docker container ls -q))](#parando-todos-os-containers-de-uma-vez-docker-stop-docker-container-ls--q)
      - [EXPOSE](#expose)
      - [leitura dinamica da porta no node](#leitura-dinamica-da-porta-no-node)
      - [ARG](#arg)
      - [ENV](#env)
    - [subindo a imagem para o Docker Hub](#subindo-a-imagem-para-o-docker-hub)
      - [Login no docker Hub no command line](#login-no-docker-hub-no-command-line)
      - [docker push](#docker-push)
        - [access denied no docker push](#access-denied-no-docker-push)
      - [docker tag](#docker-tag)
    - [o que aprendemos?](#o-que-aprendemos-2)
    - [O problema de persistir dados](#o-problema-de-persistir-dados)
      - [comandos do docker ate agora](#comandos-do-docker-ate-agora)
      - [tamanho do container (docker ps -s)](#tamanho-do-container-docker-ps--s)
    - [Utilizando bind mounts](#utilizando-bind-mounts)
    - [flag -v](#flag--v)
      - [flag --mount](#flag---mount)
      - [o que acontece se passarmos um diretorio que nao existe?](#o-que-acontece-se-passarmos-um-diretorio-que-nao-existe)
    - [Utilizando Volumes](#utilizando-volumes)
      - [docker volume](#docker-volume)
      - [docker volume ls](#docker-volume-ls)
      - [docker volume create](#docker-volume-create)
      - [docker run com volume](#docker-run-com-volume)
      - [local onde o docker armazena os volumes no linux](#local-onde-o-docker-armazena-os-volumes-no-linux)
      - [docker volume commands](#docker-volume-commands)
      - [criar um volume com a flag --mount](#criar-um-volume-com-a-flag---mount)
    - [utilizando tmpfs (somente linux)](#utilizando-tmpfs-somente-linux)
      - [flag --tmpfs](#flag---tmpfs)
      - [flag --mount com tmpfs](#flag---mount-com-tmpfs)
    - [o que aprendemos?](#o-que-aprendemos-3)
    - [conhecendo a rede bridge](#conhecendo-a-rede-bridge)
      - [docker inspect](#docker-inspect-1)
      - [bridge](#bridge)
      - [docker network](#docker-network)
    - [criando uma rede bridge](#criando-uma-rede-bridge)
      - [nomeando um container - flag --name](#nomeando-um-container---flag---name)
      - [criando uma user defined network - docker network create](#criando-uma-user-defined-network---docker-network-create)
      - [criar um container com nome personalizado e rede propria](#criar-um-container-com-nome-personalizado-e-rede-propria)
    - [as redes none e host](#as-redes-none-e-host)
      - [rede 'none'](#rede-none)
      - [rede 'host'](#rede-host)
    - [Comunicando aplicaçao e banco](#comunicando-aplicaçao-e-banco)
    - [o que aprendemos?](#o-que-aprendemos-4)
    - [conhecendo o Docker Compose](#conhecendo-o-docker-compose)
      - [docker compose no windows](#docker-compose-no-windows)
      - [docker compose no linux](#docker-compose-no-linux)
    - [definindo os serviços](#definindo-os-serviços)
      - [docker compose up](#docker-compose-up)
    - [complementando o Compose](#complementando-o-compose)
      - [depends on](#depends-on)
      - [docker-compose ps](#docker-compose-ps)
      - [docker-compose down](#docker-compose-down)
      - [seçao deploy](#seçao-deploy)
      - [docker swarm](#docker-swarm)
    - [o que aprendemos?](#o-que-aprendemos-5)
    - [docker commands](#docker-commands)
  

## Conhecendo o problema
Vamos partir do nosso problema inicial. 

Os sistemas atualmente têm diversas aplicações e ferramentas que interagem entre si para compor o todo desse sistema. 

E é mais ou menos esse caso que vamos visualizar nesse curso e entender porque os containers vão nos ajudar nessas situações. 

Mas antes vamos partir bem do básico da ideia de porque vamos chegar nessa solução.

Nós teremos uma aplicação Nginx, que vai servir como um load balancer do nosso sistema. Temos uma aplicação Java e também uma aplicação C# rodando com o .NET.

Isso é para ilustrar a nossa situação. 

E como queremos que todas essas aplicações estejam em execução para que o nosso sistema como um todo esteja ok, nós precisamos de uma máquina para que essas aplicações e, consequentemente, o nosso sistema estejam rodando.

A nossa aplicação C# está rodando na porta 80, ela precisa da porta 80 para executar. 

A nossa aplicação Java também, assim como o Nginx. 

O C# estamos utilizando na versão 9, o Java na versão 17 e o Nginx na versão 1.17.0.

E quais possíveis problemas podem ocorrer nessa situação? 

Se formos observar cada uma dessas aplicações e ferramentas, podemos acabar tendo um conflito de portas, porque as três aplicações desse cenário dependem da porta 80 para executar o fluxo que elas precisam.

Além disso, como é que podemos alterar as versões de maneira prática? Será que se eu simplesmente fizer um downgrade ou um upgrade da versão do C#, atualizando meu .NET, eu vou quebrar alguma coisa? Será que eu preciso desinstalar para instalar uma nova?

A mesma coisa para o Java e para o Nginx: será que eu vou conseguir atualizar de maneira prática?

E outra questão é como teremos um controle de recursos de memória e de CPU para essas aplicações? Eu quero que, por exemplo, a minha aplicação C# precise de 100 milicores de CPU e 200 MB de memória para funcionar. Como é que eu posso definir isso de maneira fácil?

A mesma coisa para as minhas outras aplicações. Como eu posso fazer essa definição de consumo de recursos desse sistema de maneira prática? É uma questão que precisamos levantar.

E por fim, juntando todos esses problemas de uma vez só, como é que podemos fazer o processo de manutenção dessas aplicações? Como conseguiremos mudar a versão? Como conseguiremos ter esse controle sobre as portas da nossa aplicação, gerenciar os recursos e manter isso no longo prazo?

Então uma solução que poderíamos pensar inicialmente e que seria bem simples, mas ao mesmo tempo problemática, e nós veremos o porquê, seria simplesmente comprarmos uma máquina para cada aplicação.

Teremos uma máquina para nossa aplicação .NET, uma máquina para nossa aplicação Java e uma máquina para nossa aplicação Nginx.

Assim nós temos o problema resolvido do conflito de portas. Cada máquina teria sua respectiva porta 80.

Conseguiríamos controlar os recursos de maneira mais fácil, porque não teria essa dependência das aplicações entre si.

E também faríamos o controle do versionamento um pouco mais facilmente, porque não teria diversas aplicações num mesmo sistema.

Qual o problema disso? 

```diff
- A questão é que o nosso dinheiro vai para o ralo, porque se temos uma máquina para cada aplicação, vamos pensar que tem sistemas com milhares ou milhões de aplicações executadas ao mesmo tempo. Então imagine ter milhares ou milhões de máquinas para que o seu sistema esteja operante. O dinheiro vai embora.
```

### Maquinas virtuais
Então existe uma solução já bem difundida, ela não foi criada agora, já é até razoavelmente antiga, que nos ajuda nesse tipo de problema, que são as máquinas virtuais, onde teremos nosso hardware bem definido, um sistema operacional, seja Windows, Linux, Mac e afins.

E teremos também uma camada de hypervisor, que vai fazer um meio de campo entre virtualizar um novo sistema operacional, que pode ser um Windows, um Linux, um Mac, rodando dentro de outro sistema. Mas graças a essa camada de hypervisor nós teremos uma camada de isolamento desse sistema operacional original.

E conseguiremos instalar nossas dependências e aplicações de maneira isolada, porque cada uma delas terá seu respectivo sistema operacional.

É uma questão que já resolve esses problemas iniciais, mas a pergunta que fica nesse momento é: será que é realmente necessário fazer isso? 

Porque nós vamos querer executar nossas aplicações, como vimos, de maneira isolada uma da outra, ter um controle de recursos, ter um controle de versionamento bem definido.

Então será que essa camada de hypervisor é realmente necessária? Será que precisamos sempre nessas situações virtualizar o sistema operacional? Pode ser que sim, pode ser que não. Mas o caso que veremos durante esse curso é a utilização de containers. 

### Containers
O que um container é?

Repara que se formos comparar os desenhos, não temos a camada de sistema operacional virtualizado e hypervisor, e sim diretamente a camada de container rodando no nosso sistema operacional, e mesmo assim essas aplicações estão isoladas entre si e também isoladas do nosso sistema operacional original.

E é isso que vamos entender a partir de agora. Temos algumas perguntas que temos que responder de início: por que os containers são mais leves, além desse argumento visual que vimos agora?

Como eles vão garantir esse isolamento? E também como eles vão funcionar sem instalar um sistema operacional? Porque no caso da máquina virtual, a nossa aplicação C# terá um sistema operacional para ela. A nossa aplicação Java também.

Se olharmos dentro do nosso sistema de containers, a nossa aplicação C# está diretamente dentro do container. E qual sistema operacional ela vai usar? É Windows, é o Linux? Precisa instalar um sistema? Nós precisamos responder a essas perguntas, de onde vêm essas informações?

E também como é que vai ficar a divisão de recursos entre essas aplicações que estarão a partir de agora containerizadas?

Nós entenderemos a partir de agora, no próximo vídeo, como essas situações acontecem. Como o container será mais leve em questão de consumo de recursos, como ele vai garantir isolamento, como ele funciona sem instalar um sistema operacional, por assim dizer.

Agora que já vimos como os containers nos ajudam, o que eles fazem, assim como as máquinas virtuais ajudam, vamos entender os diferenciais de como o container opera dentro do nosso sistema.

## Como containers funcionam
Vamos responder às perguntas: por que os containers são mais leves em relação a uma máquina virtual? O que muda no fim das contas?

O container funciona da seguinte maneira: dentro do nosso sistema operacional nós temos diversos containers, diversas aplicações que estarão sendo executadas no nosso sistema operacional. 
```diff
+ Só que no fim das contas eles vão funcionar diretamente como processos dentro do nosso sistema.
```

Enquanto uma máquina virtual terá toda aquela etapa de virtualização dos sistemas operacionais, dentro do nosso sistema original, os containers vão funcionar diretamente como processos dentro do nosso sistema.

Então a grosso modo, conseguimos visualizar como o consumo de recursos, a carga para que ele consiga ser executado é um pouco menor. Porque eles serão processos, e não uma virtualização completa.

Mas como o processo vai conseguir garantir isolamento, como é que vai funcionar sem instalar o sistema? Como vamos conseguir resolver e responder a essas perguntas?

### Namespaces
A outra questão é que quando os nossos containers estiverem em execução dentro do nosso sistema operacional, a fim de garantir o isolamento entre cada um deles e o nosso sistema operacional original, existe um conceito chamado namespace, que vai garantir que cada um desses containers tenha a capacidade de se isolar em determinados níveis.

#### Principais Namespaces
Mas que níveis são esses? Nós teremos os principais namespaces, que são os de 
* PID, que garantem o isolamento a nível de processo dentro de cada um dos containers. Então um processo dentro de um container, que consequentemente é um processo dentro do nosso sistema operacional, vai estar isolado de todos os outros do nosso host, da nossa máquina original.

* Nós teremos o NET também, o namespace de rede, que vai garantir o isolamento entre uma interface de rede de cada um dos containers e também do nosso sistema operacional original.

* O de IPC, que vai ser de intercomunicação entre cada um dos processos da nossa máquina.

* O de MNT, que é da parte de files system, sistema de arquivos, montagem, volumes e afins. Também estará devidamente isolado.

* E o de UTS, que faz um compartilhamento, um isolamento ao mesmo tempo também, de host do nosso Kernel da máquina que está escutando o container.

Esse último em específico, o UTS, ajuda a responder até a próxima pergunta, que é como o container dentro do nosso sistema operacional vai funcionar sem um sistema operacional?

### como o container vai funcionar sem o sistema operacional
Porque na verdade graças ao namespace de UTS, se estivermos executando os nossos containers, por exemplo, em uma máquina que tem o Kernel Linux, cada um desses containers a princípio também vai ter um pedaço desse Kernel, só que devidamente isolado.

Então conseguimos já responder a essa pergunta. Nós não precisamos necessariamente instalar um sistema operacional dentro de um container porque ele já vai ter, graças a esse namespace de UTS, também esse acesso ao Kernel do sistema original, só que isoladamente.

### gerenciamento de recursos nos containers
E por fim, também na parte de gerenciamento de recursos, vamos dizer que queremos definir o que levantamos no problema anteriormente, de definir o consumo máximo de memória, de CPU e afins para cada um dos nossos containers.

#### CGroups
Existe um outro conceito que se chama “Cgroups”, que vai garantir que consigamos definir tanto de maneira automática quando de maneira manual como os consumos serão feitos para cada um desses containers dentro do nosso sistema operacional.

Então no fim das contas, voltando às nossas perguntas originais, graças aos namespaces e aos Cgroups nós conseguimos garantir isolamento, conseguimos garantir que nosso container funcione sem instalar um sistema operacional dentro dele, e também conseguiremos ter um controle de gerenciamento de recursos, como memória de CPU.

Sobre a parte de porque eles são mais leves, nós vimos que eles funcionam como processos diretamente do nosso sistema operacional, mas no decorrer desse curso vamos entender ainda mais porque eles conseguem ser tão mais práticos em relação à maquina virtual a nível de consumo de recurso e de tamanho de armazenamento no nosso sistema operacional.

Nós respondemos as principais perguntas de quem está começando agora no mundo de containers sobre como vai garantir isolamento, como ele funciona sem instalar um sistema operacional e afins.

Agora que entendemos como um container se diferencia de uma máquina virtual tradicional, veremos como instalar o Docker, inicialmente no Windows e depois também no Linux, que vai ser onde abordaremos as principais utilizações do Docker.


## Instalando o Docker no Windows
Em relação à instalação do Docker no Windows, tem alguns pontos que são muito relevantes para se destacar.

O primeiro deles, para já irmos adiantando, é que precisamos acessar a página da documentação oficial e clicar em “Docker Desktop for Windows”. Já tenho baixado, mas é só clicar no botão que vai começar a baixar.
(download)[https://www.docker.com/get-started]

Nesse caso vou cancelar, porque como falei para vocês eu já tenho baixado. E quando baixarmos, basta clicar duas vezes no instalador que vai carregar e seguir o fluxo de instalação normalmente.

Depois de um tempo, eu vou fazer o processo de instalação com vocês, ele vai abrir aquela tela de instalação e afins.

Nesse momento ele mostra duas opções de instalar os componentes necessários para o Docker rodar com Windows em WSL 2, então isso é uma questão que precisamos responder; e adicionar um atalho na área de trabalho, que pode ser marcada sem nenhum problema.

Então vamos deixar essas duas opções marcadas nesse momento. Eu vou clicar em “OK” para ele já ir instalando.

Enquanto ele vai instalando precisamos destacar alguns pontos. O primeiro é que caso você seja um usuário de Docker ou esteja começando agora e esteja pensando em utilizar o Windows para isso, eu recomendo fortemente que você não faça a utilização do Docker no Windows, e sim utilize a ferramenta no Linux, que é um ambiente muito mais estável e pronto para gerenciar e utilizar essa plataforma toda de conteinerização.

Um ponto que também já reforça isso que eu acabei de falar para vocês é que o Docker está fazendo uma atualização de termos de uso dele.

O Docker Desktop, que é o que estamos utilizando agora no caso do Windows, é uma ferramenta que vai passar a ser paga a partir de 31 de janeiro de 2022, caso a sua empresa tenha mais de 250 funcionários ou tenha uma receita anual de mais de 10 milhões de dólares.

Pode ser que não tenha, pode ser que tenha, mas já é um ponto que você deve ter atenção, dependendo da empresa em que você trabalha.

O uso para estudo, a princípio, até o momento da gravação desse curso, continua liberado para caso você queira fazer seus experimentos e afins. Esse é o primeiro ponto que você precisa se atentar.

### WSL
O segundo é que você deve também ter atenção nesse cenário de WLS 2 backend. Nós vimos que aquela opção já estava marcada no momento da instalação do Docker.

E o que significa aquele WSL 2? Ele nada mais é do que o Windows Subsystem for Linux. Nós teremos um subsistema Linux rodando dentro do nosso Windows.

Então ele vai meio que virtualizar, a muito grosso modo, o sistema operacional Linux para que utilizemos o Docker nesse sistema operacional.

Então caso você já tenha tanto o Windows 11 quanto o Windows 10 nas respectivas versões que estão aparecendo na tela, basta você habilitar o WSL 2 dentro do seu sistema operacional Windows, em conjunto com a documentação da Microsoft. É bem simples.

Em conjunto com seu PowerShell também você consegue instalar o WSL. Tem toda a documentação, e caso você queira ver em português também, basta você alterar o idioma.

A URL está com “en-us”, por praticidade eu vou mudar para “pt-br”, e a partir desse momento ele também mostra tudo em português para fazer a instalação do WSL caso você não tenha ele ativado ainda.

Nesse momento, voltando para a instalação, ela já terminou. Vou clicar em “Close”. E como podemos ver se o Docker está funcionando?

Primeiro precisamos executá-lo. Eu posso abrir o menu Iniciar, ou simplesmente abrir a área de trabalho podemos clicar no ícone de instalação do Docker.

Basta clicar duas vezes e ele vai começar a carregar o Docker para a nossa máquina e executar tudo que é necessário para que o Docker funcione no ambiente Windows.

E como podemos testar se o Docker realmente está funcionando agora? Antes de mais nada, outra coisa que eu tinha falado para vocês é que assim que você abrir ele vai mostrar essa parte de serviços que estão sendo alterados, sobre 250 funcionários e 10 milhões de dólares de receita, então você tem que aceitar os termos.

Clicando que você aceita, o Docker vai começar a ser executado na sua máquina. Como você pode saber que o Docker está funcionando? Eu vou utilizar o PowerShell do próprio Windows e vou abrir. Podemos simplesmente fazer um teste.

Como podemos ver algum comando tipo um “Olá, mundo”? Caso você já tenha feito algum curso de programação você entendeu exatamente o que eu estou falando. Como podemos ver se o Docker está rodando?

Basta executarmos o comando docker run. Com isso vamos executar alguma coisa com Docker. E vamos colocar em seguida um hello-world, que é “olá, mundo”, um container clássico para vermos se o Docker está em funcionamento.

Eu vou dar um “Enter”. Ele deu um erro, falando que não conseguiu encontrar uma imagem “hello-world” na versão “:latest” localmente. Ele meio que travou, e agora funcionou. Nós vamos entender o que isso quis dizer no decorrer do curso.

Mas podemos ver que está funcionando, porque ele está mostrando “Hello from Docker”, um olá do Docker, e ele está falando nessa mensagem que nossa instalação funcionou corretamente.

Então instalamos o Docker no Windows sem nenhum problema, no instalador fomos dando “Next” e tudo funcionou.

Então a última observação antes de finalizar esse vídeo é: fique atento aos termos de uso do Docker com Windows, porque eles podem variar diversos termos, a partir de determinados momentos. Você tem que ficar atento quanto a isso para não ser cobrado posteriormente. Então tome cuidado, pode variar. Mas a instalação em si é essa.

E não fique apreensivo ou apreensiva, nós vamos entender o que cada uma das coisas que apareceram na tela querem dizer, como esse latest e o digest. Nós não sabemos o que isso quer dizer ainda, mas vamos entender.

Só que agora vamos fazer o processo de instalação do Docker no Linux, utilizando distros do Ubuntu.

## Instalando o Docker no Linux
O processo de instalação do Docker no Linux é bem simples e prático também, basta seguirmos a documentação.

(link)[https://docs.docker.com/engine/install/ubuntu/]

No caso do curso vamos utilizar o Docker no Ubuntu, que é uma das distros mais utilizadas dentro do ambiente Linux.

Ao contrário do Windows, não precisamos nos preocupar com aquela questão da alteração dos termos de uso. Mas você precisa ficar atento a outra coisa, caso você esteja utilizando o Ubuntu, e eu recomendo que você esteja para ficar alinhado com o progresso do curso: o suporte versão 16.04, que é a penúltima LTS lançada até o momento, não está mais sendo dada. Então utilize no mínimo a 18.04, conforme a recomendação da própria documentação.

Caso você esteja utilizando outros sistemas operacionais, ainda baseados em Linux, temos no menu lateral esquerdo o CentOS, o Debian, Fedora, RHEL, SLES e afins.

Mas caso você, no fim das contas, esteja se preocupando e realmente queira, por algum motivo, utilizar o Docker com o Mac iOS, também tem o guia de instalação. Não recomendo que faça. Ainda tem muitas instabilidades, e o Docker roda com muita facilidade no Linux. O ambiente Linux é o ambiente inclusive recomendado para que você faça a utilização produtiva do seu ambiente Docker.

Dados os recados, para instalar é bem simples. É só seguir a documentação, começando a partir do ponto em que vamos copiar o comando de atualizar os repositórios, os pacotes do nosso Ubuntu.

```
sudo apt-get update
```

Ele pede a senha de sudo, vou colocar a minha. Ele vai atualizar rapidamente e é só seguir copiando e colando os comandos. Cada um deles na instalação do pacote é necessário, tudo é necessário para que consigamos fazer.

```
sudo apt-get install
```

Eu já fiz a execução dele, vou refazer com vocês para vocês verem que está tudo funcionando corretamente.

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

No meu caso ele está perguntando se eu quero sobrescrever, porque como eu acabei de falar, eu já fiz. Mas vou colocar que sim. No seu caso, se for a primeira vez, não vai aparecer isso, ele só vai executar sem nenhum problema.

Agora nós vamos definir que queremos utilizar o Docker na versão estável. Ele tem a versão também “nightly” ou “test”, que ainda é o próprio Docker, mas são diferentes versões a nível de teste e estabilidade da plataforma, da ferramenta.

```
 echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Nós queremos estabilidade, então vamos utilizar a versão estável. E por fim vamos efetuar o update dos nossos pacotes mais uma vez. E no fim das contas executar também o comando de instalação. Ele vai executar rapidamente.

E assim que ele terminar de atualizar os pacotes do meu sistema, vamos executar o comando de instalação do Docker Community Edition, CLI do Docker e o containerd.io, que será responsável pela parte do funcionamento dos containers dentro do nosso sistema. Assim que ele terminar, o Docker estará 100% dentro da nossa máquina.

```
sudo apt-get update
```

Terminou de ler os pacotes, vou dar um “Ctrl + L” para limpar. E agora sim vou colar o último código e dar um “Enter”. No meu caso já estava instalado.

```
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

E agora, para validarmos isso, vamos executar o comando 

```
docker run hello-world
```
, assim como nós fizemos no Windows.

E o que aconteceu? Nós recebemos uma permissão negada. O que será que isso quer dizer? Por que isso aconteceu?

Agora nós precisamos nos atentar ao seguinte: quando temos essa questão de permissão no Linux, geralmente se executarmos o mesmo comando com um sudo na frente, tudo vai funcionar. Vamos ver se é assim mesmo.

```
sudo docker run hello-world
```

Ele deu o “Hello from Docker”, assim como deu no Windows. Então a princípio a instalação toda funcionou sem nenhum problema.

Mas como fazemos agora para não ficar executando Docker com o sudo na frente? É uma coisa meio chata termos que lembrar de colocar o sudo toda hora.

Existe uma maneira recomendada pela própria documentação de como podemos contornar esse problema, que é criar um grupo chamado Docker dentro do nosso sistema e colocarmos o nosso usuário, que no meu caso é Daniel, dentro desse grupo.

Nós usamos o comando sudo usermod e passamos os parâmetros -aG, seguido de docker, que é o nome do grupo ao qual queremos adicionar o nosso usuário, e em seguida $USER, que será o nosso próprio usuário.

```
sudo usermod -aG docker $USER
```

Dessa maneira conseguimos executar o comando do Docker sem o sudo na frente. A única questão é que precisamos reiniciar a nossa máquina.

Então vou mandar reiniciar a minha máquina, mas esse vídeo não vai parar. Parecia que eu ia cortar o vídeo no meio, mas não cortei. Enquanto a máquina vai reiniciando eu vou mostrar para vocês a questão na documentação de onde está isso que eu falei para vocês.

Na parte de pós instalação no Linux ele tem algumas etapas interessantes, inclusive essa que eu mostrei para você, com o comando sudo usermod –aG docker $USER. Nesse momento ele está adicionando nosso usuário a esse grupo Docker.

Ele fala no fim das contas que precisamos fazer o login novamente e tudo mais. No caso, se você estiver utilizando uma máquina virtual, será necessário reiniciar essa máquina para que as alterações tenham efeito.

Como eu estou gravando originalmente no ambiente Windows, eu estou utilizando uma máquina virtual para que tudo fique mais fácil no processo de produção.

Mas no fim das contas nós estamos no Linux. Já reiniciei a máquina, vou logar no meu usuário. E agora, quando ele terminar de carregar, nós vamos conseguir executar o mesmo comando que executamos previamente, que é o nosso docker run hello-world, só que agora sem o sudo. Vamos ver se vai funcionar mesmo.

Ele está terminando de carregar a área de trabalho. Vou abrir o terminal e agora executar docker run hello-world, sem o sudo na frente. Agora está tudo funcionando. Nossa instalação está ok.

Agora que já sabemos como instalar o Docker e fazer o teste para ver se ele realmente está funcionando, vamos começar a colocar a mão na massa e entender o que é esse docker run que executamos, o que aconteceu nesse momento.

Nós precisamos entender sobre esse fluxo, o que o Docker fez e porque tivemos essa mensagem. 

## Faça como eu fiz: Compreendendo containers
Antes de darmos o pontapé inicial, precisamos entender o benefício da utilização de containers em diferentes aplicações.

Muitos sistemas atualmente são compostos por diversas aplicações executadas simultaneamente. 

Diversos problemas podem ocorrer quando muitas aplicações precisam se comunicar. 

Porém, antes de pensarmos em comunicação de aplicações, é preciso visualizar outras questões importantes para nosso trabalho, como isolamento de contextos e versionamento de aplicações.

No decorrer deste curso, você irá compreender como os containers resolvem estes problemas e como executar aplicações de maneira isolada entre si.

## o que aprendemos?
* Maquinas virtuais possuem camadas adicionais de virtualizaçao em relaçao a um container
* Containers funcionam como processos no host
* Containers atigem isolamento atraves de namespaces
* os recursos dos containers sao gerenciados atraves de cgroups

## Conhecendo o Docker Hub
Agora vamos entender o que o Docker fez com o docker run, como ele funcionou. Nós vamos começar a aprofundar essas questões do docker run e como ele identificou aquele “hello world”, como ele sabia o que deveria fazer naquele momento.

Vamos voltar ao nosso terminal e executar mais uma vez o docker run hello-world do zero, para vermos o que acontece.

```
docker run hello-world
```

Nesse momento o Docker fala que não encontrou essa imagem na nossa máquina, depois ele faz o download. Por fim ele finaliza e faz uma validação sha256, que vamos entender mais à frente o que significa.

O ponto é que ele executa a saída do container que pedimos para executar, baseado naquele hello-world que nós passamos.

Vamos limpar o terminal e agora vamos executar outro docker run. Só que ao invés de simplesmente colocar hello-word, que queremos descobrir de onde veio, vamos colocar, por exemplo, um nome maluco nessa sequência, sem sentido de caracteres e dar um “Enter”.

A primeira parte foi igual, ele fala que não foi possível encontrar esse cara localmente. E depois ele dá um erro falando que ou tivemos acesso negado ou esse repositório que contém essa imagem não existe.

Mas voltou essa questão, o que é imagem? Nós vamos responder também isso aos poucos, mas vai chegar a hora que vamos dar a resposta final.

Mas precisamos entender que para que o nosso docker run funcione, ele precisa encontrar essa tal de imagem para que o container funcione. Nesse caso, quando passamos um nome maluco ele não encontrou. Mas com o hello-world ele encontrou.

Então como é que o Docker sabe onde ele deve procurar e encontrar esses nomes para resolver e executar o nosso container?

Ele até está dando uma dica para nós, de que existe um grande repositório de imagens que são essas receitas que vão executar os nossos containers, que é o chamado Docker Hub.

(link)[https://hub.docker.com/]

Dentro do Docker Hub podemos encontrar diversas dessas imagens, que são esses parâmetros que estamos passando. Se fizermos docker run --help, além de todas as flags, ele mostra que o uso do docker run é docker run, seguido de algumas opções, que vamos entender quais são em breve; e o nome da imagem que queremos executar.

Então “hello-world” é o nome de uma imagem. Aquele nome maluco que passamos também foi uma tentativa de um nome de imagem.

Isso significa que se viermos no Docker Hub pesquisarmos, por exemplo, por “hello-world”, ele vai achar nossa imagem. Temos como resultado o “hello-world”, que tem o selo de imagem oficial e na descrição fala que é só uma imagem para exemplificar uma “dockerização” mínima, ou seja, executar um container com Docker para testarmos se está funcionando.

A imagem ser oficial significa que ela foi feita e é mantida por um grupo confiável de pessoas que desenvolveram e têm reconhecimento da comunidade. Então ela recebe esse selo de oficial.

Abaixo ele mostra as descrições, o que é essa imagem efetivamente, conta uma breve história sobre ela.

Mas a questão é: se tentarmos pesquisar no Docker Hub a imagem maluca que criamos anteriormente, ele não encontra nada. Então é por isso que quando executamos o docker run com aquele nome maluco ele não encontrou. Mas o hello-world já existe.

Então o que mais podemos fazer agora para começar a levantar algumas perguntas? Quais outras imagens são interessantes de conhecermos para darmos esse pontapé agora na parte do docker run e entender como isso vai funcionar?

Existem diversas imagens que podem replicar, por exemplo, o conteúdo de um sistema operacional. Então por mais que um container não tenha necessidade de ter um sistema operacional instalado, conforme estávamos falando anteriormente, nós podemos ter um container que contém um sistema operacional, por exemplo, o Ubuntu.

Então se pesquisarmos por Ubuntu no Docker Hub, existe uma imagem, oficial inclusive. Então ela foi criada e é mantida por um grupo de pessoas confiáveis. Quando essa imagem for executada, ela vai gerar um container baseado no Ubuntu.

Então como podemos fazer isso agora? Vamos voltar para o terminal e dar um “Ctrl + C” para fechar e um “Ctrl + L”.

Se executarmos o comando docker run ubuntu, ele vai fazer exatamente isso: ele vai no Docker Hub, baixar essa imagem e executar esse container.

```
docker run ubuntu
```

Voltando no Docker Hub, se quisermos fazer em etapas, nós podemos primeiro baixar a imagem separadamente para executá-la depois.

Então ao invés de docker run ubuntu podemos fazer docker pull ubuntu. Ele não vai dar o output de que não encontrou localmente, porque já estamos pedindo para fazer o download. Ele vai fazer o download, vai extrair e, por fim, vai fazer aquela verificação. Mas ele não vai executar, porque nós só pedimos para ele baixar a imagem.

```
docker pull ubuntu
```

E agora se pedirmos para executar efetivamente, ele não vai fazer o download, porque nós já temos a imagem localmente. Então podemos simplesmente colocar agora docker run ubuntu para que o container execute.

E o que vai acontecer agora? Nada. Por que nada aconteceu? Vamos na nossa apresentação mais uma vez para entender e levantar algumas dúvidas.

No momento em que executamos o comando docker run ubuntu, nós esperávamos que subisse um container com o Ubuntu dentro dele e que alguma coisa executasse, como “Bem vindo ao Ubuntu”, ou alguma coisa do tipo.

Mas no fim das contas temos duas perguntas: por que o container não rodou? Ou será que ele rodou e nós não sabemos? Por que ele não exibiu nada para nós? São perguntas que precisamos responder.

E também, o que o docker run fez por baixo dos panos agora? Já sabemos agora que ele foi no Docker Hub, buscou aquela de imagem, que é a receita para gerar um container, e executou.

Mas como isso funciona efetivamente, para entendermos todo o fluxo e conseguirmos compreender o que estamos fazendo? 

## Fluxo da criação de containers
Vamos entender o que aconteceu. Primeiro: por que o container não rodou? Vamos voltar ao nosso terminal mais uma vez.

Vamos tentar mais uma vez por teimosia colocar docker run ubuntu. Nada aconteceu de novo. Mas vamos entender e aproveitar para conhecer outros comandos do Docker que vão nos auxiliar a entender se o container realmente executou ou não, e por que ele não exibiu nada.

### docker ps
Um comando muito útil que vamos utilizar bastante é chamado docker ps. Ele vai exibir quais containers estão em execução no presente momento. Vou limpar a tela e fazer docker ps, e ele exibiu só o cabeçalho.

```
docker ps
```

No fim das contas significa que minha tabela de containers em execução está vazia. Então não tem nenhum container efetivamente em execução.

### docker container ls
Apenas a título de curiosidade, outra maneira um pouco mais semântica para utilizar o docker ps, é o comando docker container ls. É a mesma coisa, um pouco mais verboso, mas ao mesmo tempo mais semântico. É o mesmo comando que será executado, então a saída tinha que ser a mesma também.

```
docker container ls

```

Nós já sabemos que nosso container não está em execução. Então como é que podemos ver todos os containers, inclusive os que já não estão mais em execução, para saber se o nosso docker run fez alguma coisa efetivamente ou não?

### docker ps -a | docker container ls -a
Esse comando pode ser simplesmente o docker ps –a, ou o docker container ls –a, que é a mesma coisa. Vamos executar.

Repara que ele tem nosso “hello-world”, que executamos no caso há 9 minutos. Tem o Ubuntu que fizemos no vídeo anterior e o outro Ubuntu que é o que acabamos de fazer na base da teimosia.

Vamos entender cada uma dessas colunas. 

#### CONTAINER ID
A primeira é o container ID, que é um identificador. O ID, obviamente, tem que ser único para cada container que vai ser criado.

#### IMAGE
Depois temos a imagem que foi usada como base para criação desse container. Temos docker run ubuntu duas vezes e docker run hello-world.

#### COMMAND
Depois o comando que foi executado ao criar esse container. Para os comandos do Ubuntu foi executado um bash, para o hello-world foi executado um /hello. Em seguida temos o momento que foi criado, um deles foi criado há um minuto, outro há 4 e outro há 9.

#### STATUS
E em seguida temos o status, que está como exited para todos. Por isso que eles não foram exibidos no docker ps ou no docker container ls, só quando passamos a flag -a.

#### PORTS
Depois temos a questão das portas, que vamos entender em breve. 

#### NAMES
E a coluna de names, que é simplesmente o nome que ele cria automaticamente para o container quando não especificamos um nome para ele. Por enquanto não estamos nos preocupando com isso, é só um preciosismo do Docker, que cria um nome aleatório para os containers.

Mas vamos voltar e responder à pergunta: por que o container não está em execução? A resposta já está na tela inclusive. É por causa da linha de comando.

No momento em que um container é executado a partir dessa imagem, está definido que quando o container subir e for executado ele vai executar o comando bash, no caso do Ubuntu. No caso da imagem do hello-world ele vai executar um /hello.

No momento em que executamos mais uma vez o docker run ubuntu, ele vai subir o container, vai executar o comando bash e pronto. Quando ele executa o comando bash ele já fez o que tinha que fazer, já cumpriu o objetivo dele.

Então voltando à nossa apresentação, no fim das contas o container foi executado. Ele subiu, executou o bash, fez o que tinha que ser feito, que era executar. E a partir desse momento não tinha mais nenhum processo segurando a existência desse container. Então por isso ele foi encerrado.

Para que um container esteja em execução deve ter no mínimo um processo dentro dele para que o container fique vivo. Então se não houver processos em execução, o container não vai ficar em execução. E como só pedimos para ele executar um bash, ele abriu, fez o papel e fechou.

Então essa é a primeira questão: no momento em que executamos o run, ele fez esse comando e fechou logo em seguida, porque nós não mantivemos nenhum processo em execução. Então vou limpar o terminal, e se executarmos o comando docker run ubuntu mais uma vez, o que podemos fazer?

Vou passar um docker run --help para lembrarmos do que vimos, na verdade. Quando nós especificamos a imagem, nós podemos passar um comando para que esse container execute.

Nós já sabemos que dentro do Ubuntu com um todo nós temos a linha de comando que nós podemos executar. Então eu quero que esse container, por exemplo, execute o comando de docker run ubuntu sleep 1d.

### comando 'sleep'

```
docker run ubuntu sleep 1d

```

Eu quero que o comando que o container execute quando esse Ubuntu subir seja um sleep de um dia. Então tecnicamente, quando esse container subir ele vai ter um processo de sleep travando ele por um dia. Será que vai funcionar? Vamos ver agora. Eu vou dar um “Enter”.

Ele travou o nosso terminal a princípio. Como ele travou nosso terminal, vamos abrir um novo e vamos executar docker ps.

Nós temos um container ID, com a imagem Ubuntu. O comando é um sleep 1d. Como esse comando vai demorar um dia para ser executado, esse container vai ter um tempo de vida de um dia. E ele está rodando, o status dele contém “Up” há 27 segundos. A questão de portas, mais uma vez, vamos relevar por enquanto. E o nome dele é “funny_pike”.

Entendemos agora como conseguimos manter um container em execução e porque ele parou de executar, quando simplesmente não especificamos um comando que deveria ser executado. Porque não tinha nada travando a execução dele, agora tem, que é o nosso sleep de um dia que passamos que esse container deveria executar.

E agora, a segunda pergunta que ainda temos que responder, voltando à nossa apresentação é: o que o docker run fez por baixo dos panos para que nosso container executasse daquela primeira vez?

### Processo de execuçao do docker
No momento em que tínhamos o nosso host, no caso o nosso Ubuntu, executando o comando docker run ubuntu ou docker pull ubuntu, ele simplesmente foi no Docker Hub e falou: “Eu preciso dessa imagem chamada Ubuntu”.

E o Docker Hub falou: “Eu tenho essa imagem e vou passá-la para você”. Então fizemos o download dessa imagem e depois o que o nosso host fez, naquela parte que dizia “digest sha256”, foi simplesmente uma validação para verificar a autenticidade da imagem, se realmente era a imagem que estávamos procurando.

Então no momento em que fazemos o docker run, se não tivermos a imagem localmente nós a buscamos no Docker Hub, fazemos uma validação em cima de um hash e executamos nosso container, que como vimos no caso do Ubuntu, vai ter geralmente um comando padrão a ser executado. Se colocarmos mais uma vez o -a para comparar, veremos que esse comando será o bash.

Então se simplesmente não tivermos cuidado, e vamos ver como lidar com isso no decorrer da criação dos nossos containers, nós simplesmente acabaremos criando containers zumbi. Eles vão subir e morrer logo em seguida, porque não teremos nenhum processo travando eles.

Mas no caso de agora, vimos que se mantivermos no mínimo um processo, o nosso container vai ficar sempre em execução.

Entendemos agora o que mantém um container vivo, em execução versus o que mantém um container parado e o que o docker run faz para que consigamos executar efetivamente um container quando não possuímos uma imagem localmente.

## Outros comandos importantes
Agora vamos entender mais alguns comandos que serão muito úteis para nós no decorrer desse curso para que consigamos desenvolver nosso conhecimento com o Docker.

Até então nós temos a criação de containers, já vimos o que o docker run faz, como ele funciona; a questão do comando padrão, que a princípio é executado quando executamos uma imagem. E vimos como sobrescrever esse comando, no caso colocando um sleep de um dia.

Mas o que mais é importante de saber? Tem uma coisa que ainda precisamos entender. Eu vou limpar a tela e fazer um docker ps novamente. Temos nosso container em execução com nosso sleep de um dia.

Ele está em execução, nós mantivemos um terminal travado para isso, o que é um pouco chato. Mas o que acontece, por exemplo, se eu quiser fazer um fluxo um pouco mais utilizável, por assim dizer?

Vamos fechar esse terminal que está travando o nosso. Se eu der um docker ps agora, ele vai manter o meu container em execução, porque eu fechei o outro.

### docker stop
Mas se eu quiser parar a execução desse container, eu posso executar o comando docker stop e passar para ele o ID ou o nome do container que eu quero. Eu posso copiar o ID ou o nome do container que eu quero parar e colar logo após o comando docker stop.

```
docker stop 912812761ec
```

Ele vai demorar um tempo e vai parar a execução desse container. Então no momento em que damos um docker ps de novo, não teremos mais nenhum output. Não temos mais nenhum container em execução.

### docker start
E se eu quiser por algum motivo reexecutar o meu container que foi parado? Vou fazer docker ps –a.

Eu posso pegar o ID do meu container e simplesmente fazer docker start, e passar o ID dele mais uma vez. Nesse momento ele vai voltar a executar o meu container.

```
docker start 912812761ec
```

Se fizermos um docker ps de novo, ele está em execução. Ele foi criado há 11 minutos, mas o status dele é de execução só há 4 segundos.

Então conseguimos parar e reexecutar os nossos containers com esses dois comandos, o docker start e o docker stop.

Para que eu consiga a fazer inicialização a partir de um start é preciso que meu container esteja em estado de parada. E vice-versa: se eu quiser parar ele precisa estar no estado de execução.

Vamos um pouco mais além. O meu container do Ubuntu que estamos usando para exemplificar está em execução. Mas eu como é que eu interajo com ele? Porque a princípio nós não estamos conseguindo fazer nada. Ele está em execução, mas não está servindo para nada.

Então como é que eu posso interagir com esse container de maneira interativa para que eu consiga fazer alguma coisa?

### docker exec 
Existe um comando, também dentro desse universo todo do Docker, que é o docker exec. Eu posso simplesmente falar que eu posso executar algum comando dentro do meu container em modo interativo.

Então para que seja em modo interativo eu vou adicionar docker exec -it ao comando. O I é de modo interativo, e o T é que eu quero acessar o tty, o terminal padrão desse container.

```
docker exec -it 912812761ec bash
```
E eu coloco também o ID do container que eu quero fazer isso, e em seguida eu posso executar algum comando.

Qual comando eu posso usar para navegar dentro do meu Ubuntu, que estamos assumindo esse conhecimento de terminal? Eu posso executar simplesmente um bash.

Ele alterou meu terminal, agora ele está com um usuário root nessa máquina, e se formos comparar é exatamente o ID do meu container.

Então agora eu estou dentro do terminal do meu container. Então tudo que eu fizer agora, como vimos naquela questão dos namespaces, estará devidamente isolado.

Se voltarmos à apresentação, por exemplo, nós conseguiríamos ver que na verdade tudo está devidamente isolado por conta dos namespaces. Nós temos processo isolado, rede isolada, a intercomunicação de processos, file system, o Kernel.

Se voltarmos para o terminal e acessarmos, por exemplo, a home desse container e criarmos um arquivo touch eu-sou-um-arquivo.txt, o que vai acontecer? Eu vou dar um ls e vai aparecer o meu arquivo.

Mas se eu vier na minha máquina e abrir minha home não aparece nada, porque são sistemas de arquivos isolados, graças ao namespace de MNT. É o file system que está completamente isolado.

Nós podemos fazer diversas coisas agora nesse Ubuntu, porque ele vai estar devidamente isolado do nosso sistema. Nós poderíamos executar os comandos apt, com apt-get update, apt-get install, conforme nossa demanda, e vai estar tudo isolado do nosso sistema original.

Vou parar essa execução do apt update, porque não estamos nos importando com isso e vou limpar a tela com “Ctrl + L”. Tudo que fizermos estará dentro desse terminal, desse container.

No caso do Ubuntu, podemos até executar o comando top, e ele vai mostrar os processos que estão em execução, do nosso usuário root, que é exatamente nosso sleep que estava mantendo o nosso container em execução, e o nosso bash, que estamos escutando agora.

Então nosso container está com dois processos neste momento no nosso usuário root.

### sair do container
Vou parar novamente e dar um “Ctrl + L”. Vou dar um “Ctrl + D” para sairmos do container e um docker ps de novo. Ele ainda vai estar em execução porque o nosso sleep ainda está em execução.

Eu vou dar um docker stop nesse meu container e reexecutar. E vou dar um docker start mais uma vez para vermos o que acontece.

Mais uma vez vou executar aquele comando para acessarmos o terminal em modo interativo. E nesse momento, se eu voltar para minha home e der um ls, está aparecendo meu arquivo.

Agora vou dar um top. Ele simplesmente resetou toda minha árvore de processos, ele deu um sinal de SIGKILL, por assim dizer, em toda minha árvore de processos, porque eu parei todos os processos e os recomecei. Então o tempo de execução deles foi zerado. Toda a contagem do processo foi reiniciada.

### docker pause
Então essa é uma questão que quando executamos o stop acontece. Vou sair do terminal mais uma vez e executar outro comando como, por exemplo, docker pause e passar o ID do nosso container de novo. Com isso nós podemos também pausar o nosso container.

```
docker pause 912812761ec
```

Se fizermos docker ps ele vai aparecer ainda, mas no status de pausado, e não de parado. E se dermos um docker ps de novo, repara que a contagem do status dele continua.

### docker unpause

Se eu tentar acessar o container agora eu não vou conseguir, porque ele está pausado. E eu posso simplesmente despausar esse container com o comando docker unpause.

```
docker unpause 912812761ec
```

E se eu tentar acessar agora eu vou conseguir. E se eu der um top vemos que nossa árvore de processos a princípio foi mantida, porque ele mantém todo o nosso fluxo de execução, assim como nossos arquivos. Mas essa é uma situação menos agressiva em relação ao stop que podemos fazer.

Então nós vimos como podemos parar um container, dispará-lo, no caso, voltar a execução. Vimos como podemos pausar e despausar e a diferença entre cada um desses tipos de operação.

E vimos também que devidamente os nossos containers estão isolados do nosso host original, graças à utilização do namespace NMT.

Eu vou fazer um último detalhe para ficarmos com uma pulga atrás da orelha. Eu vou dar um docker ps. Mas agora eu vou pegar esse meu container ID e fazer um docker stop mais uma vez.

### parar um container instantaneamente com -t=0
E se eu quiser evitar que ele fique demorando 10 segundos para executar eu posso colocar a flag -t=0, antes do nome do meu container, para que não tenha nenhum tempo para parar, porque por padrão ele espera 10 segundos para nosso container parar de maneira saudável. Mas se quisermos nos apressar, podemos colocar o -t=0, que ele vai parar instantaneamente.

```
docker stop -t=0 912812761ec
```

### docker rm
E agora eu posso remover esse meu container com docker rm, que é o comando de remoção, passando o ID do meu container. Com isso ele vai remover.

```
docker rm 912812761ec
```

Se eu criar mais uma vez com o nosso docker run Ubuntu sleep 1d e manter um sleep de um dia, ele vai travar nosso terminal. E por fim, se eu abrir um novo e fizer um docker exec –it 48aac971d7fb bash neste nosso terminal que foi criado, nós acessamos o nosso terminal. Se entrarmos na nossa home com ls o nosso arquivo sumiu.

```diff
+ Como o nosso container deixou de existir e ele estava completamente isolado dos outros containers e do nosso host, todo o conteúdo dele foi perdido.
```

Então o container tem essa questão de ser efêmero nesse sentido. Os containers devem estar sempre prontos para deixar de ser executados, e nós devemos estar prontos para perder esses dados caso não configuremos nada em relação a isso. Então é uma questão que também veremos mais à frente, como lidar com a persistência de arquivos em container.

E para finalizar, mais um comando interessante é o seguinte: ao invés de ter que fazer docker exec toda hora depois de dar um sleep, teria que ter uma maneira mais prática de já começarmos a executar nosso container e mantê-lo em execução sem precisar ficar dando sleep, sem precisar ficar roubando nesse sentido.

Nós podemos simplesmente dar um docker run ubuntu bash, mas indo mais além, podemos falar que queremos executá-lo em modo interativo, assim como nosso exec: docker run -it ubuntu bash. E vamos simplesmente criar um container novo, e já estamos diretamente no terminal dele.

```
docker run -it ubuntu bash
```

Se abrirmos mais um terminal e dermos um docker ps, nós temos o que criamos com o sleep de um dia ainda há pouco, e o nosso bash que acabamos de criar há 10 segundos.

Se fizermos qualquer coisa dentro dele, como ls, criar algum arquivo na home, dá certo. Mas no momento em que eu sair desse terminal ele vai matar o meu container, por aquele motivo que explicamos. Agora não há mais nenhum processo, antes tinha o nosso bash, agora não tem mais o nosso terminal que estava travando a execução de um processo para que o container se mantivesse em execução.

Então a partir de agora, no momento em que finalizamos a execução do nosso único processo, o container morreu.

Nós vimos agora alguns pontos que são muito importantes nessa parte de ciclo de vida dos containers, e entendemos que por eles serem devidamente isolados, nós não conseguimos manter as informações de alguma maneira. Mas vamos responder isso também em breve, fique tranquilo, fique tranquila.

Essa aula foi para entendermos mais alguns comandos que são muito úteis e interessantes, e alguns parâmetros que podemos e devemos utilizar com o Docker. 

## Mapeando portas
Agora veremos um exemplo mais visual para vermos todo o fluxo, e veremos como interagir efetivamente com nosso container para ver uma saída, um output real, mais bonito, para realmente vermos como devemos interagir com um container.

Ao invés de voltarmos a executar docker run com Ubuntu e hello-world, vamos um pouco mais além. Vamos executar um exemplo prático de uma aplicação web cuja saída vamos conseguir visualizar através do nosso navegador.

### docker samples
Que aplicação é essa? Se voltarmos no Docker Hub, existe um grupo de usuários, uma organização chamada “dockersamples”, que disponibiliza diversos tipos de aplicação a fim de exemplificar a utilização do Docker, um pouco mais bonitas e elegantes.

Então repara que ao contrário da nossa imagem do Ubuntu, essa imagem do “dockersamples” não é oficial.

E além de ela não ter aquele símbolo de verificado, como no Ubuntu, quando uma imagem não é feita por usuários reconhecidos pela comunidade ela segue o seguinte padrão: vai ter um nome do usuário ou organização barra o nome da imagem. Então conseguimos reconhecer que essa imagem não é oficial por conta desse padrão.

Então é uma boa prática você sempre tentar manter a utilização de imagens oficiais dentro do seu projeto o máximo possível, mas nesse caso estamos utilizando essa imagem a fim de exemplificar e visualizar o Docker em execução com um container.

Eu vou copiar mais uma vez o nome dessa imagem e vou fazer docker run, passando o dockersamples/static-site.

```
docker run dockersamples/static-site
```

Mas vimos quando executamos o Ubuntu que se simplesmente executarmos um docker run e o nosso container ficar em execução, assim como fizemos com o sleep naquele momento, ele vai travar o nosso terminal.

### terminal detached
Então se eu quiser executar esse comando e manter o comando em background no terminal, para que eu consiga manter o terminal em execução sem travar, eu posso passar a flag -d, de detached.

```
docker run -d dockersamples/static-site
```

```diff
+ Então ele vai executar o container baseado nessa imagem, mas não vai travar o terminal. 
```

Vamos ver o que vai acontecer. Ele vai efetuar o download das camadas necessárias para que esse container baseado nessa imagem “dockersample/static-site” execute.

Nós já vimos esse fluxo, ele está indo no Docker Hub, validando todas as camadas que ele precisa para fazer a execução desse container, que é basicamente essa imagem que ele encontrou. E está fazendo a verificação depois de fazer todo o download e extração das camadas na nossa máquina.

Então ele terminou a extração, fez o download, e repare que ele não travou nosso terminal, graças à flag -d. Mas agora se eu executar um docker ps, vamos ver o que vai acontecer.

Nós temos nosso docker ps, nosso ID do container, nossa imagem baseada, o comando que ele manteve em execução; que ele foi criado há 22 segundos e que ele está em execução há 19 segundos.

Então ao contrário do Ubuntu e do “hello-world”, por exemplo, a imagem que foi usada para criação desse container definiu que o comando para o container ser executado trava o terminal.

Então esse comando que começa com /bin/sh –c, é um comando que mantém um processo vivo dentro do terminal do nosso container.

Então o container em si vai continuar em execução. Se fizermos docker ps várias vezes, a aplicação em si está em execução, graças ao comando padrão que foi executado quando o container subiu.

E agora temos a nossa coluna de portas falando que a nossa aplicação está sendo executada na porta 80 e na 443, além do nosso nome, que é “friendly_lamarr”.

### isolamento das interfaces de rede (localhost)
Então se nossa aplicação está sendo executada na porta 80, vamos abrir no nosso navegador o nosso “localhost:80”. Não acessou. Por quê? Mais uma vez voltando à nossa apresentação, graças aos namespaces, nesse caso principalmente ao NET, nós temos um isolamento das interfaces de rede.

Então a porta 80 do meu container não é exatamente uma porta que já está mapeada na minha máquina, no meu host. Eu não vou conseguir acessá-la diretamente assim, elas estão isoladas.

Então vamos voltar ao nosso terminal para entender o que está acontecendo. Precisamos visualizar o seguinte: essa porta 80 é de uso do container, e é a porta 80 de dentro da interface de rede do container. Se eu quiser acessá-la de outras maneiras, nós precisamos expor essa porta de alguma maneira.

Mas antes disso inclusive, precisamos fazer outra coisa. O nosso container já está em execução. Nós vamos parar e remover esse container de uma vez só, então podemos ser um pouco mais agressivos.

### docker rm --force
Ao invés de fazer o docker stop e depois o docker rm, podemos copiar o ID e fazer docker rm 1b6d75073457, passar diretamente o ID e acrescentar --force. Assim ele vai parar e remover o container de uma vez só. Agora se eu fizer docker ps, não tem mais nenhum container em execução.

```
docker rm 123344212312 --force
```

### docker run -P (expor as portas)
Vamos executar agora mais uma vez o docker run –d -P dockersamples/static-site. Só que ao invés de só executarmos esse comando mais uma vez, agora vamos colocar a flag -P, com P maiúsculo.

```
docker run –d -P dockersamples/static-site
```

Vamos descobrir agora o que essa flag vai fazer. Ele vai executar o nosso container, sem travar mais uma vez, por conta do -d; e ele não fez o download porque ele já tinha o conteúdo na nossa máquina agora.

E se fizermos um docker ps, ele está fazendo um mapeamento maluco na nossa coluna de portas, que está um pouco difícil de entender. Mas o resto é a mesma coisa.

### docker port
Vamos executar um comando agora docker port b0e93e405db6 seguido do ID, que é um comando voltado para mostrar como está o mapeamento de portas de um container em relação ao host.

```
docker port b0e93e405db6
```

Então vamos passar o container, e agora ele está falando que a porta 80 do meu container foi mapeada para a porta 49154 do meu host.

```
443/tcp -> 0.0.0.0:49153
443/tcp -> :::49153
80/tcp -> 0.0.0.0:49154
80/tcp -> :::49154
```

Isso significa que se agora no meu navegador eu executar o “http://localhost:49154”, nós conseguimos acessar o nosso container. O conteúdo do nosso container foi acessado, porque agora fizemos um mapeamento de uma porta interna do container para uma porta do nosso host.

E no fim das contas nós poderíamos ter feito isso também de uma maneira mais bem definida. Vamos executar o nosso docker rm, passando o ID do nosso container com --force. Vamos executar um novo container.

Como eu matei o container, se voltarmos para o navegador e dermos um “F5”, não tem mais o container, não tem mais conteúdo.

### mapear manualmente uma porta (-p 8080:80)
Mas se executarmos o docker run -d -p dockersanples/static-site mais uma vez, só que agora com a flag -p com P minúsculo, nós conseguimos fazer um mapeamento específico de uma porta do nosso host.

Por exemplo, vamos mapear a porta 8080 do nosso host. Ela deve refletir em qual porta do nosso container?

```
docker run -d -p 8080:80 dockersanples/static-site
```

Nós vimos que por padrão ele expôs naquelas colunas de porta as portas 80 e 443. Então quero que a porta 8080 da minha máquina reflita na porta 80 do meu container.

Ele vai executar. E agora, se voltarmos para o navegador e acessarmos “localhost:8080”, conseguimos fazer esse mapeamento de uma porta agora específica do nosso host para o nosso container.

Voltando na nossa apresentação mais uma vez, nós temos esse isolamento de rede, mas conseguimos fazer um mapeamento para que consigamos acessar o conteúdo do nosso container e vê-lo de maneira que consigamos validar o que está acontecendo.

Fazemos isso para que não fiquemos às cegas do que está sendo executado dentro do nosso container e para que, no fim das contas, nós consigamos expor nossa aplicação também para que algum usuário consiga acessar.

Nessa aula fizemos essa parte de voltar a execução e entender mais alguns atalhos, como a flag -d, por exemplo, que mantém nosso terminal destravado; e também fazer o mapeamento de portas entre o nosso host e o nosso container.

## o que aprendemos?
* o Docker Hub é um grande repositorio de imagens que podemos utilizar
* a base dos containers sao as imagens
* como utilizar comandos acerca do ciclo de vida dos containers, como: docker start, para iniciar um container que esteja parado; docker stop para parar um que esteja rodando; docker pause para pausar um container e docker unpause para iniciar um container pausado
* mapear portas de um container com as flags -p e -P

## Entendendo imagens
Chegou o grande momento de explicar o que são as imagens. Nós vamos entender efetivamente agora o que são as imagens, como elas funcionam, como elas viram containers e como criar nossas próprias imagens, porque não podemos depender só do trabalho alheio para desenvolver o nosso.

Então chegou agora a hora de entender o que são as imagens. Por enquanto nós estamos aceitando que as imagens são uma receita para criar um container. Mas efetivamente como elas funcionam?

### o que sao imagens?
Uma imagem nada mais é do que um conjunto de camadas. 

É um conjunto de camadas, e quando juntamos essas camadas nós formamos imagens.

E essas camadas em si são independentes, cada uma tem seu respectivo ID, por exemplo, cada uma tem o seu respectivo identificador.

Então vamos voltar mais uma vez para o caso do dockersamples no nosso terminal para visualizarmos.

### docker images ou docker image ls
Quando fazemos um docker run na nossa imagem, nós podemos ver agora as imagens que temos baixadas no nosso sistema através do comando docker images, ou docker images ls.

```
docker images
docker image ls
```

Dando um docker images eu posso ver que tenho baixada a nossa imagem “dockersamples/static-site”, com a tag latest e seu respectivo ID. E ela foi criada há 5 anos pelo grupo do dockersamples. E o tamanho dela é de 191 MB.

### docker inspect
Nós podemos ir um pouco mais além. Podemos dar o comando docker inspect em uma imagem. Vou fazer novamente o docker images para copiar o ID da imagem. E agora vou fazer docker inspect, passando o identificador do que nós queremos inspecionar.

```
docker inspect 1231234332
```

Nós temos diversas informações. Tem um conjunto muito grande de informação que podemos saber detalhadamente sobre determinado recurso dentro do nosso Docker.

Temos o ID, qual é a tag do repositório, o digest que foi utilizado para validação da imagem, se tem alguma imagem que é um parent, uma imagem pai ou mãe, a data de criação, o container, container config. Então temos diversas informações acerca dos recursos que podemos ter dentro do Docker.

Inclusive, no final nós conseguimos ver mais informações na parte de layers, que são as camadas. Mas podemos ir um pouco mais além. Tem um comando específico para ver quais são as camadas de uma imagem. Vou limpar o terminal.

### docker history
Temos o comando docker history. Vou fazer docker images para pegar mais uma vez o ID. E vamos fazer o comando docker history, passando esse ID. Nós temos a nossa imagem, que é a f589ccde7957, e ele mostra todas as camadas. Ela tem 13 camadas para essa imagem.

```
docker history f589ccde7957
```

E quando essas camadas são aglutinadas, empilhadas umas nas outras, elas formam essa imagem final que é “dockersamples/static-site”.

E caso alguma outra imagem venha a depender desses camadas, nós conseguimos reutilizá-las. Vamos entender isso daqui a pouco.

Mas ele mostra detalhadamente qual é o tamanho de cada uma dessas camadas, a ordem de cada uma delas, o command, workdir, copy. Nós conseguimos ver também a data de criação. Conseguimos ver todas essas informações e entender o que está acontecendo.

Mas agora que nós entendemos que uma imagem é um conjunto de camadas empilhadas para formar determinada regra de execução de um container, voltando à nossa apresentação, vamos ver como podemos visualizar um pouco melhor.

Quando fizemos nosso docker run pela primeira vez ou simplesmente um docker pull para não executar o container, mas só baixar a imagem, nós fazemos o download das nossas imagens, das nossas camadas.

Mas pode ser que, por exemplo, como eu falei para vocês agora, no nosso host nós já tenhamos algumas das camadas que queremos. Então no momento em que fizermos um pull ou um run, que vai fazer um pull consequentemente, nós vamos fazer simplesmente download só das camadas que necessitamos.

Então o Docker é inteligente o suficiente para reutilizar essas camadas para compor novas imagens.

Então conseguimos ter uma performance muito boa nesse sentido, já que não precisaremos ter informação duplicada ou triplicada, enfim. Porque nós conseguimos reutilizar as camadas em outras imagens. Isso por si só já é muito interessante. Então no fim das contas, conseguimos ter essa reutilização.

### imagens sao read only
Mas o que mais podemos ver na parte da criação de imagens? No fim das contas, quando temos a nossa imagem, ela é read only. Isso significa que não conseguimos modificar as camadas dessa imagem depois que ela foi criada.

Então voltando ao nosso container, no momento em que nós temos essa imagem “dockersamples/static-site”, ela é imutável, assim como, por exemplo, a imagem que temos do nosso Ubuntu.

Vou fazer mais uma vez docker run ubuntu –it bash para executar de modo interativo o nosso bash. Eu já tinha apagado a imagem, e veremos como fazer isso aos poucos, mas vamos baixar a camada necessária para ter a nossa imagem de execução para que consigamos executar nosso container do Ubuntu. Ele baixou, extraiu e vai abrir o nosso terminal.

Ele deu um erro porque eu coloquei na ordem errada. O correto é docker run –it ubuntu bash.

Nós vimos que tínhamos criado na nossa home, por exemplo, um arquivo qualquer com o comando touch um-arquivo-qualquer.txt. Nós estamos meio que escrevendo dentro do container.

Mas como estamos conseguindo fazer isso se a imagem que gera o nosso container é apenas para leitura, ou read only? Se ela é bloqueada para escrita como é que o container consegue escrever informação dentro dela?

### container sao imagens com uma camada de R/W
Porque no fim das contas, quando criamos o container, o container nada mais é do que uma imagem com uma camada adicional de read-write, de leitura e escrita.

Então quando criamos um container nós criamos uma camada temporária em cima da imagem, onde conseguimos escrever informações. E no momento em que esse container é deletado, essa camada extra também é deletada.

Por isso que quando fizemos aquele experimento anteriormente, a nossa informação dentro do container era perdida quando nosso container era apagado. Porque essa camada é temporária, bem fina e leve para que o container tenha um ambiente de execução muito leve e fácil de ser executado.

E agora voltamos naquela primeira pergunta da primeira parte desse curso, que era por que os containers são tão leves?

```diff
+ Além da parte de eles serem simplesmente processos dentro do nosso sistema, nós também podemos falar que quando um container entra em execução, nós estamos sempre reaproveitando a mesma imagem.
```

Porque como a imagem é apenas de leitura, nós podemos ter um, dois, 100 ou 10 mil containers baseados na mesma imagem. A diferença é que cada um desses containers vai ter só uma camada diferente de um para o outro de read-write.

E como essa camada é extremamente leve, a fim de manter essa performance, nós temos uma reutilização da imagem para múltiplos containers.

Então no fim das contas o que acontece é que quando definimos um container ou outro baseado na mesma imagem também logo em seguida, o tamanho do container no fim das contas vai ser só o tamanho da camada de escrita que estamos gerando para ele, porque a imagem em si será reutilizada para cada um deles.

E isso é muito legal. Nós veremos um experimento prático em breve, conforme formos avançando na criação e no fluxo das nossas imagens.

Mas basicamente agora nós entendemos porque efetivamente o container é tão leve e otimizado. Porque ele consegue reaproveitar as camadas das imagens prévias que já temos.

E quando criamos novos containers ele simplesmente só reutiliza as mesmas imagens e camadas, consequentemente, e utiliza a camada de read-write para utilizar de maneira mais performática o que ele já tem no ecossistema do Docker. Isso por si só é muito legal.

E no fim das contas, basicamente é isso. Vamos entender ainda a partir de agora como definir um arquivo chamado “dockerfile” que vai nos ajudar a criar nossas próprias imagens, e no fim das contas como gerar os nossos containers através das imagens que vamos criar.

Agora desmistificamos efetivamente o que é uma imagem, como transformar uma imagem num container e porque o container é tão leve.

E ainda vai ficar mais fácil de entender conforme formos avançando e criando a nossa própria imagem e vermos como são as etapas de criar uma camada, uma imagem, transformar em container.

## Criando a primeira imagem
Agora vamos criar nossa primeira imagem. Vimos o que é uma imagem, como ela vira um container, qual a diferença de imagem para container.

Mas precisamos entender agora como criar nossa imagem efetivamente para não dependermos 100% de imagens de outras pessoas diretamente. Nós vimos na nossa apresentação que precisamos no fim das contas seguir esse fluxo.

Nós precisamos definir esse arquivo que é o Dockerfile. E a partir dele vamos criar nossa imagem. E imposta nossa imagem basta executar usando run para que o container seja gerado a partir da imagem.

Voltando ao nosso projeto como um todo, nós temos uma aplicação Node. Não precisa se preocupar, nós não vamos entrar em nenhum detalhe específico de Node e de nenhuma linguagem de programação, você não precisa saber Node.

Só estamos usando como exemplo para termos uma aplicação efetivamente para empacotar e transformar numa imagem e depois num container. Não precisa se preocupar quanto a isso.

O que nós queremos no fim das contas é que quando formos executar nosso container e acessarmos via host, por exemplo, mapeando as portas, que tenhamos essa visualização, que é a nossa aplicação realmente em execução.

Mas não queremos simplesmente clicar duas vezes no arquivo e abrir. Nós queremos um servidor que disponibiliza essa aplicação para nós. Então nós precisamos de alguma maneira colocar todo esse conteúdo dentro de uma imagem, instalar o Node, que será responsável por executar o servidor.

E no fim das contas, quando nosso container executar, queremos que ele execute algum comando que mantenha esse servidor em execução.

Então o que precisamos fazer de início é o seguinte: eu estou utilizando o Visual Studio Code para fazer a edição de texto nesse caso, mas você pode usar o da sua preferência.

O que vou fazer é criar um novo arquivo dentro da nossa pasta do nosso exemplo, que estará disponível para você fazer o download. E dentro dessa pasta vou criar um arquivo chamado “Dockerfile”, que é basicamente o arquivo que nós vamos criar. E vou dar um “Enter”.

Repara na parte superior esquerda que o VS Code tem esse charme de reconhecer que é um arquivo Dockerfile. E agora nós vamos simplesmente, dentro desse arquivo, definir como vai ser a criação da nossa imagem. O que nós queremos fazer?

Nós queremos que dentro do nosso projeto como um todo nós tenhamos o Node para que consigamos rodar um servidor.

Então se queremos usar o Node como base para nossa aplicação, nós podemos pegar emprestado do que já desenvolveram. Então vamos fazer o pull dessa imagem já existente para que possamos utilizar no nosso projeto, e a partir daí só fazer as nossas modificações para customizar o projeto à nossa maneira.

No fim das contas nós precisamos do Node. Mas como nós colocamos o Node dentro da nossa imagem por padrão? Nós podemos colocar a princípio um Ubuntu, e dentro desse Ubuntu podemos instalar o Node e fazer toda a configuração necessária.

Mas lembra que não precisamos ter necessariamente um sistema operacional dentro do nosso container. Nós podemos simplesmente utilizar alguma imagem que disponibilize o Node para nós, por exemplo, a própria imagem do Node no Docker Hub, que é uma imagem oficial, inclusive.

Se olharmos a descrição como um todo, tem todas as versões do Node que nós podemos definir, como as versões 16, 17, 14, 12.

Então o que nós podemos fazer nesse cenário? Nós podemos simplesmente falar que queremos pegar uma dessas versões do Node para que possamos executar o nosso projeto, usar essa imagem como base para a nossa. Então a partir da imagem que nós vamos definir nós vamos começar a usar a nossa.

Como podemos pegar essa imagem emprestado? Por exemplo, queremos usar o Node na versão 14, então dentro do nosso “Dockerfile” eu quero pegar a partir do Node na versão 14. Como eu explicito a versão? Utilizando dois pontos e a versão que eu quero: FROM Node:14. Então a partir do Node na versão 14.

```
FROM node:14
```

Como eu sei que é a versão 14? Porque na documentação da imagem do Node ele está mostrando quais são as tags suportadas, inclusive a 14. E a partir do Node na versão 14, o que nós queremos fazer? Queremos colocar todo o nosso projeto, que são esses arquivos, menos o próprio “Dockerfile”, dentro dessa imagem. Então nós queremos copiar esse conteúdo do nosso host para a nossa imagem.

Para isso podemos simplesmente colocar COPY. Nós queremos copiar todo o conteúdo do nosso diretório atual. Em que diretório está o nosso “Dockerfile”? No diretório “exemplo.node”.

Então todo o conjunto do nosso diretório atual nós queremos copiar para algum diretório dentro do nosso container, por exemplo, para uma pasta chamada “/app-node”, só para termos a distinção do que nós queremos fazer: COPY . /app-node.

Então a partir desse momento nós estamos copiando esse conteúdo do diretório do nosso host para o diretório de dentro da nossa imagem que vai virar um container, chamada “/app-node”.

```
FROM node:14
COPY . /app-node
```

Nós queremos executar o comando RUN npm install. Só que esse comando terá que ser executado dentro do nosso diretório /app-node, para que consigamos instalar as dependências da nossa aplicação.

Caso você não conheça Node, esse comando está sendo responsável só por instalar as dependências que o nosso projeto precisa em um projeto Node. Então basicamente estamos instalando as dependências e o Node está resolvendo isso automaticamente.

```
FROM node:14
COPY . /app-node
RUN npm install
```

E por fim nós queremos que o ponto de entrada do nosso container, ao executar esta imagem e começar a ter seu container devidamente em execução, seja startar a aplicação, então eu faço ENTRYPOINT npm start.

```
FROM node:14
COPY . /app-node
RUN npm install
ENTRYPOINT npm start
```

E isso também tem que ser executado dentro desse diretório /app-node. Só que nós teríamos que ficar passando em todos os lugares o “/app-node”. Será que podemos resolver isso de uma maneira mais simples?

Eu quero que, por exemplo, esses comandos todos, por padrão, sejam executados no meu diretório que eu estou atualmente. E como é que eu defino o diretório que a imagem vai tratar como padrão? Qual será o meu diretório de trabalho, por assim dizer?

Para isso eu tenho a instrução WORKDIR, e com ela podemos definir o nosso diretório padrão: WORKDIR /app-node.

```
FROM node:14
WORKDIR /app-node
COPY . /app-node
RUN npm install
ENTRYPOINT npm start
```

Inclusive, no nosso COPY eu posso fazer um COPY de ponto. Esse ponto é o nosso diretório atual dentro do nosso host, para ponto também, que vai ser o nosso diretório atual dentro da nossa imagem: COPY . .. E qual será nosso diretório atual? O nosso /app-node, que foi definido através do nosso WORKDIR.

```
FROM node:14
WORKDIR /app-node
COPY . .
RUN npm install
ENTRYPOINT npm start
```

Então o que nós estamos fazendo? Nós estamos definindo que vamos utilizar imagem do Node na versão 14 como base para nossa imagem. E nós vamos definir o nosso diretório de trabalho padrão como sendo o /app-node.

Vamos copiar do diretório atual, onde está o nosso “Dockerfile” do nosso host, que é a pasta “exemplo-node”, para a pasta atual dentro da nossa imagem “/app-node” que foi definida dentro do nosso WORKDIR.

E vamos executar o comando npm install enquanto a imagem estiver sendo criada. Esse comando será executado na etapa de criação da imagem. Então esse npm install será executado enquanto a imagem estiver sendo criada.

E quando o container for executado a partir dessa imagem, o comando executado vai ser o npm start. Vamos dar um “Ctrl + S” agora, vamos ao nosso terminal e vamos acessar cd Desktop/exemplo-node/.

### docker build
Como é que a partir desse Dockerfile eu posso gerar uma imagem? Através do comando docker build.

```
docker build -t danielartine/app-node:1.0
```

Também passamos o -t para podermos criar um nome, etiquetar nossa imagem. No caso vou colocar o meu nome, então docker build -t danielartine/app-node:1. Com o :1 nós podemos explicitar qual é a versão que estamos criando. Eu vou colocar a versão 1.0 só para ficar mais bonito. Então docker build ´t danielartine/app-node:1.0.

Em qual contexto tudo isso terá que ser executado? No contexto de diretório atual, ou seja, ponto, que é a referência ao diretório atual: docker build -t danielartine/app-node:1.0 ..

Eu dou um “Enter”, e no Docker Hub, nesse exato momento, ele vai pegar a imagem do Node na versão 14 e baixar. Então ele vai pegar todo esse conteúdo para a nossa máquina, como já vimos que o Docker faz, e vai construir uma nova imagem utilizando essa como base.

Então no momento em que isso terminar nós vamos executar o comando docker history para ver o que ele vai fazer no fim das contas e ver como essa imagem vai se comportar dentro do nosso sistema.

Com o que precisamos nos preocupar agora? Fazendo um breve apanhado no nosso Dockerfile, ele está na etapa FROM Node:14, onde ele foi no Docker Hub e pegou a imagem do Node na versão 14. É isso que ele está fazendo agora.

Mas um ponto interessante: você pode me perguntar como eu sei dessas instruções, como eu as deduzi. Essa é uma pergunta muito boa.

Nós podemos entender tudo isso através da própria documentação do Docker, que eu vou mostrar para vocês. É uma documentação muito completa, na qual podemos e devemos sempre nos basear para seguir os nossos projetos e criação de imagens.

https://docs.docker.com/engine/reference/builder/

Nós temos todas as principais instruções para a criação de uma imagem. Ele mostra os comandos e as principais instruções. Ele tem o FROM, que usamos agora há pouco; ele mostra como todas as sintaxes funcionam, como escapar caracteres.

Temos o ADD, o COPY. Nós ainda não vimos algumas, mas veremos, por exemplo, o ENV, o EXPOSE. Já vimos o FROM. Com o LABEL podemos etiquetar e colocar algumas labels na nossa imagem; podemos definir algumas questões de volume, que ainda não sabemos o que é; o WORKDIR, que já vimos.

Ele tem diversos exemplos que podemos ver como funcionam dentro da documentação e aplicar aos nossos projetos. Então vou deixar também o link da documentação nas atividades para vocês conseguirem ver como é que funciona.

Mas ainda teremos outros exemplos de criação de imagens. Esse é só o primeiro para entendermos realmente como vai funcionar.

Voltando para o terminal, ele está terminando de extrair nesse momento as últimas camadas dos downloads que ele fez da imagem do Node. Então vamos ver qual vai ser o resultado final.

Ele está agora na etapa de WORKDIR, depois copiando os arquivos, executando o npm install. E por fim, no ENTRYPOINT ele definiu o npm start. Agora vou limpar o nosso terminal e vou fazer docker images. Olha o que temos agora.

Temos “danielartine/app-node” na versão 1.0 e temos o ID dessa imagem. E se agora eu simplesmente fizer um docker run nessa imagem “danielartine/app-node” e fizer um mapeamento?

Lembra que nós definimos a nossa aplicação dentro do container, mas ela é isolada? Então como eu posso agora saber em qual porta essa aplicação está rodando dentro do meu container?

A princípio nós precisaríamos ser um pouco malandros. Se olharmos dentro do nosso “index.js”, veríamos que ela está sendo executada na porta 3000. Tem um breve problema que precisaremos resolver. Mas vamos colocar isso no terminal.

Na nossa máquina nós vamos querer na porta 8080 de novo, mas nós queremos que a porta 8080 reflita na porta 3000, que é onde vimos que nossa aplicação vai ficar em execução dentro do nosso container: 

```
docker run -d –p 8080:3000 danielartine/app-node
```

Vou colocar também um -d para ficar em modo detached. E faltou especificar a versão, que é a 1.0, então docker run -d -p 8080:3000 danielartine/app-node:1.0.

Na verdade ele deu um problema porque a porta 8080 já está em uso por causa dos nossos exemplos anteriores. Obviamente não podemos usar a mesma porta, então vou mudar para 8081.

```
docker run -d –p 8081:3000 danielartine/app-node
```

E agora se viermos no nosso navegador e tentarmos acessar o “http://localhost:8081”, está tudo funcionando, conseguimos acessar a nossa aplicação agora de maneira conteinerizada.

Então nós criamos nossa própria imagem e executamos um container a partir dela. Isso é muito legal.

Mas ainda tem algumas questões que precisamos resolver. Nós deixamos em aberto aquela questão de como saber em qual porta nossa aplicação estava em execução, como sabemos como expor ou não expor.

Tem algumas questões que precisamos deixar menos obscuras para o nosso Dockerfile, para que consigamos deixar tudo muito mais fácil de ser entendido.

Nós criamos nossa primeira imagem, mas precisamos entender mais alguns detalhes acerca da criação de imagens.

#### projeto exemplo
app-exemplo-node.zip

### Incrementando a imagem
Agora vamos voltar ao nosso projeto e entender algumas questões que ficaram pendentes e que poderíamos aperfeiçoar na criação do nosso Dockerfile, até mesmo em relação ao projeto.

Primeiro vamos voltar ao nosso terminal e antes de mais nada, vamos entender um comando novo, que na verdade nem é tão novo, é a junção de dois comandos que já conhecemos. Vamos fazer docker ps e olhar que eu estou com vários containers em execução.

#### parando todos os containers de uma vez (docker stop $(docker container ls -q))

Se eu quiser parar todos de uma vez, como eu faço? Eu posso colocar o comando docker stop, e eu posso simplesmente falar que eu quero parar todos os meus containers passando os IDs deles. Para fazer isso basta eu executar junto o comando docker container ls.

Só que não tão rápido assim. Eu preciso falar que eu quero executar o comando docker container ls e usá-lo como entrada para o meu stop. Para isso eu uso docker stop $(docker container ls).

E no fim das contas também eu preciso falar que eu quero pegar só o ID, então eu coloco a flag –q, de quiet. Assim ele pega só o ID, docker stop $(docker container ls -q).

```
docker stop $(docker container ls -q)
```

Vou dar um “Enter”. Ele vai ter aqueles 10 segundos para parar os containers de maneira segura, por assim dizer. E ele vai parar todos os containers a partir desse momento.

Mas enquanto ele vai parando, o que podemos observar no nosso “Dockerfile”? Nós construímos a nossa imagem, e no momento em que executamos, o que acontece?

```
FROM node:14
WORKDIR /app-node
COPY . .
RUN npm install
ENTRYPOINT npm start
```

Vamos mais uma vez no terminal fazer um docker run -p 8080:3000 -d danielartine/app-node:1.0. Ele rodou. E agora vamos dar um docker ps mais uma vez.

```
docker run -p 8080:3000 -d danielartine/app-node:1.0
```

Repara que ele está mostrando para nós o mapeamento de portas que estamos fazendo graças à flag -p. Mas e se eu executar esse mesmo container sem fazer o -p e colocar só o -d? Vamos ver o que vai acontecer. Vamos dar um docker ps de novo.

Repare que ele não está exibindo nada na coluna de portas. E sabemos que a nossa aplicação roda na porta 3000.

Então como é que poderíamos documentar isso para que outras pessoas que fossem utilizar o nosso container posteriormente, baseado na nossa imagem, soubessem que a aplicação está exposta na porta 3000?

#### EXPOSE

Existe na verdade uma maneira que podemos fazer essa documentação para que fique explícito em qual porta a aplicação está sendo executada. Basta colocarmos a instrução EXPOSE.

```
FROM node:14
WORKDIR /app-node
EXPOSE 3000
COPY . .
RUN npm install
ENTRYPOINT npm start
```

No nosso caso, colocamos EXPOSE 3000. Nesse momento estamos falando que a nossa aplicação estará exposta na porta 3000. 

Isso não é obrigatório, até porque já fizemos anteriormente e funcionou esse mapeamento.

Mas agora vamos fazer o seguinte teste: vou salvar o arquivo e gerar uma nova imagem com o comando docker build –t danielartine/app-node:1.1 .. Coloquei a versão 1.1 e o ponto no final, que é o nosso diretório atual.

```
docker build –t danielartine/app-node:1.1 .
```

Ele está fazendo todo o processo de build. E no momento em que eu executar agora mais uma vez o container sem fazer nenhum tipo de definição de porta, vamos ver o que vai acontecer. 

Se eu der um docker ps, repare que ele fala que a porta 3000 está exposta.

Então qualquer pessoa que olhar agora vai saber que a porta 3000 tem alguma aplicação dentro daquele container executando na porta 3000 do container. 

Então não precisamos adivinhar, e fica muito mais fácil de fazer um possível mapeamento de portas a partir daí.

Mas mais uma vez, vamos aperfeiçoar ainda mais a nossa imagem. 

Vamos entender o que está acontecendo.

Nós deixamos exposta a porta 3000. 

Mas o que poderíamos fazer se a nossa aplicação estivesse em um cenário diferente?

Porque vimos que no “index.js” estamos definindo que a porta 3000 é efetivamente a porta da nossa aplicação.

E se quiséssemos fazer isso no momento da criação da nossa imagem, de maneira mais parametrizada, através de uma variável de ambiente?

#### leitura dinamica da porta no node
Nós podemos simplesmente usar só uma sintaxe muito específica do Node, que seria colocar um process.env.PORT. 

```
process.env.PORT
```

PORT é o nome da variável que estamos definindo. 

Com isso basicamente estamos atribuindo que queremos ler uma variável de ambiente chamada PORT.

A partir desse momento podemos definir na nossa imagem o seguinte: nós vamos querer agora receber esse parâmetro definido na nossa imagem.

#### ARG
Então nós podemos simplesmente definir que nós vamos ter um argumento, que será usado para definir essa variável de ambiente dentro do nosso container posteriormente, e que será a porta que queremos utilizar. 

Para isso podemos fazer, por exemplo, ARG PORT=6000.

```
FROM node:14
WORKDIR /app-node
ARG PORT=6000
EXPOSE 3000
COPY . .
RUN npm install
ENTRYPOINT npm start
```

E depois vamos colocar o EXPOSE com essa porta. 

E como eu pego o valor desse argumento, dessa variável que estamos utilizando dentro da criação da nossa imagem? 

É só colocar EXPOSE $PORT.

```
FROM node:14
WORKDIR /app-node
ARG PORT=6000
EXPOSE $PORT
COPY . .
RUN npm install
ENTRYPOINT npm start
```

Mas tem um pequeno detalhe: 
```diff
- esse ARG só funciona em tempo de criação, de build da nossa imagem.
```

E se eu quiser passar isso efetivamente para dentro do meu container que será gerado? 

Ou seja, se eu quero que em algum momento essa variável possa ser lida dentro do meu container, eu preciso explicitar também um outro tipo de variável de ambiente para dentro do container, que é uma ENV.

#### ENV
```diff
+ O ARG só é usado em tempo de build da imagem, e o ENV será usado dentro do container posteriormente. 
```

Então podemos colocar também ENV PORT=$PORT.

```
FROM node:14
WORKDIR /app-node
ARG PORT=6000
ENV PORT=$PORT
EXPOSE $PORT
COPY . .
RUN npm install
ENTRYPOINT npm start
```

Essa variável ambiente PORT que nós estamos definindo para dentro do container terá um valor previamente definido por essa variável $PORT.

Nós podemos até ser um pouco mais semânticos e trocar para ARG PORT_BUILD=6000 e ENV PORT=$PORT_BUILD, e também EXPOSE $PORT_BUILD.

```
FROM node:14
WORKDIR /app-node
ARG PORT_BUILD=6000
ENV PORT=$PORT_BUID
EXPOSE $PORT_BUILD
COPY . .
RUN npm install
ENTRYPOINT npm start
```

Agora vamos buildar essa imagem mais uma vez, agora na versão 1.2: 

docker build –t danielartine/app-node:1.2 .

Ele vai buildar todo o nosso processo. 

E agora eu vou fazer o docker run da nossa versão 1.2 sem definir nenhuma porta para ver se vai acontecer o que nós esperamos. 

E depois fazer um docker ps. 

Ele está mostrando a porta 6000.

E agora nós já sabemos, olhando para nosso docker ps, que a porta que precisamos fazer algum mapeamento caso queiramos acessar esse container é a porta 6000.

Então vamos fazer novamente nosso docker run -p da porta 9090 da nossa máquina, na porta 6000 do nosso container e em modo detached: 

docker run –p 9090:6000 –d danielartine/app-node:1.2

Vamos executar isso e voltar para nosso navegador, acessando o “localhost:9090”. Conseguimos acessar nossa aplicação mesmo assim.

Nós conseguimos definir variáveis de ambiente para dentro do nosso container. 

Ou seja, conseguimos fazer a leitura dessas variáveis e colocá-las dentro do container.

Temos também as variáveis que são explícitas, específicas para a parte de construção da nossa imagem, que é o caso do ARG. 

O ARG é usado para construção da imagem e o ENV é usado posteriormente dentro do container.

E conseguimos também utilizar a instrução de EXPOSE, para ser mais semântico e deixar claro para as pessoas que vão utilizar nosso container posteriormente que a aplicação que estará ali dentro está exposta em determinada porta.

Então conseguimos deixar a nossa imagem ainda mais fácil de ser manuseada posteriormente, além de tornar mais parametrizável através das variáveis de ambiente.

Deixamos nossa imagem agora um pouco mais robusta. 

E vamos entender a partir de agora como fazer o deploy dessa imagem, como colocá-la no Docker Hub.

### subindo a imagem para o Docker Hub
Agora vamos fazer o push da nossa imagem para o Docker Hub. 

O primeiro passo é que você crie sua conta na parte direita da própria home do Docker Hub. 

Você define seu username, seu e-mail e sua senha, aceita os termos e marca o recaptcha. 

Depois é só clicar em “Sign Up” e confirmar sua conta por e-mail.

Depois que você dizer isso, no canto superior direito tem a parte de “Sign In”. 

Você vai colocar o seu usuário, que no meu caso é “aluradocker”, mas você terá o seu. E também a senha que você usou no momento do cadastro.

Quando você fizer o login ele vai carregar e na parte superior, na barra de navegação, tem a parte de repositórios. 

Se clicarmos, veremos que nós ainda não temos nada, porque nós simplesmente já criamos nossa imagem, mas ainda não a subimos para o Docker Hub.

E como vamos fazer isso? 

Vamos voltar ao nosso terminal, e o primeiro passo que precisamos fazer é autenticar a nossa conta também no Command Line Interface do Docker, para que ele saiba que somos realmente quem nós somos.

#### Login no docker Hub no command line
Então faço o comando docker login -u aluradocker. 

```
docker login -u aluradocker
```

O aluradocker é o meu nome de usuário. 

Quando você der um “Enter” ele vai pedir a senha que você usou no momento do seu cadastro no Docker Hub. 

Ele não vai mostrar o número de caracteres por questão de segurança, mas ele vai digitar.

```diff
+ Você vai dar um “Enter” e ele vai dar um warning de que sua senha foi armazenada de maneira não criptografada nesse caminho.
```

Agora vou dar um “Ctrl + L” e um docker images. 

Agora nós queremos simplesmente colocar a imagem “danielartine/app-node” na versão 1.0 no Docker Hub.

Nós precisamos usar um comando específico para isso. 

#### docker push
Ao invés de ser o docker pull, vai ser o docker push, seguido do nome da imagem e sua versão. 

```
docker push danielartine/app-node:1.0
```

Com isso ele vai saber que ele deve fazer esse push automaticamente para o Docker Hub.

No momento em que dermos o “Enter”, ele vai preparar todas as camadas, mas repare que ele deu um acesso negado. 

##### access denied no docker push
Isso quer dizer que nós não temos permissão para colocar essa imagem “danielartine/app-node” na versão 1.0. 

Mas por quê?

Vamos lembrar daquela questão, por exemplo, do “dockersamples/static-site”. 

Nós temos o nome do usuário ou da organização, barra e o nome da imagem.

E qual é o meu nome de usuário efetivamente nessa conta? 

É “aluradocker”. 

Então não faz sentido eu ter permissão de fazer um push em nome de “danielartine/app-node”. 

Assim como não teria se eu tivesse uma imagem “dockersamples/alguma coisa”.

Então eu vou voltar ao meu terminal e fazer docker images mais uma vez só para visualizar. 

Eu quero gerar uma cópia dessa imagem, mas com uma nova tag, um novo repositório na coluna que queremos gerar.

#### docker tag
Para fazer isso basta executarmos o comando docker tag. 

Então docker tag danielartine/app-node:1.0. E em seguida eu coloco a imagem que eu quero gerar, então fica: 

```
docker tag danielartine/app-node:1.0 aluradocker/app-node:1.0
```

No momento em que eu apertar o “Enter”, ele vai fazer sem nenhum output. 

E se eu der um docker images de novo, agora temos o “aluradocker/app-node” na versão 1.0. 

Inclusive com o mesmo ID, mas agora com um repositório diferente.

Agora eu vou tentar efetuar mais uma vez o meu push, mas agora com a versão correta, que é o repositório de aluradocker: 

```
docker push aluradocker/app-node:1.0
```

Ele vai começar a fazer o push, só que agora efetivamente, sem dar nenhum problema de acesso, porque ele consegue, a partir desse momento, fazer o meu push de maneira bem mais segura, porque ele sabe que eu sou realmente a pessoa que deveria ter essa permissão, baseado no nome de repositório que definimos para a nossa imagem.

Repara que agora o push terminou. 

Isso quer dizer que se agora formos ao Docker Hub na nossa parte de repositórios, vai aparecer a nossa imagem que acabamos de subir. 

Se clicarmos sobre nossa imagem, vemos a versão 1.0.

Só que agora veremos uma coisa mais legal ainda. 

No terminal vamos dar um “Ctrl + L” e fazer o docker images mais uma vez. 

Nós temos a “danielartines/app-node” também na versão 1.2.

Então se eu fizer um docker tag dessa imagem na versão 1.2 para “aluradocker/app-node”, também na versão 1.2, eu vou fazer um push dessa nova versão.

```
docker tag danielartine/app-node:1.2 aluradocker/app-node:1.2
docker push aluradocker/app-node:1.2
```

Só que qual vai ser a grande sacada? 

Ele vai fazer o processo de novo, só que agora ele sabe que diversas dessas camadas já existem no Docker Hub, e ele só faz o push das camadas necessárias.

Então até o Docker Hub também consegue ser inteligente nessa parte. 

Ela já sabe que uma camada já está lá e ele consegue aproveitar para gerar uma imagem nova com as camadas já existentes. 

E isso é muito legal.

Se voltarmos ao Docker Hub e atualizarmos a página da nossa imagem, além da nossa tag 1.0, nós também teremos a nossa 1.2, ambas com OS do Linux, e mostrando que o pull de uma foi feito há um minuto e ou outro há poucos segundos.

Agora nós aprendemos a fazer o push das nossas imagens, gerando tags novas também localmente. E vimos que o Docker Hub é capaz de reutilizar as camadas já existentes também no nosso repositório remoto. E isso é muito legal.

### o que aprendemos?
* imagens sao imutaveis, ou seja, depois de baixadas, multiplos containers conseguirao reutilizar a mesma imagem
* imagens sao compostas por uma ou mais camadas. dessa forma, diferentes imagens sao capazes de reutilizar uma ou mais camadas em comum entre si
* podemos criar nossas imagens atraves do Dockerfiles e do comando docker build
* para subir uma imagem no Docker Hub, utilizamos o comando docker push

### O problema de persistir dados
Antes de seguirmos no nosso fluxo original, vamos revisar os comandos importantes que já vimos.

#### comandos do docker ate agora
docker ps -> lista os containers ativos
docker ps -a -> lista os containers instaladas
docker container rm $(docker container ls -aq) -> remove os containers (todos)
docker images -> lista as imagens
docker rmi $(docker image ls -aq) -> remove as imagens (todas)
docker rmi $(docker image ls -aq) --force -> força a remoçao da imagem (todas)


Vamos dar um docker ps. 
Não temos nenhum container em execução. Se fizermos um docker ps –a, temos vários containers parados.

Vamos dar aquela revisada e remover todos os containers. Então vou fazer docker container rm. E vou usar como entrada a saída do meu comando docker container rm $(docker container ls –aq).

Na flag -aq, o q é para pegarmos somente os IDs e o -a é porque eu quero pegar todos os meus containers, inclusive os que estão parados. Se eu der um “Enter”, ele vai remover. Vamos fazer um docker images. Nós temos algumas imagens.

Agora o que vamos fazer é um docker rmi $(docker image ls –aq). Com isso ele está removendo todas as imagens. Repara que ele deu um conflito.

Ele está falando que algumas imagens estão sendo referenciadas por múltiplos repositórios, então precisamos forçar essa remoção. Vamos fazer o mesmo comando, mas passando o docker rmi $(docker image ls –aq) --force no final. Assim ele consegue agora remover essas imagens também.

Então caso você não consiga, a flag --force vai dar essa força para nós. Agora se nós fizermos um docker images, não temos mais nada, foi tudo removido.

Qual é a grande mágica que veremos durante essa etapa do curso? 

Vamos voltar às origens e dar um docker run –it ubuntu, porque eu quero iniciar um container do Ubuntu em modo interativo. 

Nós vamos entender o porquê.

Vou dar um “Enter” para ele baixar a imagem rapidamente. 

Nesse caso, como a imagem é bem curta, vai ser rápido, não precisamos fazer nenhum corte nesse caso.

Estou com meu container já praticamente em execução, ele está terminando de extrair. 

E eu vou abrir já outro terminal e fazer docker ps. 

E com isso ele mostra as informações desse container.

Até então nada de novo. 

#### tamanho do container (docker ps -s)
Mas existe uma flag bem interessante de vermos, que é a -s. 

```
docker ps –s
docker container ls –s
```

Então se fizermos docker ps –s ou docker container ls –s, repara que surgiu uma coluna extra. 

Nessa coluna ele fala que o tamanho desse container é 0B, mas o tamanho virtual dele é 72.8 MB. 

O que isso quer dizer?

Vamos voltar à nossa apresentação original falando sobre imagens, como elas funcionam e afins. 

Lembra que uma imagem, no fim das contas, é um conjunto de camadas que estão empilhadas uma em cima da outra? 

Nós conseguimos, aliás, ver essa informação com docker history.

Se eu fizer um docker history na imagem do Ubuntu, por exemplo, vejo que ela é composta por duas camadas, uma de 0B e outra de 72.8MB.

Então repara que o tamanho virtual do meu container é meio que o tamanho total da minha imagem. 

Isso faz total sentido, porque no fim das contas o container nada mais é do que a imagem com uma camada extra de read-write.

No momento em que criamos esse container, até então não tem nenhum dado dentro dele além das informações originais da imagem, então o tamanho dele vai ser igual ao tamanho da imagem.

Mas o tamanho mesmo dele efetivamente será 0B. 

O tamanho virtual dele vai ser só o tamanho da imagem no fim das contas.

Vamos voltar ao nosso outro terminal e fazer algumas outras operações. 

Por exemplo, vamos fazer apt-get update. 

Vamos fazer algumas operações dentro do nosso container para ver o que vai começar a acontecer com aquele tamanho que estamos vendo.

Ele vai atualizar o repositório, nós podemos criar alguns arquivos. 

Você também pode fazer os experimentos que você quiser. 

Eu só estou dando uma atualização no repositório.

Se eu fizer agora docker ps –s, repara que o size dele já está em 16.2MB. 

Agora o tamanho virtual do meu container é o tamanho que era original da imagem que tínhamos com o docker history mais o tamanho das informações de dados que eu tenho dentro do meu container.

Então essa informação na coluna de size nada mais é do que as informações que estão agora na nossa camada de read-write. 

São informações a princípio temporárias, porque lembra que essa camada é fina e temporária, só para informações que serão escritas dentro daquele container.

E é por isso que se criarmos outros containers a partir da imagem do Ubuntu, teremos cada um com uma camada de read-write diferente e os containers terão o mesmo tamanho base no fim das contas, para que consigamos fazer essas operações. 

Mas cada um terá a sua camada de escrita separada.

Então repara que se dermos um docker ps –s mais uma vez, o tamanho já vai estar praticamente o dobro, porque o apt-get update já foi executado provavelmente.

Mas é aquela velha história: se eu voltar no meu container, sair dele e der um docker ps, ele já não está mais em execução. 

Mas agora eu vou criar um novo container com docker run –it ubuntu bash.

Se eu voltar no meu outro terminal e fizer docker ps –s, teremos um novo, que é a mesma história: repara que o tamanho dele zerou, porque as camadas de read-write são isoladas umas das outras, cada container terá a sua.

Se fizermos docker ps –sa, nós temos agora o antigo, que não está mais em execução, e o outro que ainda está em execução.

Então agora precisamos entender como podemos persistir essas informações de alguma maneira para que containers que já foram removidos e talvez subam de novo com alguma informação mantenham esses dados.

Porque vimos que essa camada não é persistente entre containers, ela também não é persistente caso eu remova esse container e suba um novo, eu não vou ter essa informação mais. 

Então como podemos persistir essas informações?

A primeira informação que podemos seguir de uma maneira, além da documentação, é utilizar os volumes. 

Existem três tipos principais.

Um deles é a parte do bind mount, que é uma maneira com que podemos fazer um bind entre o file system do nosso sistema operacional e o sistema de arquivos do nosso container. 

Então teremos uma ponte entre essas duas partes, que vai persistir essa informação no nosso host.

Temos o volume efetivamente, que será gerenciado pelo Docker. 

Mas vamos entender tudo isso em mais detalhes. 

E tem o tmpfs, que é temporário. 

Mas vamos entender a utilidade dele também.

Agora que já entendemos o problema que precisamos enfrentar e como resolver ele, com essas três possíveis soluções.

### Utilizando bind mounts
Agora nós vamos entender como utilizar a primeira solução que o Docker disponibiliza para persistência de dados, que se dermos uma olhada na documentação, são os bind mounts.

Ele vai fazer basicamente o bind, uma ligação entre um ponto de montagem do nosso sistema operacional e algum diretório dentro do container. 

Vamos entender agora como isso vai funcionar.

Eu vou parar mais uma vez os containers com o comando de docker rm $(docker container os -aq) --force. 

E agora vamos executar mais uma vez aquele docker run –it ubuntu. 

E a questão agora é a seguinte: o que podemos definir no momento em que vamos executar um container?

```diff
+ Eu posso informar que quando esse container for criado e executado eu quero que as informações persistidas em determinado diretório dentro dele sejam persistidas em algum diretório na minha máquina local mesmo, no meu host.
```

### flag -v
Então além de definir os comandos básicos que já definimos e a flag de -it também, eu posso colocar uma flag de -v.

Vamos ver o que essa flag faz. 

Eu vou criar uma nova pasta para esse caso específico. 

Já estou na minha home, vou criar um pasta com o comando mkdir, chamada volume-docker. 

Pode ser o nome que você quiser criar para essa pasta.

E agora, voltando à nossa execução, eu quero que esse diretório que esteja na minha pasta “/home/daniel/volume-docker” corresponda a um determinado caminho dentro do meu container.

Então eu coloco dois pontos e informo que, por exemplo, dentro do container eu terei um diretório chamado “/app”, e tudo que for gravado dentro desse diretório será persistido nesse diretório no meu host, docker run –it –v /home/daniel/volume-docker:/app Ubuntu bash.

Vamos ver como isso vai funcionar. Eu vou colocar ainda um bash no final e dar um “Enter”.  

Se eu der um ls agora, repara que ele tem esse diretório “/app”. 

Agora vou acessar com cd app/. 

E vou criar um arquivo com touch arquivo-qualquer.txt. 

Vamos ver o que vai acontecer.

Se eu simplesmente abrir o gerenciador de arquivos e entrar na pasta “volume-docker”, está ali o meu “arquivo-qualquer.txt”.

Então eu consegui fazer essa definição de colocar um caminho dentro do meu diretório da minha máquina local para um diretório dentro do container e salvar esse arquivo.

Mas como isso vai funcionar agora? 

Eu quero parar esse container. 

Vou dar um “Ctrl + D” e vou criar um novo container, definindo o mesmo bind mount, o mesmo caminho na minha máquina para esse diretório “/app”.

E vai ser um novo container, porque estamos dando um novo run, então sabemos que será uma nova camada de read-write para esse container: docker run –it –v /home/daniel/volume-docker:/app ubuntu bash. 

E vou dar um “Enter”.

Se agora eu der um ls, repare que eu vou continuar tendo minha pasta “/app”. 

E se eu der um cd app e um ls em seguida, o meu “arquivo-qualquer.txt” ainda aparece.

Então conseguimos agora persistir informações entre containers. 

Caso um container pare de funcionar de alguma maneira e queiramos persistir os dados que estejam lá, já conseguimos fazer isso agora, e de maneira a princípio prática.

Nós só definimos um diretório dentro do nosso host para um diretório do nosso container e está tudo feito, 100% funcionando. 

É muito fácil e prático nesse sentido.

Mas qual é a outra maneira que podemos fazer e que inclusive vem sendo mais recomendada pelo Docker para criação de volume?

Mais uma vez, em conjunto com a documentação, repara que a maneira que estamos utilizando é com a flag -v.

#### flag --mount
Mas vem sendo recomendado, por ser mais semântico, a utilização além da flag –v, a flag --mount. 

Vamos pegar um exemplo para ver o que ela faz.

No terminal vou dar um “Ctrl + D” de novo e vou criar um terceiro container agora, só para vocês verem que eu não estou roubando, não estou com nenhum container execução.

Vamos criar o terceiro container agora utilizando essa flag --mount. 

Como é que ela funciona? 

```
docker run -it --mount type=bind,source=/home/daniel/volume-docker,target=/app ubuntu bash
```

No nosso caso vamos fazer um mount do tipo bind, porque é um bind mount. 

E a nossa source, o diretório da nossa máquina, vai ser mais uma vez, no meu caso, docker run –it --mount type=bind,source=/home/daniel/volume-docker,target=/app ubuntu bash. 

E o meu target será um “/app” também dentro desse container.

Vou dar um “Enter”. 

#### o que acontece se passarmos um diretorio que nao existe?
Isso é até uma coisa interessante: se o diretório que você estiver tentando utilizar não existir no seu host, a flag --mount vai falar que esse caminho não existe. 

Se eu der um ls tenho o meu app. 

E seu eu der cd app/ e em seguida um ls, aparece o meu arquivo.

Então tudo continua funcionando, utilizando tanto da maneira com a flag --mount quanto com a flag –v. 

Conseguimos agora persistir os dados entre os containers e também entre os próprios containers nas execuções seguintes para que os dados sejam persistidos caso seja necessário.

Se tiver algum arquivo de configuração, que a sua aplicação precisa executar e coisas afins, nós conseguimos ter isso agora.

Nós veremos alguns exemplos mais robustos posteriormente, mas até então vamos entender os conceitos primordiais que baseiam toda a utilização de volumes, bind mount e tmpfs.

Mas vamos ficar com a pulga atrás da orelha no final dessa aula com o seguinte questionamento: será que é interessante o nosso container depender de um caminho dentro do nosso host?

Pode ser que nós escrevamos como aconteceu agora, de um caminho não existir e dê algum problema; ou não tenhamos permissão para acessar; ou alguém simplesmente delete esse caminho localmente, porque ele estará no host.

Então são N cenários que podem acontecer e que devemos nos preocupar. 

E para isso vamos conseguir utilizar uma solução ainda mais robusta e que é mais recomendada pelo Docker, que são os volumes em si, que serão gerenciados pelo próprio Daemon do Docker. 

### Utilizando Volumes
A segunda solução, que inclusive é a mais recomendada para se usar em ambientes produtivos e afins pelo Docker, é a utilização de volumes.

Se olharmos a imagem que está presente na documentação, ele mostra que a utilização de volumes é uma área gerenciada pelo Docker dentro do seu file system.

Então por mais que no fim das contas as nossas informações continuem dentro do nosso host original para ser persistidas, nós teremos uma área que o Docker vai gerenciar e é muito mais segura a nível de alguém mexer e fazer alguma loucura ali dentro, porque será gerenciada pelo próprio Docker.

E como criamos um volume inicialmente? Vamos voltar no nosso terminal. Não estou com nenhum container em execução.

#### docker volume
Existe, dentre os diversos comandos que temos no Docker, um comando chamado docker volume. 

```
docker volume
```

#### docker volume ls
Podemos até dar um docker volume ls, para vermos que não temos nenhum criado. 

```
docker volume ls
```

#### docker volume create
Nós podemos simplesmente criar. 

Eu posso dar um docker volume create e colocar em seguida nosso meu-volume.

```
docker volume create meu-volume
```

Quando eu der esse comando, ele vai criar, e se dermos um docker volume ls, ele mostra nosso volume, usando nosso driver local do nosso host, com o nome que demos.

Mas onde é que ele está na nossa máquina? Como é que eu sei que ele vai gravar essas informações? Nós vamos chegar lá.

Mas antes vamos fazer o mesmo experimento que fizemos anteriormente. 

Eu vou executar o docker run com a flag -v, como fizemos da maneira original. 

Só que ao invés de colocarmos o mesmo caminho de antes, eu não quero mais definir nenhum diretório dentro da minha máquina manualmente.

#### docker run com volume
Então eu vou simplesmente explicitar que eu quero utilizar o “meu-volume”, que é o nome do meu volume, e ele será mapeado nesse meu diretório “/app” dentro do meu container, docker run –it –v meu-volume:/app ubuntu bash. 

```
docker run -it -v meu-volume:/app ubuntu bash
```

Vou dar um “Enter” e um ls. 

Vou entrar no meu /app mais uma vez.

Ele está vazio, porque repara que por mais que o /app seja igual, agora nós estamos utilizando um ponto dentro do nosso host diferente. 

Antes estávamos utilizando o diretório na nossa home, mas agora estamos usando um novo volume, que é esse “meu-volume” gerenciado pelo Docker.

Mas se seguirmos a mesma receita, vamos criar um arquivo qualquer com touch arquivo-qualquer.txt. 

Vamos criar um novo container, seguindo o mesmo comando: docker run –it –v meu-volume:/app ubuntu bash. 

Se eu fizer agora um cd app/ e um ls, temos agora o nosso arquivo qualquer.

Então tudo continua funcionando. 

Mas a pergunta que precisamos responder agora é: onde está esse arquivo? 

Porque antes nós sabíamos que se viéssemos na nossa home e acessássemos a pasta “volume-docker” ele estaria dentro dela.

Mas esse meu volume está aonde? 

Eu vou entrar com o super usuário, fazendo _sudo su_ e colocar minha senha. 

Agora existe um diretório onde o Docker está realmente na nossa máquina. 

Não o Docker em si, mas diversas informações que ele armazena na nossa máquina. 

#### local onde o docker armazena os volumes no linux
Ele grava em “/var/lib/docker/”.

Se dermos um ls, nós temos diversas informações, como plug-ins, buildkit, imagens, overlay, e inclusive volumes. Se fizermos cd volumes/, nós encontramos o “meu-volume”.

Então se eu fizer um cd meu-volume/, ele vai ter um hash doido, dentro dessa pasta de data, e dentro dele estará o nosso arquivo qualquer.

Então nós sabemos agora onde os nossos arquivos estão, porque agora por mais que eles estejam ainda dentro desse caminho, eles estão num lugar completamente gerenciado pelo Docker. Então conseguimos utilizar os comandos do Docker para gerenciar esse volume.

#### docker volume commands

```
docker volume 
```

Se sairmos do modo de super usuário e fizermos docker volume simplesmente sem passar nada, ele vai mostrar que conseguimos criar volumes, inspecioná-los, listá-los, remover os que não estão sendo usados, remover independentemente se estiver sendo usado ou não.

```
docker volume create
docker volume inspect
docker volume ls
docker volume prune
docker volume rm
```

Então conseguimos gerenciar esses volumes agora através da interface do Docker. 

Não ficamos 100% dependentes do nosso sistema de arquivos do nosso sistema operacional. 

E sim o Docker vai gerenciar isso para nós agora.

Isso é muito interessante, porque agora não dependemos diretamente de uma estrutura de pastas específicas do nosso sistema operacional. Ele estará sempre nesse diretório de volumes.

#### criar um volume com a flag --mount
E por fim, para finalizar essa parte da criação de volumes, mais uma vez vamos olhar na documentação. Também temos a possibilidade de criar um volume com a flag --mount.

E é até mais fácil nesse caso, porque por padrão ele já assume que o tipo que vamos criar é um volume. Então não precisamos colocar o tipo, como fizemos com o bind.

Então vamos voltar ao terminal e executar o comando de criação de um container. Vamos colocar o --mount, sem o type=bound, porque não é mais necessário. E a source será o meu-volume, e o target continua para “/app”, docker run -it --mount source=meu-volume, target=/app ubuntu bash. Se fizermos o ls dentro de /app, temos o nosso arquivo qualquer.

```
docker run -it --mount source=meu-volume,target=/app ubuntu bash
```

E ainda tem uma graça dos volumes também que é o seguinte: vamos criar mais um container, mas agora vou colocar, por exemplo um meu-novo-volume em source, que é um volume que eu não tenho criado até então. Ele vai fazer isso automaticamente para nós.

Se eu der um ls app/ vai estar vazio, porque eu estou usando um volume novo. Mas se eu sair desse container agora e der um docker volume ls, vemos que ele criou já esse volume automaticamente.

Então conseguimos ter esse controle também e o Docker vai nos ajudar nesse sentido. Então não precisamos se preocupar necessariamente em criar, porque como é gerenciado pelo Docker ele pode fazer isso por nós. E isso é muito interessante.

Agora já vimos as duas principais formas de criar maneiras de persistir dados, tanto bind mount como com os volumes em si gerenciados pelo Docker. Mas ainda falta uma terceira forma, que são os tmpfs.

### utilizando tmpfs (somente linux)
Chegamos ao terceiro tipo que podemos utilizar de persistência com o Docker, nem tanto de dados, que são os tmpfs.

Antes de seguirmos, algumas peculiaridades: 
```diff
- a primeira é que ele só vai funcionar no host Linux. 
```

Então por isso é importante estarmos utilizando o Linux, porque diversas funcionalidades, como essa, por exemplo, são feitas para rodar em ambiente Linux.

Então essa é a importância de você estar seguindo provavelmente esse curso no Linux também, porque é uma questão que já está disponível dentro desse ambiente.

Mas vamos ver como o tmpfs funciona e o que vai mudar. Nós já executamos containers diversas vezes. E agora qual será a diferença?

Temos toda a nossa parte de definir como um container vai funcionar. Mas como é que fazemos para utilizar agora um tmpfs?

#### flag --tmpfs
No fim das contas, se fôssemos olhar na documentação, mas eu não vou dar spoiler para vocês, nós vamos defini-lo de maneira bem simples, porque não vamos utilizar o -v agora. 

Ele possui uma flag própria já, que no caso é a --tmpfs.

Se fizermos docker run –it --tmpfs=/app ubuntu bash e dermos um “Enter”, seguido de um ls, veremos que ele criou a pasta “app”.

```
docker run –it --tmpfs=/app ubuntu bash
```

Mas repara uma coisa muito importante: diferente das outras vezes que criamos essa pasta através de volumes, agora ela está com um fundo verde, assim como a pasta tmp. 

O que será que isso quer dizer?

```diff
+ Isso significa que essa pasta “/app” é temporária. 
```
Então ela está sendo escrita na memória do nosso host.

Eu vou criar um arquivo dentro do meu diretório app, por exemplo: touch app/um-arquivo-qualquer. 

Vou dar um ls app/, para ver meu arquivo. 

Agora vou sair com exit e vou criar um novo. 

E se eu der um ls app/ de novo, agora não tem nada dentro dele.

Então qual é a utilidade prática desse tipo de armazenamento que não armazena? 

```diff
+ A ideia do tmpfs é basicamente persistir dados na memória do seu host, mas esses dados não estão sendo escritos na camada de read-write. 
```

Eles estão sendo escritos diretamente na memória do host.

Isso é importante, por exemplo, quando temos algum dado sensível que não queremos persistir na camada de read-write por questões de segurança talvez, mas queremos tê-los dentro de alguma maneira. Nesses casos podemos utilizar o tmpfs.

Então esses dados não serão escritos na camada de read-write, e sim ficar em memória temporariamente.

Então é uma questão de segurança que seria interessante também em alguns cenários como, por exemplo, arquivos de senha ou algum arquivo que você não quer carregar durante a execução como um todo e manter na camada de escrita.

Então o tmpfs é basicamente isso. 

Ele só funciona no host Linux. 

Mas assim como todas as outras abordagens que já vimos, tanto de bind mount quanto de volume, ao invés de fazermos a parte de tmpfs com a flag --tmpfs, podemos utilizar também a flag --mount, seguindo uma ideia bem parecida com a do bind mount, em que definimos o tipo que vamos utilizar.

#### flag --mount com tmpfs
A ideia será a mesma: basta colocar o tipo como tmpfs. E o nosso destination nesse caso é o /app dentro do nosso container: docker run –it --mount type=tmpfs,detination=/app ubuntu bash.

```
docker run –it --mount type=tmpfs,detination=/app ubuntu bash
```

Se executarmos isso e fizermos um ls, mais uma vez está o nosso “/app” temporário, porque o fundo está verde, assim como nosso tmp. 

Se criarmos um arquivo com touch app/um-arquivo-qualquer e fizermos um ls app/, temos nosso arquivo. 

Só que se criarmos um novo container de novo, não vai ter nenhuma informação dentro do nosso app.

Então essa é a ideia do tmpfs. 

Caso queiramos colocar algum dado temporário que não deve ser armazenado de maneira alguma na camada de read-write, podemos utilizar o tmpfs também.

A ideia é basicamente essa. Na questão de persistência de dados no fim das contas nós vimos três possibilidades. Temos o tmpfs, que acabamos de ver.

Temos os bind mounts, que foi o primeiro que vimos e que fazem um bind, uma ligação direta entre o sistema de pastas do nosso host e do nosso container.

E também os volumes, que são a solução recomendada, inclusive, para utilização, porque eles são gerenciados pelo Docker e conseguimos ter um controle maior sobre isso, sem depender diretamente da estrutura de pastas do nosso host efetivamente.

Essa parte de persistência de dados nós terminamos por aqui.

### o que aprendemos?
* quando containers sao removidos, nossos dados sao perdidos
* podemos persistir dados em definitivo atraves de volumes e bind mounts
* bind mounts dependem da estrutura de pastas do host
* volumes sao gerenciados pelo docker
* tmpfs armazenam dados em memoria volatil

### conhecendo a rede bridge
Voltando um pouco, lembra que nós vimos sobre a questão de como os containers são isolados em relação ao nosso host e como precisamos nos preocupar em como eles vão se comunicar?

Porque no fim das contas estávamos debatendo aquela questão de que um sistema complexo é composto por diversas aplicações atualmente.

Então pode ser que tenhamos uma aplicação Java se comunicando com uma C#, que se comunica com uma Nginx. Ou podemos pensar num caso clássico de uma aplicação back-end se comunicando com um servidor de banco de dados, por exemplo.

Então se os containers estão isolados, como podemos lidar com essa questão da comunicação entre containers?

Se voltarmos naquela questão dos namespaces, nós temos toda aquela parte que já provê isolamento para nós nas interfaces de rede. Mas como será que isso funciona dentro do Docker?

Vamos voltar ao nosso terminal. E nesse momento podemos testar o nosso experimento clássico de execução de um container Ubuntu. Então vou fazer docker run –it Ubuntu bash. E vou colocar também um bash para executarmos.

O que vai acontecer nesse momento? Já sabemos que o container vai ficar em execução. Mas eu vou abrir um novo terminal com um docker ps. Temos o nosso container que acabou de subir.

#### docker inspect
Existe um comando interessante com que podemos inspecionar as configurações, os detalhes de um container quando ele já está em execução ou até mesmo em outras ocasiões também. É o docker inspect. Colocando o ID desse container e dando um “Enter”, ele vai dar diversos detalhes.

```
docker inspect 1233sad2123
```

#### bridge
Já no final tem a parte que estamos procurando, que é a parte de networks, de redes. E dentro desse conjunto de redes ele tem uma chamada bridge que tem diversas configurações.

Mas em que momento nós configuramos essa rede? A questão é que nós não configuramos. Quem fez isso foi o próprio Docker.

Vamos fazer uma comparação. Eu vou abrir mais um terminal e executar mais um container do Ubuntu com docker run –it ubuntu bash. E vamos comparar a saída desses outputs de rede.

Então vou abrir mais terminal e fazer um docker ps e um docker inspect com o ID desse outro Ubuntu que acabamos de criar.

```diff
+ Repara que se colocarmos lado a lado, toda essa parte de rede que ele está mostrando é igual. 
+ A parte de IPAMconfig como null, o network ID é igual.
```

Então todos esses pontos dentro do nosso sistema, exceto o endpoint ID e o IP address, são iguais. 

Por quê? 

```diff
+ Isso significa então que esses containers no fim das contas estão na mesma rede.
```

Mas será que conseguimos fazer algum tipo de comunicação entre eles, já que eles estão na mesma rede, que é um driver que o Docker está colocando para nós? Antes de pensar nisso, precisamos entender o que é a bridge.

#### docker network
Então vou abrir mais um terminal para ficar tudo bem separado o que estamos fazendo. E existe, dentro de todo o arsenal de comandos que o Docker provê para nós, uma parte sobre redes. Temos o docker run, o docker images, o docker build, e temos o docker network.

E como eu faço para listar as redes que o Docker já tem no sistema, criado de maneira automática? 

Basta eu fazer docker network ls. 

```
docker network ls
```

Ele mostra três redes para nós, uma se chama bridge, que tem seu ID, o driver bridge, e um escopo local. 

Tem uma que se chama host, que usa o driver host e o escopo também é local. 

E no fim das contas também temos uma última, que se chama none, que poderíamos não colocar nenhuma rede dentro do nosso container.

Mas como isso vai funcionar no fim das contas? O que isso tudo significa e por que precisamos nos preocupar com isso?

Se compararmos nossos IDs, pegando um dos meus inspects, nós temos que o ID da rede começa com 80a1db. Na sua máquina vai ser diferente. E repara que é exatamente a mesma rede que estamos vendo no nosso network ls.

Isso significa que os nossos dois containers que criamos sem definir nenhuma rede foram colocados nessa rede padrão bridge, com esse nome e utilizando esse driver também de bridge.

E o que isso significa? Significa que se, por exemplo, eu tentar acessar algum desses containers, como o container de ID 8ea67 e fizer um docker ps e um docker inspet, ele tem o IP address dele, que é 172.17.0.2.

E se fizermos um docker ps no outro, que começa com b02, e fizermos um docker inspect, nós temos o IP address de 172.17.0.3.

Então se eu tentar, por via das dúvidas, acessar o meu b02, cujo IP address termina com 03, e por algum motivo eu tentar comunicá-lo com o outro via IP, eu devo conseguir, já que eles estão na mesma interface de rede.

Só que como é que isso vai funcionar? Como estamos usando uma imagem do Ubuntu, bem provavelmente, caso tentemos executar algum ping, ele não vai conseguir.

Então é a velha etapa: nós precisaríamos usar uma imagem base que já contém o ping ou podemos simplesmente fazer apt-get update, e depois instalamos o ping para fazer esse experimento.

Existem imagens que já vêm com ping, mas como estamos padronizando os nossos primeiros testes com a imagem do Ubuntu, para mantermos o padrão vale nós continuarmos com toda essa parte de utilizar o Ubuntu.

Existem outras imagens voltadas para essa parte de teste de rede e afins que também você pode consultar no Docker Hub, mas como eu falei, só na questão de ping mesmo nós vamos fazer esses testes.

Então ele vai atualizar os pacotes rapidamente e quando ele terminar vamos instalar o pacote do ping para que consigamos fazer essa comunicação. 

Nós fizemos o apt-get update, e logo depois para agilizar eu já executei o comando de instalação também, que foi apt-get install iputils-ping.

Se eu der um “Enter” ele já vai ter instalado. Mas a ideia é só para fazermos a instalação do ping. Então se eu tentar agora dar um ping no 172.17.0.2, que no caso é o de início 8ea, ele vai fazer a comunicação sem nenhum problema.

Então estamos conseguindo fazer essa comunicação entre containers via IP. 

Mas quais são os problemas que isso pode levantar? 

Porque estávamos fazendo uma comunicação diretamente via IP. 

Mas já vimos que os containers estão suscetíveis a reiniciar, a serem recriados e afins. 

E isso não vai garantir que o contêiner vai ter sempre o mesmo IP. 

Então teremos uma conexão muito instável nesse sentido.

Precisamos ter uma maneira mais certa de fazer isso, como, por exemplo, via um DNS, talvez um hostname seria interessante.

Mas como vamos entender isso? 

Nós já entendemos primeiro o que são as redes, vimos que podemos containers que estão na mesma rede.

Mas vamos aprofundar isso um pouco mais vendo a questão de como podemos criar a nossa própria rede e como ela vai se comportar nesse sentido.

### criando uma rede bridge
Como podemos fazer para ter uma comunicação mais estável entre containers? 

Porque vimos que o IP é uma coisa que não podemos garantir.

Mas vamos ver um pouco a nossa coluna de informações sobre o container. 

Eu removi todos os containers com nosso comando clássico que já vimos anteriormente de docker container rm $(docker ps -aq) --force.

Agora eu vou mais uma vez fazer docker run –it ubuntu bash e vou abrir um novo terminal. 

Se eu der um docker ps, que informação eu poderia ter que seria mais estável do que um IP?

Eu poderia utilizar, por exemplo, o nome. 

Talvez você esteja se perguntando, e com total razão: esse nome não é gerado de maneira aleatória? 

Por exemplo, agora é o “angry_keller”. 

Como teremos isso de maneira estável, como conseguiremos identificar isso?

Nós podemos simplesmente definir os nossos próprios nomes para os containers, porque até então nós não fizemos isso. 

Quem fez essa criação de nome para nós foi o próprio Docker.

#### nomeando um container - flag --name
Então se eu voltar em algum dos outros terminais e der mais uma vez um docker rm com o nosso comando de remover todos os containers, o que eu posso fazer é, no momento da execução de um container, definir um nome para ele com a flag --name.

Por exemplo, vou dar o nome de “ubuntu1” para o container: docker run -it --name ubuntu1. E eu quero executá-lo com a imagem do Ubuntu e o comando bash, então docker run -it --name ubuntu1 ubuntu bash.

```
docker run -it --name ubuntu1 ubuntu bash
```

Mas isso será o suficiente para conseguirmos comunicar dois containers via host name? 

Não. 

Nós ainda precisamos dar um passo além.

Qual vai ser esse passo? 

Se olharmos o nosso docker network ls, nós temos as redes que já são padrão do Docker: a bridge, a host e a none. 

```diff
+ Mas para que consigamos fazer a comunicação entre containers via hostname nós precisamos criar nossa própria rede.
```

E como criamos nossa própria rede? Deve ser muito difícil. Na verdade é bem fácil. 

#### criando uma user defined network - docker network create
Basta executarmos o comando docker network create.

Queremos criar uma rede que faça o papel de bridge, que é a rede padrão, mas será uma própria nossa para fazer a ponte entre os containers utilizando esse driver de bridge. 

Então docker network create --driver bridge minha-bridge. O nome dela é o último parâmetro que é passado.

```
docker network create --driver bridge minha-bridge
```

#### criar um container com nome personalizado e rede propria
E agora nesse momento em que vamos criar o nosso container, além de definir o nome dele, vamos definir também a rede através do --network minha-bridge.

```
docker run -it --name ubuntu1 --network minha-bridge ubuntu bash
```

E repara que se voltarmos ao outro terminal, fizemos docker ps e agora inspecionarmos esse container com docker inspect, ele vai mostrar que dentro da parte de network não está mais simplesmente aquela bridge. 

Está a “minha-bridge”, que eu criei. 

Tem as outras informações dele, o IP está como 172.19.0.2 e com esse nome de “ubuntu1”.

Vamos agora tentar criar um outro container. 

Vou dar um “Ctrl + L” e vamos criar um container dentro dessa mesma rede.

Eu vou colocar um docker run -d porque não vamos nos preocupar com o terminal dele. 

E vou colocar o nome de --name pong, só por uma piada que vai ser engraçada e que você já vai entender, 

docker run -d --name pong --network minha-bridge ubuntu sleep 1d.

Coloquei o sleep de um dia só para manter o container em execução, não vamos nos preocupar com o terminal dele. 

Vou dar um “Enter”. 

Ele criou. E agora se eu vier no terminal do meu ubuntu1, vamos fazer apt-get update de início. Ele vai fazer toda a atualização de repositórios e afins do sistema do meu container.

Mas enquanto ele vai fazendo isso, se simplesmente fizermos um docker inspect no container que acabou de ser criado, que é o nosso pong, repara que assim como o nosso ubuntu1, ele está na rede “minha-bridge”, com um IP diferente. 

Mas não estamos mais nos preocupado com o IP.

Se fizermos um docker ps agora, temos o nosso ubuntu1 e o pong. 

Então conseguimos definir os nomes agora, conseguimos ter um controle sobre isso.

E no momento em que eu tentar agora comunicar esse meu ubuntu1 com o pong, a ideia vai ser que se eu fizer simplesmente o comando de comunicação diretamente ele deve funcionar.

E agora vamos fazer o nosso teste final. 

Vou dar um “Ctrl + L” mais uma vez e vou fazer um ping pong. 

```
ping pong
```

Era essa a grande piada que eu queria fazer com vocês, por isso que eu coloquei o nome de pong no container.

E repara que ele está fazendo a comunicação para o IP inclusive do container. 

Ele está mostrando que é a 172.19.0.3. 

Se fizermos um docker ps e um docker inspect pong, veremos que é o 172.19.0.3.

Conseguimos agora comunicar dois containers via host name com a user-defined bridge, a rede que nós definimos através de criação.

Mas como nós saberíamos disso? 

Isso é um tópico muito importante que está listado na documentação, em use bridge networks. 

Ele fala de todos os processos, eu não tirei isso do além.

Ele mostra que nas redes user-defined bridges, ou seja, 
```diff
+ as redes que são criadas por usuários de bridge, a diferença é que elas provém essa resolução automática de DNS entre containers, 
```
que é basicamente o que nós estamos fazendo agora.

Então conseguimos agora comunicar diferentes containers via host name. 

É um pouco mais fácil manter essa comunicação agora. 

E vamos entender outras questões sobre, por exemplo, o que é a rede host e a rede null para complementar nosso conhecimento, e depois vamos seguindo. 

### as redes none e host
Agora veremos como funcionam as duas redes restantes. 

Vamos dar um docker network ls.

Já vimos como funciona a bridge, e nós criamos nossa própria bridge também. 

Mas vamos olhar como funciona a rede host, que utiliza o driver host, e a rede none, que utiliza o driver null.

#### rede 'none'
Vamos começar pela rede none, que utiliza o driver null. 

Como é que ela funciona? 

Vamos exemplificar para ver como realmente ela impacta a vida do nosso container.

Vamos executar um docker run –d. Coloquei o -d porque não vamos nos preocupar com questão de terminal em modo interativo. Em seguida vou falar que meu container será executado na minha rede chamada none, que já existe: 

```
docker run -d --network none ubuntu sleep 1d
```

Vou executar a imagem do Ubuntu, e o comando que eu quero executar para o container se manter em execução é o sleep, como fizemos no início para manter o container em execução e não precisar travar o terminal para ele: docker run -d --network none ubuntu sleep 1d. Vou executar esse comando. Ele mostrou o ID completo do container.

E se fizermos docker inspect nesse ID para vermos quais são as características desse container, repara que no final ele está falando que está utilizando agora o none e toda a descrição da rede desse none.

Mas o que isso impactará diretamente nesse container? No fim das contas, 
```diff
- quando utilizamos o driver none, estamos simplesmente falando que esse container não terá qualquer interface de rede vinculada a ele. 
- Ele ficou completamente isolado a nível de rede.
```

Nós não conseguimos fazer nenhum tipo de operação envolvendo a rede desse container, porque o driver dele é none, ele utiliza o driver null no fim das contas.

O que precisamos fazer agora, caso queiramos fazer o contrário disso, por exemplo? 

Queremos que nosso container tenha interface de rede, e já vimos como fazer com a bridge, mas de uma maneira um pouco mais prática em alguns casos. 

Nós queremos fazer o contrário de não ter uma interface de rede, e sim ter uma interface de rede, mas vinculada ao nosso host, por exemplo.

Vamos fazer uma leitura das nossa redes com docker network ls. 

Nós temos a nossa rede que utiliza o driver host e possui também o nome host.

E como vai funcionar agora? Vamos fazer praticamente o mesmo teste. 

Se eu fizer um docker ps, eu vejo que não estou com mais nenhum container em execução além desse Ubuntu que eu acabei de criar.

#### rede 'host'
E agora eu vou fazer 

docker run -d --network host aluradocker/app-node:1.0

E antes do nome da imagem vou dizer que esse container será executado na rede host. 

Vamos ver o que vai acontecer. 

Vou copiar o ID do container e fazer docker inspect. 

```diff
+ Agora no final ele está informando que está utilizando a rede host na saída desse inspect.
```

Só que o que isso muda na prática? 

Agora eu vou simplesmente abrir uma nova aba do meu navegador e tentar acessar essa aplicação.

Mas não como eu vou acessar se eu não fiz o mapeamento de portas como já aprendemos? 

Se eu colocar “localhost” e definir qual porta eu quero acessar, vamos lembrar que a versão 1.0 da nossa aplicação app-node executava por padrão sempre na porta 3000.

E depois nós parametrizamos as versões 1.1 e 1.2. Mas a 1.0 sempre era executada na porta 3000. 

Então se no meu navegador eu tentar acessar a aplicação na porta 

“localhost:3000”, 

eu consigo.

Mas por que eu consegui? 

```diff
+ Porque nós simplesmente agora retiramos quaisquer isolamentos que tinham entre a interface de rede do container e do host. 
+ Porque utilizando o driver host nós estamos utilizando a mesma rede, a mesma interface do host que está hospedando esse container, por assim dizer.
```

Então caso tivesse alguma outra aplicação na minha porta 3000 com meu host em execução, eu não conseguiria fazer a utilização desse container dessa maneira, daria um problema de conflito de portas, porque a interface seria a mesma.

### Comunicando aplicaçao e banco
Agora vamos colocar um pouco de prática para vermos como funciona realmente a questão de comunicação de containers através da rede do Docker.

Vamos fazer um docker images. 

Até então nós temos imagens que vamos utilizar a partir de agora nesse vídeo, que eu já baixei com o comando docker pull. 

Mas vou deixar o comando para você também baixar.

Vou reexecutar, mas no caso ele não vai baixar porque eu já tenho, a imagem mongo na versão 4.4.6 e a “aluradocker/alura-books” na versão 1.0.

```diff
+ Ênfase na versão 4.4.6 do mongo, e não na versão latest. 
```

Quando você for baixar a imagem do mongo você vai utilizar o comando 

```
docker pull mongo:4.4.6 
```

e dar o “Enter”. Então ele fará o download dessa versão em específico.

É a mesma coisa para a imagem alura-books. Então vai ser 

```
docker pull aluradocker/alura-books:1.0
```

Agora o primeiro passo que precisamos dar é comunicar o container que será gerado pela imagem alura-books com um banco de dados que vai ser gerado pela imagem do mongo, com um container.

Mas como é que faremos essa comunicação? 

Através da rede do Docker. 

Então de início eu vou fazer um docker run no meu banco de dados: 

docker run mongo:4.4.6

Mas ainda faltam alguns detalhes. Por exemplo, eu não quero travar o meu terminal, então eu vou executar em modo detached com a flag -d.

Eu vou definir que a minha rede vai ser a “minha-bridge”, que foi a rede que criamos. Se fizermos um docker network ls veremos a rede que nós já criamos.

Caso você tenha apagado, por favor, crie sua própria bridge com o comando docker network create --driver bridge minha-bridge. Se eu executar esse comando vai dar erro, porque a rede já existe. Mas a ideia é executar esse comando, caso você tenha apagado.

E agora o que estamos fazendo é um docker run –d com o container nessa rede: 

docker run -d --network minha-bridge mongo:4.4.6

E o ponto agora é o seguinte: o nosso container de alura-books vai se comunicar com o banco de dados mongo. 

E como eles estarão numa rede bridge criada por nós, ou seja, criada manualmente, a comunicação poderá ser feita via host name.

Só que como será o host name que a nossa aplicação alura-books está buscando? Qual é o nome de banco que ela está buscando para se conectar?

Para isso eu também vou disponibilizar para vocês o código-fonte da aplicação que foi usada como base para gerar a imagem que será usada para gerar esse container.

```diff
- Mas o que importa é que no arquivo de configuração dessa aplicação, no host ele está procurando por um host chamado “meu-mongo”. 
```

Então no momento em que essa imagem foi construída, esse arquivo estava definido dessa maneira.

Isso significa que eu preciso que o host name, ou seja, o nome desse container, seja “meu-mongo”. 

Então o comando vai ficar 

```
docker run -d --network minha-bridge --name meu-mongo mongo:4.4.6
```

E a partir de agora, se eu der um “Enter” nesse comando, ele vai criar o container.

E agora eu preciso fazer o run do nosso alura-books. 

Então 

```
docker run -d --network minha-bridge --name alurabooks -p 3000:3000 aluradocker/alura-books:1.0
```

E agora vamos adicionar os detalhes com os quais precisamos nos preocupar, como o -d; a rede, que precisa ser a mesma rede, para que os containers consigam se comunicar: 

docker run -d --network minha-bridge aluradocker/alura-books:1.0.

O nome nesse caso é irrelevante, porque a aplicação está procurando pelo banco, e não o contrário. 

Não estou falando que sempre vai ser essa regra, mas nesse caso que estamos fazendo nós não precisamos nos preocupar com o nome desse container especificamente, então vou deixar como “alura-books”: 

`docker run -d --network minha-bridge --name alurabooks aluradocker/alura-books:1.0´.

E outro detalhe também é que precisamos fazer o mapeamento de portas, que vai ser a porta 3000 na porta 3000 também do nosso container: 

`docker run -d --network minha-bridge --name alurabooks –p 3000:3000 aluradocker/alura-books:1.0´.

Eu preciso fazer o mapeamento de portas, porque eu não estou e nem posso nesse caso utilizar a rede host. 

Eu estou utilizando a rede “minha-bridge”. 

Vou dar um “Enter” e ele vai executar.

Vou abrir uma nova aba no meu navegador, e agora vamos fazer 

“localhost:3000”, 

que vai acessar a nossa aplicação. 

E ela tem um endpoint, o “/seed”, que vai popular o banco. 

E agora se atualizarmos a página, todos os dados do banco estão sendo carregados na nossa aplicação.

Então a partir desse momento, se eu voltar ao terminal e simplesmente parar o container do meu mongo com o comando 

docker stop meu-mongo

, os dados no navegador vão sumir, porque a comunicação com o banco parou.

Se eu voltar ao terminal e executar 

docker start meu-mongo

, fizer um seed novamente e recarregar a página, tudo volta ao normal.

Então nós estabelecemos a comunicação entre dois containers que estão na mesma rede, e conseguimos ter um resultado real, bem próximo do que vemos no dia a dia de utilização de aplicações.

Nós tivemos uma aplicação back-end, que também tem um front, se comunicando com o banco e trazendo os dados para o usuário no fim das contas.

Só que precisamos levantar alguns questionamentos agora. 

Será que a melhor maneira é fazermos a inicialização de containers sempre manualmente? 

Porque eu tive que me preocupar em fazer o docker run de um, depois o docker run do outro.

Será que quando vamos fazer as coisas realmente em produção nós sempre subimos tudo na mão? 

É um ponto sobre o qual precisamos pensar. 

### o que aprendemos?
* o docker dispoe por padrao de tres redes: bridge, host e o none
* a rede bridge é usada para comunicar containers em um mesmo host
* redes bridges criadas manualmente permitem comunicaçao via hostname
* a rede host remove o isolamento de rede entre o container e o host
* a rede none remove a interface de rede do container
* podemos criar redes com o comando docker network create

### conhecendo o Docker Compose
Agora vamos voltar a um problema que já tivemos anteriormente, mas vamos ver o que aconteceu.

O que nós fizemos ainda há pouco? 

Nós executamos aquele nosso container do aluradocker da imagem alura-books na versão 1.0 em conjunto com o mongo na versão 4.4.6.

Para fazer isso nós precisamos de início executar o banco. 

O comando que executamos foi o 

docker run –d --network minha-bridge --name meu-mongo mongo:4.4.6

E para executar nosso container do alura-books foi através do comando: 

docker run –d --network minha-bridge --name alurabooks –p 3000:3000 aluradocker/alura-books:1.0

E nós precisamos fazer tudo isso manualmente. 

Nós precisamos ter definido qual era o comando que iríamos executar e definido a ordem que queríamos.

Então vamos parar para pensar: qual foi a motivação inicial que tivemos no curso?

Uma das questões é que podemos ter diversas aplicações se comunicando entre si para construir um sistema ainda mais complexo.

Mas o que está acontecendo agora é que se nós crescermos muito a nossa aplicação, o que vai acabar acontecendo, em algum momento teremos que subir diversos containers manualmente.

Sempre que quisermos parar um container teremos que fazer docker stop ou um docker rm para remover um a um. Então para cada container vai ser um comando que precisaremos executar, além de nos preocuparmos com toda a questão da pilha de execução que teremos com todos esses containers.

Existe uma solução já desenvolvida pela empresa do próprio Docker que vai nos ajudar a resolver esse tipo de situação, que no caso é o Docker Compose.

```diff
+ O Docker Compose nada mais é do que uma ferramenta de coordenação de containers. 
```
Não confunda com orquestração, são coisas diferentes.

Então o Docker Compose vai nos auxiliar a executar, a compor, como o nome diz, diversos containers em um mesmo ambiente, através de um único arquivo. 

Então vamos conseguir compor uma aplicação maior através dos nossos containers com o Docker Compose.

E faremos isso através da definição de um arquivo yml, aquela extensão yml, ou yaml, caso você já tenha ouvido falar. 

E nada mais é do que um tipo de estrutura que vamos seguir baseado em indentação do nosso arquivo.

Nós vamos seguir mais ou menos essa receita de definir uma versão, quais serão os nossos serviços, e também como fazer toda a parte de rede e comunicação dos nossos containers, só que agora através de um único arquivo. 

E conseguiremos coordenar isso diretamente dos comandos de como o Docker Compose faz isso.

#### docker compose no windows
Um pequeno adendo é que caso você esteja no Windows nesse momento, quando você instala o Docker você já tem nesse momento o Docker Compose.

Então caso você execute 

```
docker-compose 
```
no seu terminal, a princípio ele vai dar o “erro”, mostrando todos os comandos que você pode executar. 

Ele já fala que você poderá definir e executar aplicações de múltiplos containers com o Docker Compose.

#### docker compose no linux
Mas caso você esteja no Linux, como temos feito o curso desde o início, você precisará instalar o Docker Composer. 

Se viermos no terminal, não vamos conseguir executar nada com o Docker Compose. 

Ele não reconhece o comando docker-compose.

E ele vai sugerir também a instalação através do snap ou do apt, mas nós não faremos isso. 

Nós vamos seguir a documentação nesse caso.

Então eu vou abrir uma nova aba e vamos fazer o processo de instalação. 

Se eu digitar na barra de pesquisa “docker compose install Linux”, ele vai ter a documentação oficial que eu vou deixar para vocês também.

https://docs.docker.com/compose/install/

E caso você esteja no Windows e a instalação não tenha acontecido por algum motivo, é só marcar na página da documentação que você está utilizando o Windows. No nosso caso, estamos utilizando o Linux.

Para instalar é bem simples, basta copiar o comando que ele fornece e voltar ao terminal. 

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

Vou fazer “Ctrl + L” para limpar e “Shift + Insert” para colar. 

Vou colocar minha senha, você vai colocar a sua, claramente, e vou dar um “Enter”.

Ele vai fazer todo o processo de baixar. 

E logo depois precisamos tornar o nosso Docker Compose executável, dar a permissão para ele com o comando do chmod.

```
sudo chmod +x /usr/local/bin/docker-compose
```

Nesse momento vou abrir um terminal novo. 

Se executarmos um docker-compose para executar, agora temos o mesmo output que tínhamos no Windows. 

Ele vai definir múltiplos containers para executar uma aplicação com o Docker.

Agora que entendemos o que é o Docker Compose, que será essa ferramenta de composição e de coordenação de containers, precisamos transformar o que fizemos anteriormente num ambiente agora distribuído dessa maneira com o Docker Compose. 

### definindo os serviços
Agora precisamos traduzir esses dois comandos que já executamos anteriormente para subir o nosso alura-books e também o comando para subir o nosso mongo. Nós vamos traduzir, por assim dizer, para essa estrutura yml.

Seguiremos uma estrutura bem parecida com a que está na própria documentação. Vamos definir uma versão para o yml que vamos utilizar; vamos definir quais são os serviços. A diferença é que não seguiremos exatamente igual à documentação, mas grande parte será bem parecida.

O que eu vou fazer nesse momento é abrir um novo terminal e criar uma nova pasta na área de trabalho, chamada “ymls”: 

mkdir Desktop/ymls

Eu vou acessá-la com cd Desktop/ymls/ e vou abrir o VS Code dentro dela. Você pode usar o seu editor de texto favorito.

No VS Code, dentro dessa pasta eu vou criar um 

“docker-compose.yml”

Repara que o VS Code é inteligente o suficiente para saber que esse arquivo se trata de algo relacionado ao Docker, ele colocou até o símbolo da baleia ao lado do nome.

Vou criar o arquivo e a ideia é que dentro desse arquivo nós configuremos o nosso yml. 

Como vamos definir todas as configurações? 

Eu vou simplesmente, como eu falei de início, definir a versão do yml que vamos utilizar, que será a 3.9, então version: “3.9”.

```
version: "3.9"  # optional since v1.27.0
```

E agora nós vamos definir os nossos serviços, que no fim das contas serão nossos containers. Então quais serão os nossos serviços? 

Nós queremos repetir o que fizemos anteriormente, de subir o alura-books e o banco de dados.

Então eu posso simplesmente colocar services:

E nesse momento vamos definir que temos um serviço chamado mongodb, por exemplo, mas eu poderia dar o nome que eu quisesse.

E esse serviço vai ter as peculiaridades dele. 

Precisamos definir a partir de agora qual é a imagem que vamos utilizar para esse serviço, se queremos dar um nome para o container, se vamos colocá-lo em alguma rede.

Nós queremos fazer isso. 

Eu quero que a imagem que ele vai utilizar para esse serviço seja a do mongo na versão 4.4.6, que é a que fizemos na nossa execução. 

Então image: mongo:4.4.6.

Nós também queremos dar o nome do container de “meu-mongo”. 

Então vamos colocar container_name: meu-mongo. 

E queremos que ele esteja numa rede. 

Não precisamos manter a minha-bridge. 

Eu vou colocar uma rede chamada “compose-bridge”.

Para a definição da rede eu faço networks:, dou um “Enter”, um “Tab” e um traço, para colocarmos o elemento dessa lista. 

E vou colocar - compose-bridge.

Então agora eu defini minha rede, o nome do meu container e a imagem que eu quero utilizar, que foi a mesma coisa que definimos anteriormente quando fizemos manualmente no terminal.


```
version: "3.9"  # optional since v1.27.0
services:
  mongodb:
    image: mongo:4.4.6
    container_name: meu-mongo
    networks:
      - compose-bridge
```

Agora eu preciso fazer a mesma coisa, só que para o meu alura-books. 

Então vou quebrar mais uma linha, para ficar um pouco mais visível. 

E eu vou definir o nosso serviço chamado alura-books. 

E dentro dele eu vou utilizar a imagem: image: aluradocker/alura-books:1.0. 

Depois o nome do container, que eu vou colocar como “alurabooks”: container_name: alurabooks. 

Depois a rede, que precisa ser a mesma, então vou colocar compose-bridge.

E agora também eu vou precisar colocar o mapeamento de portas que fizemos anteriormente. 

Para definir as portas eu coloco ports:, e faço de maneira bem parecida com a rede: dou um “Enter”, um “Tab” e um traço e coloco a porta em que minha aplicação está executando, que é a 3000. 

E eu quero que ela rode na minha máquina também na porta 3000, então - 3000:3000.

Basicamente o que já definimos agora foi que o alurabooks está rodando dentro do container na porta 3000 e vai rodar no nosso host na porta 3000; definimos a imagem; a rede; e o nome.

```
version: "3.9"  # optional since v1.27.0
services:
  mongodb:
    image: mongo:4.4.6
    container_name: meu-mongo
    networks:
      - compose-bridge

  alurabooks:
    image: aluradocker/alura-books:1.0
    container_name: alurabooks
    networks:
      - compose-bridge
    ports:
      - 3000:3000
```

Agora falta um pequeno detalhe, que é configurar essa rede, porque ela não existe até então. 

Então o que precisamos fazer no final é, alinhado com a parte de services, colocar networks:, dar um “Enter” e definir a minha rede, que é a compose-bridge:.

Vou dar mais um “Enter” e vou informar o driver dela, que vai ser o bridge: driver: bridge.

```
version: "3.9"  # optional since v1.27.0
services:
  mongodb:
    image: mongo:4.4.6
    container_name: meu-mongo
    networks:
      - compose-bridge

  alurabooks:
    image: aluradocker/alura-books:1.0
    container_name: alurabooks
    networks:
      - compose-bridge
    ports:
      - 3000:3000

networks:
  compose-bridge:
    driver: bridge
```

#### docker compose up
E agora eu simplesmente vou abrir um terminal nesse diretório que eu já tenho, e vou executar o comando docker-compose up.

```
docker-compose up
```

Antes de executar esse comando vou fazer um ls para garantir que eu estou nesse diretório que o meu arquivo “docker-compose” foi criado com esse nome.

Então eu vou colocar docker compose up. E ele vai criar. Não coloquei em modo detached para entendermos o que ele está fazendo. Ele está criando o banco.

Ele já subiu todo o banco. E também, se formos subindo, conseguimos ver que ele mostra um output bem misturado, tanto do nosso “meu-mongo” quanto do nosso “alurabooks”. Tem toda essa questão deles sendo executados.

Agora temos garantia de que está tudo em execução. Se voltarmos ao nosso navegador e executarmos “localhost:3000”, conseguimos acessar nossa aplicação. Populei o banco com um “/seed”, e se eu atualizar a página, está tudo aparecendo.

Mas apesar de tudo ter funcionado, ainda tem mais detalhes que podemos ver sobre como isso tudo funcionou.

Só que antes de terminar essa aula, dentro do terminal que nós executamos e está travado, eu vou dar um “Ctrl + C”. Repara que ele vai parar os dois containers, tanto o “alurabooks” quanto o “meu-mongo”. E se voltarmos ao navegador e atualizarmos a página com “F5”, veremos que ele já derrubou.

### complementando o Compose
O que mais seria interessante saber sobre o Docker Compose? 

Uma coisa interessante é que no momento em que nós damos o docker-compose up, vimos que estamos subindo tudo de maneira meio indefinida.

#### depends on
Mas existe uma instrução que podemos colocar, em conjunto com a documentação que eu vou deixar o link também para vocês. 

Nós podemos definir algumas instruções também.

Além de todas que já colocamos, existe uma que se chama “depends_on”. 

```diff
+ Ela expressa dependência entre serviços. 
```

No momento em que colocamos uma dependência de um serviço para outro, ele vai iniciar o serviço nessa ordem específica.

No momento em que fizermos a execução, ele vai esperar o serviço subir. 

Mas tem um pequeno detalhe: 

```diff
- o depends_on não vai esperar necessariamente a aplicação dentro do container estar pronta para receber as requisições. 
```

O que ele vai fazer é esperar o container ficar pronto, o que não significa que a aplicação dentro do container já está pronta.

No momento em que viermos no nosso VS Code falarmos que agora nossa aplicação do alurabooks depende do nosso banco de dados, nós conseguimos fazer essa definição. 

Então faço depends_on:, dou um “Enter”, um “Tab” e um - mongodb.

```
version: "3.9"  # optional since v1.27.0
services:
  mongodb:
    image: mongo:4.4.6
    container_name: meu-mongo
    networks:
      - compose-bridge

  alurabooks:
    image: aluradocker/alura-books:1.0
    container_name: alurabooks
    networks:
      - compose-bridge
    ports:
      - 3000:3000
    depends_on:
      - mongodb

networks:
  compose-bridge:
    driver: bridge
```

Nós podemos agora colocar um docker-compose up no diretório mais uma vez. 

Antes vou salvar o arquivo no VS Code, vou dar um “Ctrl + L” no terminal e só então fazer docker-compose up.

Ele vai fazer toda a definição. 

Meu mongo já está ok. 

E agora repara que apesar de no final ele ainda ter feito uma parte dividida entre as duas aplicações, teve uma redução em relação ao que tínhamos antes, de vários logs misturados. 

Nesse caso ele teve um isolamento um pouco maior.

Como esperamos o container do mongo ficar pronto, só tivemos no final uma sobreposição de informações. Mas agora nós expressamos essa dependência entre as nossas aplicações.

E tem um outro detalhe. 

Vamos dar um “Ctrl + C” mais uma vez. 

Nós podemos executar em modo detached, como tínhamos mencionado, então docker-compose up -d. 

```
docker-compose up -d
```

Ele vai inicializar e pronto, ele não travar o nosso terminal.

#### docker-compose ps
Podemos fazer um docker-compose ps e ele vai mostrar os serviços que foram criados pelo Docker Compose de maneira mais organizada. 

#### docker-compose down
Podemos fazer também um docker-compose down para ele remover. 

Ele vai fazer toda a etapa de remoção dos contêineres e da rede que foi criada.

E só para finalizar essa definição toda de Docker Compose, o que é interessante sabermos e entendermos da documentação, para caso depois você for ler e não ficar perdido?

https://docs.docker.com/compose/compose-file/compose-file-v3/

#### seçao deploy
Existem algumas coisas que seriam interessantes se conseguíssemos fazer, como a sessão de deploy, que permite fazer toda uma configuração de, por exemplo, número de réplicas: quantos containers de determinado serviço você quer; a parte de placement, como você vai ajustar isso a nível de paralelismo e tudo mais.

```diff
- Só que essa definição toda não funciona da maneira que estamos utilizando o Docker. 
```

#### docker swarm
Ele fala que essas configurações só tomam efeito no momento em que estamos fazendo isso com um swarm, que vai funcionar só com docker stack deploy.

Isso significa que da maneira que estamos atualmente essas configurações, caso você tente testar na sua máquina e não funcionem, é porque você não está utilizando o Docker em modo swarm.


Sobre a parte da definição toda do Docker Compose e como ele funciona, é basicamente isso. 

Conseguimos subir e descer os serviços, fazer todas as nossas definições.

Inclusive, conseguimos também fazer a utilização de volumes. 

Analogamente como fizemos com as redes, nós conseguiríamos definir, por exemplo, um volume para um container e fazer essa utilização também.

Então caso você tenha alguma necessidade específica, a documentação é um lugar muito bom de se consultar. Eu falei para vocês de volumes, e podemos também fazer a utilização de volumes, com volumes -db para o que estamos definindo.

Nós até poderíamos, por exemplo, subir uma imagem do Ubuntu e fazer sem nenhum problema como já fizemos anteriormente a definição de um serviço com um volume, com uma declaração bem parecida com a maneira que fizemos a nossa rede também.

### o que aprendemos?
* o docker compose é uma ferramenta de coordenaçao de containers
* como instalar o docker compose no linux
* como iniciar containers em conjunto com o comando docker-compose up
* como criar um arquivo de composiçao e definir instruçoes de containers, redes e serviços

### docker commands
https://dockerlabs.collabnix.com/docker/cheatsheet/