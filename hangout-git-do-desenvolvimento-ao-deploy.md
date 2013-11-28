# Git - Do desenvolvimento ao deploy #

Hangout realizado em 28/11/2013.

Assista ao video em nosso [canal do youtube](http://www.youtube.com/channel/UCF8LFCqMG_t_arG5LMQY5uw)

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
por email, umas para as outras -> CAOS TOTAL!

- Cada alteração precisa ser aplicada manualmente.
- Não existe histórico organizado das alterações.
- Sem contar que .tar.gz é muito melhor que .zip!

## Controle de Versão ##

Entra em cena o Sistema de Controle de Versão (VCS - Version Control System)

Alguns conceitos básicos:

- **Repositório**: diretório com arquivos "monitorados" - alterações são gravadas em lote, sequencialmente, criando um histórico.
- **Commit**: alteração atômica de um ou mais arquivos.
- **Merge**: ato de aplicar uma ou mais alterações ao repositório automaticamente
- **Branch**: diferentes linhas de tempo - sequencias de commits podem ocorrem em "troncos" paralelos
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
Para enxergar esta diferença usamod o comando `diff`:

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
$ git commit -a -m Descrição melhorada"                 <--- a flag -a commita todas as alterações
[master 01318aa] Descrição melhorada
 1 file changed, 2 insertions(+), 1 deletion(-)
```

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

Commits podem ser abreviados usando apenas seus números iniciais. Ao invés de `01318aac39fc5e660b28b42eee6dd5a3c382495e`, use `01318a`.

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

1. Descartar o índice, mantendo a árvore intacta. Não nos serve agora pois não fizemos `add`

```
$ git reset
```

2. Descartar todas as alterações na árvore, voltando ao último commita do repositório

```
$ git reset --hard
```

3. Retornar arquivos específicos ao último commit

```
$ git checkout -- hello.php
```

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

Toda aplicação disponibiliza uma GIT_URL para baixar o repositório, e apresenta o seguinte formato:

```
ssh://UUID@APPNAME-NAMESPACE-getup.io/~/git/APPNAME.git/
```

O comando `clone` faz o download do repositório remoto:

```bash
$ git clone <GIT_URL>
$ cd <DIR>
````

A partir desse ponto segue-se o fluxo *Edit -> Commit* normal.

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
```

Podemos adicionar, editar e remover remotes a vontade. Uma finalidade é usar um remote para o código "live" e outro para backup.

Adicionar um repositório é simples:

```bash
$ git remote add backup /tmp/backup/projeto.git
```

Antes de enviar nossas alterações para o reposito de backup, vamos cloná-lo em `/tmp/backup/`:

```bash
$ mkdir /tmp/backup
$ cd /tmp/backup
$ git clone $OLDPWD --bare
$ cd -
```

A flag --bare clona apenas o repositório (dir .git/), sem a árvore, ideal para backups.

Agora, de volta ao nosso repositório orignal, podemos alterar a árvore, comitar e fazer push para o novo remote:

```bash
$ git push backup
```

O remote padrão é aquele que foi originalmente clonado, portanto não é necessário especificá no comando push.

## OpenShift e os hooks de deploy ##

Estamos utilizando neste tutorial um serviço de plataforma (PaaS) projetada para cuidar do deploy e aspectos gerencias da infraestrutura, abstraindo servidores, atualização de SO, isolamento de serviços e escalabilidade.

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


## Referências ##

Excelente livro sobre git:

http://git-scm.com/book/pt-br

Site interativo com os estágios e seus respectivos possíveis comandos:

http://ndpsoftware.com/git-cheatsheet.html

Referência visual com explicações sobre os comandos:

http://marklodato.github.io/visual-git-guide/index-en.html

Guia rápido de referência de comandos:

http://byte.kde.org/~zrusin/git/git-cheat-sheet-medium.png

