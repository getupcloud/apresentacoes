Git - Do desenvolvimento ao deploy
==================================

Hangout realizado em 28/11/2013.
Assista ao video em nosso [canal do youtube](http://www.youtube.com/channel/UCF8LFCqMG_t_arG5LMQY5uw)

Introdução
==========

Como nossos antepassados faziam nas cavernas:

 - Projeto.zip
 - Projeto-editado.zip
 - Projeto-revisao-1.zip
 - Projeto-alteracao-nova-versao.zip
 - Projeto-ultima-versao.zip
 - Projeto-agora-sim-corrigido.zip
 - Projeto-ultima-versao-10-11-2013.zip
 - Projeto-correcao-do-layout.zip
 - Projeto-revisao-do-chefe-que-nao-gostou-das-cores-dos-botoes.zip
 - ...e assim até a eternidade

Imagine uma equipe com varias pessoas (>1) editando o projeto e enviando arquivos
por email, umas para as outras -> CAOS TOTAL!

Cada alteração precisa ser aplicada manualmente.

Não existe histórico organizado das alterações.

Sem contar que .tar.gz é muito melhor que .zip!

Controle de Versão
==================

Entra em cena o Sistema de Controle de Versão (VCS - Version Control System)

- Repositório: diretório com arquivos "monitorados" - alterações são gravadas em lote, sequencialmente.
- Commit: alteração atômica de um ou mais arquivos.
- Merge: ato de aplicar uma ou mais alterações ao repositório automaticamente
- Conflito: quando duas alterações ocorrem no mesmo ponto de um arquivo - necessita intervenção manual
- Branch: diferentes linhas de tempo - sequencias de commits podem ocorrem em "troncos" paralelos

Git
===

Desenvolvido por Linus Torvalds para atender as necessidades exigidas para o desenvolvimento do Linux:

- Distribuido (Distributed VCS)
- Rápido - pensado em um base de código gigante (vide kernel.org)

Padrão entre projetos Open Source (GitHub, GitLab, Gitorious, BitBucket...)

Instalação
==========

Provavelmente você já possui instalado, abra um terminal e digite:

  $ git --version
  git version 1.8.3.4 (Apple Git-47)

Para instalar:

  Debian/Ubuntu:
    # apt-get install git

  RedHat/CentOS/Fedora:
    # yum install git

  Mac:     http://code.google.com/p/git-osx-installer
  Windows: http://msysgit.github.com

Instruções detalhadas:

  http://git-scm.com/book/pt-br/Primeiros-passos-Instalando-Git

Identifique-se!
===============

  $ git config --global user.name  "Seu Nome"
  $ git config --global user.email "seu@email.com"

Iniciando um repositório
========================

  $ mkdir projeto
  $ cd projeto
  $ git init .

Diretório .git/ contém metadados do repositório:

  - config
  - commits
  - branches
  - tags
  - remotes
  - hooks

Criando o primeiro arquivo
==========================

*** hello.php ***
<?php
  echo "Bem vindo ao hangout de git";
?>
*****************

 $ git add hello.php
 $ git commit            ---> abre um editor para inserir uma mensagem de commit
   ou
 $ git commit -m "Commit inicial do projeto"

- git add:    este(s) arquivo(s) devem ser monitorados a partir de agora
- git commit: guarde estas alterações

  $ git log
  commit 1fad7eb3eeffdd76be4d6c8b0ef6258e69425cb4      <--- hash unico do commit
  Author: Mateus Caruccio <mateus@caruccio.com>
  Date:   Wed Nov 27 10:11:07 2013 -0200
  
      Commit inicial do projeto


Alterações incrementais
=======================

   *** hello.php ***
   <?php
     echo "Bem vindo ao hangout de Git";
     echo "Hoje demonstraremos como usar o Git para desenvolver seus projetos";
   ?>
   *****************

Anatomia de um commit:

   $ git diff                                              <--- mostra alterações não comitadas
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

- Primeira coluna nao faz parte das alterações
- Novas linha são precedidas por um "+"
- Linha removidas são precedidas por um "-"
- "@@" localiza a alteração no arquivo (contexto)

   $ git commit -a                                         <--- -a commita todas as alterações
   [master 01318aa] Descrição melhorada
    1 file changed, 2 insertions(+), 1 deletion(-)


   $ git log
   commit 01318aac39fc5e660b28b42eee6dd5a3c382495e         <--- segundo commit
   Author: Mateus Caruccio <mateus@caruccio.com>
   Date:   Wed Nov 27 10:13:58 2013 -0200
   
       Descrição melhorada

   commit 1fad7eb3eeffdd76be4d6c8b0ef6258e69425cb4         <--- primeiro commit
   Author: Mateus Caruccio <mateus@caruccio.com>
   Date:   Wed Nov 27 10:11:07 2013 -0200
   
       Commit inicial do projeto

Árvore e índice
===============

Árvore:      diretório de trabalho, onde editamos nossos arquivos
Indice:      conjunto de commits para enviar ao repositório
Repositório: onde ficam guardados os commits (diretorio .git)


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
       |---[ git commit ]--------------------------------->|
       |                           |                       |
       |                           |                       |
       |                           |                       |


Descartando alterações não-comitadas
====================================

*** hello.php ***
<html>

<body>
<?php
  echo "Bem vindo ao hangout de Git";
  echo "Hoje demonstraremos como usar o Git para desenvolver seus projetos";
?>
</body>
</html>
*****************

$ git diff
...

$ git reset                           <--- descarta indice, mantém arvore intacta (não nos serve agora)

$ git reset --hard                    <--- descarta alterações na árvore e volta ao ultimo commit
                                           *** CUIDADO!!! Não pode ser desfeito ***

$ git checkout -- hello.php           <--- retorna arquivo ao ultimo commit

     Árvore                      Índice              Repositório
       |                           |                       |
       |<---[ git checkout -- arquivo... ]-----------------|
       |                           |                       |
       |<---[ git reset --hard ]---------------------------|
       |                           |                       |
       |                           |                       |

Inspecionando commits
=====================

$ git show [COMMIT]

*** index.php ***
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
*****************

$ git commit -a -m 'Tags HTML'
$ git log
...
$ git show [COMMIT-1]
$ git show [COMMIT-2]


Guardar alterações da árvore para continuar mais tarde
======================================================

*** index.php ***
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
*****************

Subitamente aparece um bug report! OMG!!!
Já existem alterações na árvore, e o bug interfere exatamente neste local.

Simples:

   $ git stash save "oldschool"
   Saved working directory and index state On master: oldschool
   HEAD is now at 47ebd01 Tags HTML

O comando stash salva as alterações da árvore e volta para o estado do último commit.

   $ git stash list
   $ git stash apply oldscholl       <--- reaplica as alterações do stash
   $ git stash pop oldschool         <--- reaplica as alterações e apaga o stash

Corrigimos o bug e comitamos:

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

Voltamos a nossa arvore antiga, salva no stash:

   $ git stash pop oldschool
   Auto-merging index.php
   CONFLICT (content): Merge conflict in index.php

ARGH! Conflito!

Como nosso ultimo commit mexeu na mesma região salva no stash, ao tentar reaplica-lo
o git travou, pois não sabe qual a versão "correta".

Solução: abrir o arquivo com conflito e resolver na mão.

Conflitos são sinalizados diretamente no arquivo, usando marcadores especiais:

   <<<<<<< Updated upstream
   Alterações que já existiam entram aqui (nesse caso, o ultimo commit)
   =======
   Alterações que tentam entrar sao colocadas aqui (nesse caso, o stash)
   >>>>>>> Stashed changes

Edite a vontade e comite as alterações normalmente.

Conflitos podem acontecer sempre que dois commits interferem no mesmo ponto de um arquivo.

Repositório remoto
==================

Git foi projetado para trabalhar de forma distrbuida. Isso significa que cada repositório é independente.
Em tese, não existe um repositório central.
Na prática, projetos de software  costumam eleger um local remoto onde todos os desenvolvedores publicam suas
alterações e atualizar seus repositórios locais com as alterações de outras pessoas.

Na getup, ao criar uma aplicação, o usuário recebe um repositório git no servidor (dentro do primeiro gear).
Vamos usar este local como repositório remoto, baixando o código inicial da aplicação e enviando novos commits
para lá.

Primeiro criamos uma aplicação no dashboard web, que disponibiliza uma URL para baixar a aplicação no formato:

   ssh://UUID@APPNAME-NAMESPACE-SUBDOMAIN/~/git/APPNAME.git/

O comando clone faz o download de um repositório remoto:

   $ git clone <GIT_URL>
   $ cd <REPO>

A partir desse ponto segue o fluxo Edit->Commit normal.

Publicamos as alterações no repositório remoto usando o seguinte comando:

   $ git push

Cada commit realizado localmente será repetido no repositório remoto.
Para atualizar o repositório local com as alterações remotas (por exemplo, feitas por outro usuário), use o comando:

   $ git pull

Multiplos repositorios remotos
==============================

É possível, e até comum, existir mais de um repositório remoto para um dado repositório local.

A lista de repositórios remotos é acessível através do comando remote:

   $ git remote -v

Podemos adicionar, editar e remover remotes a vontade. Uma utilidade para isso é usar um remote para o código "live" e outro para backup.

Adicionar um repositório é simples:

   $ git remote add backup /home/backup/projeto.git

Antes de enviar nossas alterações para o reposito de backup, vamos clona-lo em /tmp/backup/:

   $ mkdir /tmp/backup
   $ cd /tmp/backup
   $ git clone $OLDPWD --bare               <----- a flag --bare clona apenas o repositório (dir .git/), sem a árvore, ideal para backups.
   $ cd -

Agora, de volta ao nosso repositório inicial, podemos alterar a árvore, comitar e fazer push para o novo remote:

    $ git push backup

O remote padrão é aquele que foi originalmente clonado, portanto não é necessário especificálo no comando push.

OpenShift e os hooks de deploy
==============================

Estamos utilizando neste tutorial um serviço de plataforma (PaaS) projetada para cuidar do deploy e aspectos gerencias da infraestrutura,
abstraindo servidores, atualização de SO, isolamento de serviços e escalabilidade.

Neste ponto entram os hooks do git, nada mais do que scripts executados em diversos momentos do fluxo de desenvolvimento.

Alguns exemplos comuns são:

   No lado do cliente

   - pre-commit: executado antes do commit, pode ser usado para executar testes e bloquear o commit em caso de erro
   - post-commit: executado logo após o commit, pode ser usado para notificações (ex: enviar email)

   No lado do servidor

   - pre-receive: executado antes de receber uma atualização, pode ser usado para verificar se o usuário possui permissão para alterar os arquivos que estão sendo enviados.
   - post-receive: executado após a recepção das alterações, pode ser usado para notificações, atualizar sistema de build, reiniciar serviços

No OpenShift ambos os hooks pre-receive e post-receive são utilizados para controlar o deploy das aplicações, reiniciando serviços e notificando o
resto do sistema.



Referências
===========

http://marklodato.github.io/visual-git-guide/index-en.html
http://ndpsoftware.com/git-cheatsheet.html
http://byte.kde.org/~zrusin/git/git-cheat-sheet-medium.png
http://git-scm.com/book/pt-br
