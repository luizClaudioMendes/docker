# Docker: Criando e gerenciando containers
iniciado em 01/03/2022

terminado em ANDAMENTO

[certificate]() 

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