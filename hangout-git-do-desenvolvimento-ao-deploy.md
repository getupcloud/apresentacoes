# Git - Do desenvolvimento ao deploy #

Hangout realizado em 28/11/2013.

Assista ao video em nosso [canal do youtube](http://www.youtube.com/user/getupcloud)

## Introdução ##

Como nossos antepassados desenvolviam seus projetos nos tempos das cavernas:

1. Projeto.zip
2. Projeto-editado.zip
3. Projeto-revisao-1.zip
4. Projeto-alteracao-nova-versao.zip
5. Projeto-ultima-versao.zip
6. Projeto-agora-sim-corrigido.zip
7. Projeto-ultima-versao-10-11-2013.zip
8. Projeto-correcao-do-layout.zip
9. Projeto-revisao-do-chefe-que-nao-gostou-das-cores-dos-botoes.zip
10. ...e assim por diante, até alguém enfartar

Agora, imagine uma equipe com varias pessoas (>1) editando o projeto e enviando arquivos
por email, umas para as outras.

Resultado: **CAOS TOTAL!**

- Cada alteração precisa ser aplicada manualmente.
- Não existe histórico organizado das alterações.
- Sem contar que .tar.gz é muito melhor que .zip!

## Controle de Versão ##

Entra em cena o Sistema de Controle de Versão (VCS - Version Control System)

Alguns conceitos básicos:

- **Repositório**: diretório com arquivos "monitorados" - alterações são gravadas em lote, sequencialmente, criando um histórico.
- **Commit**: alteração atômica de um ou mais arquivos.
- **Merge**: ato de aplicar uma ou mais alterações ao repositório automaticamente
- **Branch**: diferentes linhas de tempo - sequencias de commits podem ocorrer em "troncos" paralelos
- **Conflito**: quando alterações ocorrem no mesmo ponto de um arquivo - necessita intervenção manual

Git
===

Desenvolvido por [Linus Torvalds](http://en.wikiquote.org/wiki/Linus_Torvalds) para atender as necessidades exigidas para o desenvolvimento do linux:

- Distribuido (Distributed VCS)
- Rápido - pensado para uma base de código gigante (kernel-3.12.1: ~613MB, 47337 arquivos)

Padrão entre projetos Open Source (GitHub, GitLab, Gitorious, BitBucket...)

Instalação
==========

Provavelmente você já possui o git instalado!
Abra um terminal e digite:

```bash
$ git --version
git version 1.8.3.4 (Apple Git-47)
```

Se o comando falhar, basta instalar usando um dos seguintes métodos:

### RedHat/CentOS/Fedora ###

```bash
$ sudo yum install git
```
    
### Debian/Ubuntu ###
  
```bash
$ sudo apt-get install git-core
```

### Mac ###

http://code.google.com/p/git-osx-installer

### Windows ###

http://msysgit.github.com

### Instruções mais detalhadas ###

http://git-scm.com/book/pt-br/Primeiros-passos-Instalando-Git

## Identifique-se! ##

```bash
$ git config --global user.name  "Seu Nome"
$ git config --global user.email "seu@email.com"
```

Estas informações são usadas para identificar o autor dos commits.
Evite comitar com uma conta *root*, *admin* ou algo do tipo.

## Iniciando um repositório ##

```
$ mkdir projeto
$ cd projeto
$ git init .
```

Diretório `.git/` contém metadados do repositório:

- configs
- commits
- branches
- tags
- remotes
- hooks

## Criando o primeiro arquivo ##

Conteúdo do arquivo **hello.php**:

```php
<?php
  echo "Bem vindo ao hangout de git";
?>
```

Agora vamos incluir este arquivo ao repositório:

```
$ git add hello.php
$ git commit                ---> abre um editor para inserir uma mensagem de commit
```

Explicando os dois comandos:

- **git add**:    este(s) arquivo(s) devem ser monitorados a partir de agora
- **git commit**: salve estas alterações, ou seja, crie um commit

Dependendo do seu sistema, o editor aberto pode não ser o mais adequado. Para definir um editor diferente do padrão, execute apenas uma vez o comando a seguir, trocando `gedit` pelo seu editor preferido.

```
$ git config --global --add core.editor gedit
```

Também podemos definir a mensagem do commit com a flag `-m`:

```bash
$ git commit -m "Commit inicial do projeto"
```

Com o comando `log` vemos a sequência de alterações (histórico) do repositório:

```
$ git log
commit 1fad7eb3eeffdd76be4d6c8b0ef6258e69425cb4         <--- hash unico do commit
Author: Mateus Caruccio <mateus.caruccio@getupcloud.com>
Date:   Wed Nov 27 10:11:07 2013 -0200

    Commit inicial do projeto
```

## Alterações incrementais ##

Conteúdo do arquivo **hello.php**:

```php
<?php
   echo "Bem vindo ao hangout de Git";
  echo "Hoje demonstraremos como usar o Git para desenvolver seus projetos";
?>
```

Após alterar um arquivo, este torna-se diferente do que aquele existente no repositório.
Portanto, existe uma diferença entre o repositório e o arquivo editado.
Para enxergar esta diferença usamos o comando `diff`:

```diff
$ git diff                                              <--- diff mostra alterações não comitadas
diff --git a/hello.php b/hello.php
hello 0766518..b954547 100644
--- a/hello.php
+++ b/hello.php
@@ -1,3 +1,4 @@
 <?php
-  echo "Bem vindo ao hangout de git";
+  echo "Bem vindo ao hangout de Git";
+  echo "Hoje demonstraremos como usar o Git para desenvolver seus projetos";
 ?>
```

Anatomia de um commit:

- **---**: estado antigo (o que saiu do arquivo)
- **+++**: estado novo (o que entrou no arquivo)
- **@@* localiza a alteração no arquivo (contexto)
- Primeira coluna nao faz parte das alterações
- Linha inseridas são precedidas por um "+"
- Linha removidas são precedidas por um "-"

Depois de revisar visualmente as alterações podemos comitar:

```
$ git commit -a -m Descrição melhorada"
[master 01318aa] Descrição melhorada
 1 file changed, 2 insertions(+), 1 deletion(-)
```
A flag `-a` commita todas as alterações da árvore, sem que seja necessário adicioná-las ao index com o comando `add`.

Agora, inspecionando o log, vemos um novo commit:

```
$ git log
commit 01318aac39fc5e660b28b42eee6dd5a3c382495e         <--- commit 2
Author: Mateus Caruccio <mateus.caruccio@getupcloud.com>
Date:   Wed Nov 27 10:13:58 2013 -0200

    Descrição melhorada

commit 1fad7eb3eeffdd76be4d6c8b0ef6258e69425cb4         <--- commit 1
Author: Mateus Caruccio <mateus.caruccio@getupcloud.com>
Date:   Wed Nov 27 10:11:07 2013 -0200

    Commit inicial do projeto
```

## Inspecionando commits ##

O comando `show` mostra commits arbitrários do repositório.

```
$ git show [COMMIT]
````

Conteúdo do arquivo **hello.php**:

```php
<html>
<head>
  <title>Hangout Git</title>
</head>

<body>
<?php
  echo "Bem vindo ao hangout de Git";
  echo "Hoje demonstraremos como usar o Git para desenvolver seus projetos";
?>
</body>
</html>
```

```
$ git commit -a -m 'Tags HTML'
$ git log
...

$ git show 1fad7e
$ git show 01318a
```

Commits podem ser abreviados usando apenas seus identificadores iniciais. Ao invés de `01318aac39fc5e660b28b42eee6dd5a3c382495e`, use `01318a`.

## Árvore e índice ##

Até agora vimos que um arquivo pode apresentar dois estados: comitado e alterado.
No entanto, existe um estado intermediário, chamado `índice`, onde alterações individuais, de arquivos diferentes,
podem ser agrupadas para gerar um único commit.

- Árvore:      diretório de trabalho, onde editamos nossos arquivos
- Indice:      conjunto de alterações prontas para serem enviadas pro repositório
- Repositório: onde ficam guardados os commits (diretorio .git)

```
     Árvore                      Índice              Repositório
       |                           |                       |
       |---[ git add arq1.php ]--->|                       |
       |---[ git add arq2.php ]--->|                       |
       |---[ git add arq3.php ]--->|                       |
       |                           |                       |
       |                           |---[ git commit ]----->|
       |                           |                       |
[ edit arq1.php ]                  |                       |
       |                           |                       |
       |---[ git commit arq1.php ]------------------------>|
       |                           |                       |
       |                           |                       |
       |                           |                       |
```

## Descartando alterações não-comitadas ##

Conteúdo do arquivo **hello.php**

```php
<html>

<body>
<?php
  echo "Bem vindo ao hangout de Git";
  echo "Hoje demonstraremos como usar o Git para desenvolver seus projetos";
?>
</body>
</html>
```

Vamos inspecionar o que acabamos de alterar:

```
$ git diff
...
```

Para descartar esta alteração podemos usar um dos sequintes métodos:

1. `$ git reset`: Descartar o índice, mantendo a árvore intacta. Não nos serve agora pois não fizemos `add`
2. `$ git reset --hard`: Descartar todas as alterações na árvore, voltando ao último commita do repositório
3. `$ git checkout -- hello.php`: Retornar arquivos específicos ao último commit

Visualmente temos:

```
     Árvore                      Índice              Repositório
       |                           |                       |
       |<---[ git checkout -- arquivo... ]-----------------|
       |                           |                       |
       |<---[ git reset --hard ]---------------------------|
       |                           |                       |
       |                           |                       |
```

## Interrompa uma edição ##

Conteúdo do arquivo **hello.php**:

```php
<!doctype html>
<html lang="pt-BR">
<head>
  <title>Hangout Git</title>
  <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
</head>

<body>
<?php
  echo "Bem vindo ao hangout de Git";

  if ($_GET["oldschool"] == "true") {
    echo "Hoje demonstraremos como usar o Git para desenvolver seus projetos";
  } else {
    echo "Hoje demonstraremos como usar o CVS para desenvolver seus projetos";
  }
?>
</body>
</html>
```

Subitamente aparece um bug report! OMG!!!
Já existem alterações na árvore, e o bug interfere exatamente neste local onde estamos editando!

A solução é simples:

```
$ git stash save "oldschool"
Saved working directory and index state On master: oldschool
HEAD is now at 47ebd01 Tags HTML
```

O comando stash salva as alterações na árvore e volta para o estado do último commit.

```bash
$ git stash list
$ git stash apply oldscholl       <--- reaplica as alterações do stash
$ git stash pop oldschool         <--- reaplica as alterações e apaga o stash
```

Depois de corrigir o bug, comitamos:

```diff
$ git diff
diff --git index.php index.php
index 8d233d0..bb2c260 100644
--- index.php
+++ index.php
@@ -1,6 +1,7 @@
 <html>
 <head>
   <title>Hangout Git</title>
+  <meta name="description" content="Git - Do desenvolvimento ao Deploy">
 </head>

 <body>

$ git commit -a -m 'Fixed bug #1'
```

Voltamos à nossa árvore antiga, salva no stash:

```
$ git stash pop oldschool
Auto-merging index.php
CONFLICT (content): Merge conflict in index.php
```

ARGH! Conflito!

Como nosso ultimo commit mexeu na mesma região salva no stash, ao tentar reaplica-lo
o git travou, pois não sabe qual a versão "correta".

Solução: abrir o arquivo com conflito e resolver na mão.

Conflitos são sinalizados diretamente no arquivo, usando marcadores especiais:

```
<<<<<<< Updated upstream
Alterações que já existiam entram aqui (nesse caso, o ultimo commit)
=======
Alterações que tentam entrar sao colocadas aqui (nesse caso, o stash)
>>>>>>> Stashed changes
```

Edite à vontade. Depois comite as alterações.

Conflitos podem acontecer sempre que dois commits interferem no mesmo ponto de um arquivo.

## Repositório remoto ##

Git foi projetado para trabalhar de forma distrbuida. Isso significa que cada repositório é independente.
Em tese, não existe um repositório central.
Na prática, projetos de software  costumam eleger um local remoto onde todos os desenvolvedores publicam suas
alterações e atualizar seus repositórios locais com as alterações de outras pessoas.

Na Getup, ao criar uma aplicação, o usuário recebe um repositório git no servidor (dentro do primeiro gear).
Vamos usar este local como repositório remoto, baixando o código inicial da aplicação e enviando novos commits
para lá.

Primeiro criamos uma aplicação no [dashboard web](https://broker.getupcloud.com). Caso você não possua uma conta na Getup, cadastre-se gratuitamente em [http://getupcloud.com](http://getupcloud.com).

A titulo de exemplo, criarei uma aplicação chamada `myapp`. Toda aplicação disponibiliza uma *GIT_URL* para baixar o repositório, e apresenta o seguinte formato:

```
ssh://[UUID]@[APPNAME]-[NAMESPACE].getup.io/~/git/[APPNAME].git/
```

Onde:

- **[UUID]**: identificação única do gear da aplicação
- **[APPNAME]**: nome da aplicação - compõe parte da URL HTTP
- **[NAMESPACE]**: identificador único do usuário - compõe parte da URL HTTP

A URL HTTP da aplicação apresenta o seguinte formato:

```
http://[APPNAME]-NAMESPACE].getup.io
https://[APPNAME]-NAMESPACE].getup.io          <--- SSL wildcard já incluso
```

Como meu namespace é `caruccio`, esta aplicação será acessível em http://myapp-caruccio.getup.io.

Agora podemos baixar o código fonte. Usamos o comando `clone` para baixar o repositório remoto:

```bash
$ git clone ssh://[UUID]@myapp-caruccio.getup.io/~git/myapp.git/
$ cd myapp/
````

*Obs: `[UUID]` deve ser substituído pelo valor informado no momento da criação da aplicação.*

A partir deste ponto segue o fluxo *Edit -> Commit* normal.

Publicamos as alterações no repositório remoto usando o comando `push`:

```bash
$ git push
```

Cada commit realizado localmente será repetido no repositório remoto.
Para atualizar (baixar) o repositório local com as alterações remotas (por exemplo, feitas por outro usuário), use o comando:

```bash
$ git pull
```

## Múltiplos repositórios remotos ##

É possível, e até comum, existir mais de um repositório remoto para um dado repositório local.
A lista de repositórios remotos é acessível através do comando `remote`:

```bash
$ git remote -v
origin	ssh://528bd5fd99fc7721b90002f3@myapp-caruccio.getup.io/~/git/myapp.git/ (fetch)
origin	ssh://528bd5fd99fc7721b90002f3@myapp-caruccio.getup.io/~/git/myapp.git/ (push)
```

Podemos adicionar, editar e remover remotes a vontade. Uma finalidade é usar um remote para o código "live" e outro para backup.

Adicionar um repositório é simples:

```bash
$ git remote add backup /tmp/backup/projeto.git
````

Agora temos dois remotos configurados em nosso repositório:

```
$ git remote -v
backup	/tmp/backup/projeto.git (fetch)
backup	/tmp/backup/projeto.git (push)
origin	ssh://528bd5fd99fc7721b90002f3@myapp-caruccio.getup.io/~/git/myapp.git/ (fetch)
origin	ssh://528bd5fd99fc7721b90002f3@myapp-caruccio.getup.io/~/git/myapp.git/ (push)
```

Antes de enviar nossas alterações para o repositório de backup, vamos cloná-lo em `/tmp/backup/`:

```bash
$ mkdir /tmp/backup
$ cd /tmp/backup
$ git clone $OLDPWD --bare
$ ls -l
```

A flag --bare clona apenas o repositório (dir .git/), sem a árvore, ideal para backups.

Agora, de volta ao nosso repositório orignal, podemos alterar a árvore, comitar e fazer push para o novo remote:

```bash
$ cd -
$ git push backup
```

O remote padrão é aquele que foi originalmente clonado, portanto não é necessário especificá-lo no comando `push`.
Por padrão este remoto chama-se `origin`, por ser a origem do repositório local.

## OpenShift e os hooks de deploy ##

Estamos utilizando neste tutorial um serviço de plataforma (PaaS) projetada para cuidar do deploy e aspectos gerenciais da infraestrutura, abstraindo servidores, atualização de SO, isolamento de serviços e escalabilidade.

Neste ponto entram os hooks do git, nada mais do que scripts executados em diversos momentos do fluxo de desenvolvimento.

Alguns exemplos comuns são:

#### No lado do cliente ####

- pre-commit: executado antes do commit, pode ser usado para executar testes e bloquear o commit em caso de erro
- post-commit: executado logo após o commit, pode ser usado para notificações (ex: enviar email)

#### No lado do servidor ####

- pre-receive: executado antes de receber uma atualização, pode ser usado para verificar se o usuário possui permissão para alterar os arquivos que estão sendo enviados.
- post-receive: executado após a recepção das alterações, pode ser usado para notificações, atualizar sistema de build, reiniciar serviços

No OpenShift ambos os hooks pre-receive e post-receive são utilizados para controlar o deploy das aplicações, reiniciando serviços e notificando o
resto do sistema.

### Estrutura da aplicação ###

Uma aplicação OpenShift apresenta uma estrutura simples.

- **.openshift/action_hooks/**

Guarda scripts executados durante o build e deploy da aplicação no servidor (análogos aos hooks do .git).

- **.openshift/markers/**

Guarda arquivos que servem de flags para o sistema. Por exemplo, caso o arquivo `.openshift/markers/hot_deploy` exista durante o deploy, o sistema não realiza o restart da aplicação.

- **php/** (Arquivos da aplicação)

Como criamos uma aplicação php, temos um diretório `php/`, onde podemos desenvolver nosso projeto. O mesmo aplica-se para diferentes tipos de linguagens (python, ruby, nodejs, java, etc...), contudo cada linguagem apresenta uma estrutura ligeiramente diferente, baseado em seus padrões específicos.

A partir de agora vamos utilizar este repositório como exemplo.
A aplicação online pode ser acessada na url http://myapp-[NAMESPACE].getup.io

## Branch ##

Um conceito largamente difundido em sistemas de controle de versão chama-se `branch`: um desvio do fluxo normal, com sua própria história, iniciado a partir de um commit pré-existente.

Em outras palavras, usamos um branch para desenvolver paralelamente ao fluxo (branch) normal. Imagine a situação abaixo:

```
[ a ]<-----[ b ]<-----[ c ]
```

Onde `a` é o primeiro commit do repositório e `c`  o último. Dizemos que `a` é o "parent" (pai) de `b`. Isto é um branch, e no git este branch especificamente é chamado de `master`, por ser o primeiro e principal branch do repositório.

De fato, o último commit de um branch possui um nome mais amigável, com significado (ao invés de 01318aac39fc5e660b28b42eee6dd5a3c382495e, por exemplo):

```
[ a ]<-----[ b ]<-----[ c ]<-----{ master }
```

Note que `{ master }` não é um commit, mas uma referência ao último commit de um branch.

Suponha agora que vamos desenvolver uma nova feature para nosso projeto. Naturalmente abrimos os arquivos, editamos e comitamos. Esse até pode ser um bom caminho, mas em projetos onde existem mais desenvolvedores trabalhando podemos gerar conflitos desnecessários. Além disso, se nossas alterações forem mais longas, estaremos impedidos (moralmente) de enviar elas para o repositório remoto com frequencia, pois assim vamos publicar código incompleto.

Novas features são normalmente desenvolvidas em branches exclusivos para elas, chamados *feature branch*.

Primeiro, vamos descobrir em qual branch estamos com o comando `branch`:

```
$ git branch
* master
```

Por enquanto temos apenas o branch `master`, e estamos usando-o nesse momento pois existe um `*` na frente dele.

Antes de continuar, altera o arquivo `php/index.php` para o conteúdo abaixo.

```php
<!doctype html>
<html>
<body>
<?php
    echo '<p>Visitante, bem-vindo ao hangout de Git!</p>';
?>
</body>
</html>
```

Revise, comite e publique as alterações:

```
$ git diff
$ git commit -a -m 'Mensagem de boas vindas'
$ git push
```

As alterações já devem estar disponíveis na URL do app (http://myapp-caruccio.getup.io).

### Trabalhando com branchs ###

Vamos incluir uma feature em nossa aplicação: permitir que o usuário informe seu nome. 
Usaremos um branch específico para adicionar essa "feature". Podemos chamá-lo de "feature-name":

```
$ git branch feature-name
```

Apenas a criação não nos transporta para este branch. Precisamos dizer explicitamente: *agora quero mudar para o branch X*:

```bash
$ git checkout feature-name
Switched to branch 'feature-name'

$ git branch
* feature-name                            <----- agora estamos no branch feature-name
  master
```

Pronto, agora qualquer alteração no repositório será aplicada apenas no branch `feature-name`, deixando o `master` intacto.

Dica: Um atalho para criar e trocar para um novo branch é usar diretamente o comando `checkout` com a flag `-b`:

```bash
$ git checkout -b feature-name            <----- cria e muda para o novo branch

$ git branch
* feature-name
  master
```

Edite o arquivo `php/index.php`e adicione as seguintes alterações:

```php
<!doctype html>
<html>
<body>
<?php
    if (array_key_exists('nome', $_REQUEST)) {
        $nome = $_REQUEST['nome'];
    } else {
        $nome = 'Visitante';
    }

    echo '<p>' . $nome . ', bem-vindo ao hangout de Git!</p>';
?>
</body>
</html>
```

Comite as alterações e inspecione o log:

```
$ git commit -a -m 'Mostra nome do visitante'

$ git log
commit 4741baed9ea1a3e155a2a5d4a01f762a0a128d1d              <----- nosso último commit (versão dinâmica)
Author: Mateus Caruccio <mateus.caruccio@getupcloud.com>
Date:   Thu Nov 28 15:28:44 2013 -0200

    Mostra nome do visitante

commit 9a1c8f8c8b55a803275af073367928d6b9c72673              <----- nosso primeiro commit (versão estática)
Author: Mateus Caruccio <mateus.caruccio@getupcloud.com>
Date:   Thu Nov 28 15:14:18 2013 -0200

    Mensagem de boas vindas

commit de2b8b673d98ab8b72fc09af93bca6e278ac3c12              <----- primeiro commit, veio com o template
Author: Template builder <builder@example.com>
Date:   Thu Nov 28 13:47:54 2013 +0000

    Creating template
```

Lembre que ainda estamos no branch `feature-name`. Vamos incluir mais uma pequena alteração, para mostrar o nome do visitante *Capitalizado*:

```diff
diff --git php/index.php php/index.php
index 05c0354..069ac1e 100644
--- php/index.php
+++ php/index.php
@@ -3,7 +3,7 @@
 <body>
 <?php
     if (array_key_exists('nome', $_REQUEST)) {
-        $nome = $_REQUEST['nome'];
+        $nome = ucwords($_REQUEST['nome']);
     } else {
         $nome = 'Visitante';
     }
```

Altere o arquivo de acordo com o diff acima, comite e inspecione o log:

```
$ git commit -a -m 'Nome capitalizado'

$ git log --oneline
39ccb1c Nome capitalizado
4741bae Mostra nome do visitante
9a1c8f8 Mensagem de boas vindas
de2b8b6 Creating template
```

Dica: a flag `--oneline` mostra os commits de forma comprimida, útil quando há muitos commits no branch.

Agora vamos voltar para o branch master para aplicar estas alterações.

```
$ git checkout master
$ git log --oneline
9a1c8f8 Mensagem de boas vindas
de2b8b6 Creating template
```

Veja como `master` está diferente de `feature-name`. Quando criamos o branch, partimos do segundo commit do `master`, e adicionamos dois commits somente em `feature-name`.

Visualmente nosso repositório parece com o seguinte:

```
[ de2b8b6 ]<-----[ 9a1c8f8 ]<-----{ master }
                      ^
                      |
                      |
                      +----[ 4741bae ]<-----[ 39ccb1c ]<-----{ feature-name }
```

O que queremos agora é aplicar estes dois commit (`4741bae` e `39ccb1c`) em `master`. Para isso usamos o comando `merge`: 

```
$ git merge feature-name
Updating 9a1c8f8..39ccb1c
Fast-forward
 php/index.php | 8 +++++++-
 1 file changed, 7 insertions(+), 1 deletion(-)
```

Inspecionando o log em `master` nos mostra:

```
$ git log --oneline
39ccb1c Nome capitalizado
4741bae Mostra nome do visitante
9a1c8f8 Mensagem de boas vindas
de2b8b6 Creating template
```

Agora nosso repositório parece com o seguinte:

```
[ de2b8b6 ]<-----[ 9a1c8f8 ]<-----[ 4741bae ]<-----[ 39ccb1c ]<-----{ master }
                      ^
                      |
                      |
                      +----[ 4741bae ]<-----[ 39ccb1c ]<-----{ feature-name }
```

Estamos prontos para publicar novamente nossa aplicação:

```
$ git push
```

## Referências ##

Excelente livro sobre git:

http://git-scm.com/book/pt-br

Site interativo com os estágios e seus respectivos possíveis comandos:

http://ndpsoftware.com/git-cheatsheet.html

Referência visual com explicações sobre os comandos:

http://marklodato.github.io/visual-git-guide/index-en.html

Guia rápido de referência de comandos:

http://byte.kde.org/~zrusin/git/git-cheat-sheet-medium.png

