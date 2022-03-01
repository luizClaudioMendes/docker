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

