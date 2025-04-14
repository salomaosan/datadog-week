# Dia 1

> ## Pre-requisitos
>
> ### Conta na Datadog - Conta TRIAL ou Learning Center
> 
> Acesse [DatadogHQ](https://www.datadoghq.com/) e clique em **FREE TRIAL** cadastre-se ou
> 
> Acesse [Learning Center Datadog](https://learn.datadoghq.com/users/sign_in) clique em **Create a new account** e acesse um lab disponível para estudo e a própria Datadog gerará uma conta temporária para você se divertir
> 
> Se ficar na dúvida assista o video [Youtube](https://youtu.be/OO3lVsqf_44?si=C7Xiu8_2AODzhvyw)
> 
> ### Kubernetes para executar a aplicação
> 
> Acesse [Kubernetes playgrounds](https://killercoda.com/playgrounds/scenario/kubernetes)
> Observação:
> 
> No killercoda se deixar o cluster lab numa aba sem ativida o mesmo pode ser encerrado
>

>**💡NOTA**
>
>Os arquivo dentros do diretório ***spree-ecommerce*** é o resultado das alterações ao final do Dia 1. Dúvidas após o 1º dia consulte esses arquivos


## 1º passo - Para subir a aplicação sem alteração

Clone o repositório e acesse o diretório ***maratona-datadog*** e suba a aplicação
```bash
kubectl apply -f spree-ecommerce/
```

## 2º Passo - Instalação de repositórios

Agora, adicione o repositório helm da Datadog

```bash
helm repo add datadog https://helm.datadoghq.com
```

Atualize

```bash
helm repo update
```

Antes de subir o agente no cluster, vamos criar um secrets Datadog em um comando

```bash
kubectl create secret generic datadog-secret --from-literal api-key=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

## 3º Instalando Datadog Agent com values.yaml

Baixe o arquivo completo link [datadog-values.yaml](https://github.com/DataDog/helm-charts/blob/main/charts/datadog/values.yaml)

* Parametros alterarados

    ```bash
    # Paramentros importantes
    datadog.apiKeyExistingSecret ## linha 105
    datadog.clusterName ## linha 105
    datadog.site ## linha 116
    datadog.tags ## linha 285
    datadog.kubelet.tlsVerify ## linha 304
    datadog.logs.enabled ## linha 458
    datadog.logs.containerCollectAll ## linha 463
    datadog.apm ## linha 479
    datadog.apm.portEnabled ## linha 483
    datadog.processAgent.enabled ## linha 727
    datadog.processAgent.processCollection ## linha 735
    clusterAgent.replicas ## linha 1175
    clusterAgent.createPodDisruptionBudget ## linha 1532
    ```

Execute o comando abaixo para atualizar o agent

```bash
helm install datadog-agent -f values.yaml datadog/datadog
```

## 4º Passo - Incrementando o monitoramento no PostgreSql Integrações 

### Esse passo por questões tecnicas da live será apresentedo no Dia-2

No arquivo db.yaml altere as ```anotations``` adicionando o seguinte em ```template``` para monitorar o Postgresql.

```bash
      annotations:
        ad.datadoghq.com/postgres.checks: |
          {
            "postgres": {
              "instances": [
                {
                  "host": "%%host%%",
                  "port":"5432",
                  "username":"user",
                  "password":"datadog"
                }
              ]
            }
          }
```

Re-deploy o banco Postgresql

**FIM DO DIA 1**