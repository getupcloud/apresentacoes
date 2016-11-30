Webinar Kubernetes e OpenShift — Build e Deploy Avançado
========================================================

Vídeo e blog post em https://blog.getupcloud.com/webinar-kubernetes-e-openshift-build-e-deploy-avan%C3%A7ado-725d21b83036

Docker
-------

- Imagem = pacote estático de aquivos que compoem a aplicação
- Container = Imagem em execução
- Foco em:
  - Empacotar & executar código portável

- Kubernetes
- Framework de gerenciamento de container
- Foco em:
  - criar containers
  - manter rodando
- Declarativo
  - objetos
  - estados
- Principais componentes
  - POD
  - Replication Controller
  - Service
  - Labels / Selectors

- Openshift
  - Plataforma para criar e executar aplicações conteinerizadas
  - Extensão do Kubernetes
  - Foco
    - Ferramentas de desenvolvimento
      - Usabilidade
      - Build & Deploy
      - Image Stream = Security updates facil
      - Logging
      - Métricas
- Principais componentes
  - Build Config
  - Deployment Config
  - Image Stream

Objeto BuildConfig
==================

Construir um app php 5.6 no painel

    https://github.com/caruccio/php-test.git

Escalar para 5

    $ oc get pods -w      # mostra lista de pods

Rodar curl repetidamente para mostar hostname mudando

    curl http://site-webinar.getup.io/host.php
 
Ver objeto BuildConfig

```
$ oc get buildconfigs
$ oc describe buildconfigs/site
$ oc export bc/site
$ oc edit bc/site            # mudar imagem para php:7.0
$ oc start-build site -F
```

Instalar webhooks no github e editar o código para dispara um build

    $ oc describe bc/site | grep github

Escalar para 1

    $ oc scale dc/site --replicas=1

Source-to-Image
===============

Ferramenta para fazer build de código e injetar em uma imagem Docker

É exatamente o mesmo mecanismo utilizado para fazer build de código na plataform

Beneficio:
- Build reproduzivel
  - apenas uma interface -> injetar código
  - facilita updates de segurança

- Flexível
  - scripts de build/run personalizaveis
  - integravel em CI/CD

- Velocidade
  - A aplicação entra como uma única layer Docker
    - cache mais eficiente

- Seguro
  - permite controle de quais permissoes o usuario recebe durante o build
  
```
host $ s2i build https://github.com/caruccio/php-test.git getupcloud/php-70-centos7 demo
host $ docker run -it -p 8080:8080 demo
host $ docker run -it demo bash
container $ ls -la $STI_SCRIPTS_PATH
container $ cat $STI_SCRIPTS_PATH/assemble
container $ cat $STI_SCRIPTS_PATH/run
```

Script de build personalizado
=============================

Criar .s2i/bin/assemble:

```
#!/bin/bash

source ${STI_SCRIPTS_PATH}/assemble

if [ -x test.sh ]; then
    ./test.sh
fi
```

Criar test.sh:

```
#!/bin/bash

echo '--> Executando testes'

grep 'Hello World' index.php || {
    echo 'Teste <title> falhou'
    exit 1
}

echo '--> Testes finalizados'
```

Criar .s2i/bin/run

```
#!/bin/bash

date > started.php

source ${STI_SCRIPTS_PATH}/run
```

Criar .s2i/environment

```
ENV=dev
```

Build a partir do diretório local:

    $ s2i build . getupcloud/php-70-centos7 demo --copy

Build incremental
=================

Reaproveitar artefatos gerados em um build anterior => maior velocidade no build

Build incremental envolve 3 estágios:

- gerar os artefatos
- extrair os artefatos de uma imagem anterior
- instalar os artefatos na nova imagem

Primeiro criamos um composer.json para gerar o diretorio vendor/ com as libs que nossa app depende

Criar composer.json

```
{
    "require": {
        "monolog/monolog": "1.22.0"
    }
}
```

Depois criamos o script que vai executar no container anterior e extrair todo e qualquer arquivos que desejamos reaproveitar


Criar .s2i/bin/save-artifacts

```
#!/bin/bash
tar cf - .composer composer.lock vendor
```

Por fim editamos o script de build (assemble) para instalar os artefatos antes de prosseguir com o build normalmente

Criar .s2i/bin/assemble

```
#!/bin/bash

if [ -d /tmp/artifacts/ ]; then
    echo "---> Restoring build artifacts from /tmp/artifacts..."
    shopt -s dotglob                ### pega arquivos iniciados com .
    mv -v /tmp/artifacts/* ./
    shopt -u dotglob
fi

source ${STI_SCRIPTS_PATH}/assemble   ### executa o assemble original
```

Para ativar na plataforma:

```
$ oc edit bc/site
spec:
  strategy: 
    sourceStrategy:
      incremental: true
```

Comitar e iniciar o build:

```
$ git add .s2i/bin/assemble .s2i/bin/save-artifacts
$ git commit -m 'build incremental'
$ git push
$ s2i build --incremental . getupcloud/php-70-centos7 demo
```

**Note que o primeiro build após ativar o incremental, NÃO pode rodar o script save-artifacts.
Por causa disso o primeiro build incremental serve apenas para iniciarlizar o mecanismo, mas ele
próprio não consegue aproveitar os artefatos do build anterior.**

Build binário
-------------

Enviar o código diretamente para o container de build a inves de um repositório
Ideal para quem não pode permitir acesso ao seu repositório de fontes.

```
$ oc edit bc/app
    spec.source:
        type: Binary

$ oc start-build site --from-dir=.
```

E para atualizar um container em tempo real

```
host $ oc get pods
host $ oc rsync . <POD>: --watch --no-perms --exclude=.git,.s2i,*.swp
host $ oc rsh <POD>
container $ cat index.html
```

Voltar para build type=source:

```
spec:
  source:
    contextDir: /
    git:
      ref: master
      uri: https://github.com/caruccio/php-test.git
    type: Git
```

Objeto DeploymentConfig
=======================

Define a configuração de deploy da aplicação

```
$ oc get deploymentconfigs
$ oc describe deploymentconfigs/site
$ oc edit dc/site
```

Principais campos

- selector
- strategy
- template
  - metadata.labels
  - spec.containers
    - name
    - image
    - env
    - ports

Editar replicas = 10 e matar um POD

```
$ oc get pods
$ oc delete pods/$POD
```

Editar label de um POD para remover do DC

```
$ oc edit pods/$POD
$ oc delete pods/$POD>
```

Estratégias de deploy: Rolling
==============================

Substitui aos poucos os PODs antigos pelos novos

    $ oc edit dc/site

Editar strategy:

```
spec
  strategy:
    type: Rolling
    rollingParams:
      maxUnavailable: 30%      # qtd ou % de pods antigos removidos antes de criar novos
      maxSurge: 30%            # qtd ou % de pods novos criados antes de remover antigos
```

Mostrar log de deploy

    $ oc deploy --latest --follow

Estratégias de deploy: Recreate
===============================

Remove todos os PODs antigos e depois cria os novos

    $ oc edit dc/site

Editar strategy:

```
spec
  strategy:
    type: Recreate
```

Deploy hooks: pre, mid & post
=============================

Permite executar hooks antes, durante (replicas 0) e após o processo de  deploy

```
$ oc edit dc/site
spec:
  strategy:
    type: Recreate
    recreateParams:
      mid:                             # pre|mid|post
        execNewPod:                    # execNewPod|tagImages
          command:
          - ./migrate-db
          containerName: php
          env:
          - name: DATABASE_SERVICE_NAME
            value: mysql
          - name: DATABASE_NAME
            value: site
          - name: DATABASE_USER
            value: admin
          - name: DATABASE_PASSWORD
            value: password
        failurePolicy: Abort         # Retry|Abort|Ignore
```

Criar ./migrate-db

```
#!/bin/bash
echo "--> Migrando base de dados $DATABASE_USER@$DATABASE_SERVICE_NAME/$DATABASE_NAME"
sleep 3
echo '--> Base de dados migrada com sucesso'
```

Comitar e publicar:

```
chmod +x migrate-db
git add ./migrate-db && git commit -m 'Add migration' && git push
```

Os logs do deploy são acessiveis a partir dos logs do pod:

    $ oc get pods | grep deploy
    $ oc logs $DEPLOY_POD

Estratégia de deploy: A/B
=========================

Pemite balancear a carga entre duas (versões de uma) aplicaçõe(s).
Criei uma segunda aplicação (ex siteb) e edite a rota da app inicial:


```
$ oc edit routes/site
spec:
  to:
    kind: Service
    name: site

  alternateBackends:
  - kind: Service
    name: siteb
```

Estratégia de deploy: Blue/Green
================================

Permite chavear a rota entre duas aplicações distintas.
Basta remover a config `spec.alternateBackends` da rota e alterar o camp `spec.to.name` para o nome da app que deseja rotear.

App health: Readiness & Liveness
================================

Implementa __probes__ para determinar se um POD/container deve ser inserido/removido do balanceador ou reiniciado

```
spec:
  template:
    spec:
      container:
        readinessProbe:                       # remove POD do servico
          failureThreshold: 2
          httpGet:
            path: "/health.php?ready"
            port: 8080
            scheme: HTTP

          # Outros tipos de probe:
          #
          # exec:
          #   command:
          #   - cat
          #   - /tmp/health
          #
          # tcpSocket:
          #   port: 8080

        livenessProbe:                        # reinicia o container
          httpGet:
            path: "/health.php?live"
            port: 8080
            scheme: HTTP
          periodSeconds:      20              # frequencia do probe
          timeoutSeconds:     10              # timeout do probe


          # Ambos os tipos aceitas os seguintes parametros

          initialDelaySeconds: 5              # tempo de espera até iniciar probe
          successThreshold:    1              # qtd de sucessos para considerar up
          failureThreshold:    2              # qtd de falhas para considerar down
          periodSeconds:       20             # frequencia do probe
          timeoutSeconds:      10             # timeout do probe
```
          
Autoscale
=========

Escala automaticamente o número de réplicas de um DC a partir das métricas de CPU. Atualmente é necessário calcular o percentual proporcional ao limite de recursos do container:

```
cal_percent ()
{
    limits_cpu=$1; requests_cpu=$2; threshold_cpu=$3
    echo "((($limits_cpu * ($threshold_cpu / 100)) / $requests_cpu) * 100)" | bc -l | cut -f1 -d.
}
```

80 é o percentual de CPU que dispara o scale.
Para descobrir os parâmetros limits_cpu e requests_cpu busque os valores do campo resources do container no template:

```
$ oc export dc/site
spec:
  template:
    spec:
      containers:
        resources:
          limits:
            cpu: 366m
            memory: 512Mi
          requests:
            cpu: 10m
            memory: 128Mi
```

Agora basta criar  um HPA (Horizontal Pod Autoscaler):

    $ oc autoscale dc/site --min=1 --max=10 --cpu-percent=`cal_percent 366 10 80`

Jogando carga na aplicação podemos ver o HPA agindo:

    $ oc get hpa -w

Em outro terminal iniciamos o teste de carga (Ajusta para sua URL):

```
$ ab -c 50 -n 100000 \
  http://site-projeto.getup.io/fibonacci.php?value=29
```
