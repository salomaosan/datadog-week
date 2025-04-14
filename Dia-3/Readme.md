# Dia 3

>## Pre-requisitos
>
>Esssa é uma continuação do ***Dia 2*** então recrie os acontecimentos do dia anterior e vamos em frente.
>
>Então, para acelerar o processo acesso o diretório do ***Dia 2*** para pegar as alterações feitas.
>```bash
>kubectl apply spree-ecommerce/
>```

>**💡NOTA**
>
>Os arquivo dentros do diretório ***spree-ecommerce*** é o resultado das alterações ao final do Dia 3. Dúvidas após o 1º dia consulte esses arquivos

## RUM (Real User Monitoring)

Para que o RUM passe a funcionar na aplicação, teremos que rebuildar a imagem do frontend, fazendo a inserção do JavaScrip SDK para enviar as interações da jornada do usuário com a aplicação.

O codigo javascript é fornecido pela Datadog, a unica alteração que fazemos e trasformar os valores dos campos em variáveis, segue:

```html
    <script
      src="https://www.datadoghq-browser-agent.com/us1/v5/datadog-rum.js" type="text/javascript">
    </script>
    <script>
      window.DD_RUM && window.DD_RUM.init({
        clientToken: '<%= ENV['DD_CLIENT_TOKEN'] %>',
        applicationId: '<%= ENV['DD_APPLICATION_ID'] %>',
        site: 'datadoghq.com',
        service: 'storedog-app',
        env: '<%= ENV['DD_ENV'] %>',
        version: '1.0.0',
        sessionSampleRate: 100,
        sessionReplaySampleRate: 20,
        trackUserInteractions: true,
        trackResources: true,
        trackLongTasks: true,
        allowedTracingUrls: [/http:\/\/\d+\.\d+\.\d+\.\d+/],
        defaultPrivacyLevel: 'mask-user-input'
      });
    </script>
```

Perceba que os campos ```clientToken```, ```applicationId```, ```site```, ```service``` e ```version``` são passados por meio de variáveis de ambiente.

**Atenção:** para o parâmetro ```allowedTracingUrls``` que determina a origem das requisições que serão integradas de ponta a ponta entre RUM e APM.

O arquivos que receberá esse código é o ***spree_application.html.erb*** que fica dentro do diretório ***./app/views/spree/layouts/*** do rails. Dito isso para atualizarmos a nossa imagem, vamos rebuilda-la ja copiando esse arquivo alterado para o diretório da imagem.

Então, acesso o diretório ***Dia 3/frontend*** que há um ***Dockerfile*** e um ***spree_application.html.erb*** já modificado. Agora é só builda:
```bash
docker image build --progress=plain --tag salomaosan/frontend:v0.3-rum .
```

Agora com a imagem preparada antes de atualizar a aplicação, passe as veriáveis no ***frontend.yaml***.
```yaml
- name: DD_CLIENT_TOKEN
  value: valor_informado_pela_Datadog
- name: DD_APPLICATION_ID
  value: valor_informado_pela_Datadog
```

Com o ajuste feito, atualize o ```frontend```
```bash
kubectl apply spree-ecommerce/
```

## Synthetic Monitoring

O conteúde de sintético é todo configurado na UI do Datadog, assista a maratona.

### FIM DA DATADOG WEEK