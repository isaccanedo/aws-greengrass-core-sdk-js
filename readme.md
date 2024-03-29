## SDK principal do AWS Greengrass para JavaScript

O **AWS Greengrass Core SDK para JavaScript** permite que os desenvolvedores escrevam funções JavaScript do Lambda que serão executadas no Greengrass.

## Visão geral

Este documento fornece instruções para preparar seu ambiente Greengrass Core para executar funções do Lambda escritas em JavaScript. Ele também inclui exemplos de como desenvolver uma função Lambda em JavaScript, bem como empacotar e executar um exemplo de arquivo Hello World em JavaScript para seu núcleo Greengrass.

## Preparando seu Greengrass para executar funções NodeJS Lambda

O ambiente em que o Greengrass está sendo executado precisa ser capaz de executar aplicativos NodeJS 12.x.

*   Instale o NodeJS 12 para sua plataforma. Você pode baixar o NodeJS mais recente em [https://nodejs.org/en/download/](https://nodejs.org/en/download/).
*   Ao descompactar o pacote baixado do site NodeJS, você encontrará o arquivo `node` no diretório `bin`.
*   Copie o arquivo para _**/usr/bin**_ ou _**/usr/local/bin**_ folder.
*   Renomeie o arquivo para _**nodejs12.x**_

## Primeiros passos - Olá, mundo

*   Copie a pasta `greengrassExamples/HelloWorld` para o seu espaço de trabalho.
*   Crie uma pasta `node_modules` na pasta `HelloWorld`.
*   Descompacte aws-greengrass-core-sdk-js.zip na pasta. Ele deve criar uma pasta HelloWorld/node_modules/aws-greengrass-core-sdk
*   Use o NPM para instalar a dependência necessária, cbor. `npm i cbor@5.0.1`.
*   Compacte o conteúdo da pasta HelloWorld para que o index.js fique no topo da estrutura do arquivo zip.
*   Acesse o console AWS Lambda.
*   Criar uma nova função.
*   Escolha o tempo de execução como `Node.js 12.x`
*   Carregue o arquivo zip na seção _Lambda function code_.
*   O manipulador é _index.handler_
*   Escolha qualquer função, pois a função não é usada no Greengrass.
*   Depois de criar a função, publique o Lambda.
*   Crie um Alias e aponte para a versão Publicada (não $LATEST).
*   Vá para o seu Grupo Greengrass e adicione o Lambda na seção Lambdas.
*   Clique no Lambda e altere o _Lambda lifecycle_ para _Tornar esta função de longa duração e mantê-la em execução indefinidamente._
*   Adicione uma assinatura com a seguinte configuração:
    *   Source: Lambda que você acabou de criar e adicionar ao grupo
    *   Target: nuvem IoT
    *   Topic: hello/world
*   Implantar. Uma mensagem do seu Lambda deve ser publicada no tópico _hello/world_ na nuvem a cada 5 segundos. Você pode verificar isso acessando a página _Test_ do AWS IoT e inscrevendo-se no tópico _hello/world_.

## Incluindo aws-greengrass-core-sdk com sua função

Descompacte o SDK na pasta node_modules da sua função. Isso deve criar uma pasta aws-greengrass-core-sdk que inclui o SDK.

## Fazendo login no NodeJS Lambdas

Sua operação _console.log_ será registrada como INFO. Uma operação _console.error_ será registrada como ERROR. Atualmente, nosso SDK NodeJS permite que você faça login apenas no nível INFO ou ERROR.

## Tipos de dados suportados

A partir da versão 1.5 do GGC, você pode enviar dados JSON e binários como carga útil ao invocar outros Lambdas ou publicar uma mensagem usando o serviço IoTData. Para fazer com que seu lambda seja capaz de lidar com carga binária, você precisa configurar o lambda no console do Greengrass para marcá-lo usando carga útil de entrada binária para que o GGC saiba como lidar com os dados.

## Contexto suportado

No Greengrass, você pode enviar um objeto de contexto em formato JSON para ser passado para outro Lambda que está sendo invocado. O formato de contexto se parece com isso: `{ custom: { customData: 'customData', }, }`

<div class="section" id="compatibility">

## Compatibilidade[¶](#compatibilidade "Link permanente para este título")

À medida que novos recursos são adicionados ao AWS IoT Greengrass, as versões mais recentes do SDK do AWS IoT Greengrass podem ser incompatíveis com versões mais antigas do núcleo do AWS IoT Greengrass. A tabela a seguir lista os SDKs compatíveis para todas as versões do GGC.


<table style="width:50%">

<tbody>

<tr>

<th>GGC Version</th>

<th>Compatible SDK Versions</th>

</tr>

<tr>

<td>1.0.x-1.6.x</td>

<td>1.0.x-1.2.x</td>

</tr>

<tr>

<td>1.7.x-1.8.x</td>

<td>1.0.x-1.3.x</td>

</tr>

<tr>

<td>1.9.x</td>

<td>1.0.x-1.4.x</td>

</tr>

<tr>

<td>1.10.x</td>

<td>1.0.x-1.6.x</td>

</tr>

<tr>

<td>1.11.x</td>

<td>1.0.x-1.7.x</td>

</tr>

</tbody>

</table>

</div>

<div class="Section" id="1.7.0updates">

## 1.7.0 Atualizações[¶](#1.7.0updates "Permalink to this headline")

O gerenciador de fluxo oferece suporte à exportação automática de dados para AWS S3 e AWS IoT SiteWise, fornece novo método de API para atualizar fluxos existentes e pausar ou retomar a exportação.
   
</div>

<div class="Section" id="1.6.0updates">

## 1.6.0 Atualizações[¶](#1.6.0updates "Permalink to this headline")

Adicionado suporte para StreamManager, consulte [AWS Docs](https://docs.aws.amazon.com/greengrass/latest/developerguide/stream-manager.html)
Para maiores informações.
   
### Compatibilidade

StreamManager adicionou uma nova dependência a este pacote, `cbor==5.0.1`. 
Certifique-se de incluí-lo em seu lambda ou o SDK não funcionará.
Além da nova dependência, a versão 1.6.0 deste SDK agora requer NodeJS versão 12
ou superior, já que NodeJS 6 e 8 estão em fim de vida. Consulte [https://docs.aws.amazon.com/lambda/latest/dg/runtime-support-policy.html](https://docs.aws.amazon.com/lambda/latest/dg/runtime-support-policy.html).

### Uso do StreamManager

```javascript
const {
    StreamManagerClient
} = require('aws-greengrass-core-sdk').StreamManager;

const c = new StreamManagerClient({
    onConnectCb: async () => {
        try {
            // Let's start with something simple.
            // Just print out all the available stream names on the server 
            console.log(await c.listStreams());
        } finally {
            c.close(); // Always close the client when you're done
        }
    }
});
```

</div>

<div class="Section" id="1.5.0updates">

## 1.5.0 Atualizações[¶](#1.5.0updates "Permalink to this headline")

Adicionado suporte para o parâmetro publish() queueFullPolicy, que pode ser definido como 'AllOrError' para garantir que a mensagem publicada seja entregue a todos os destinos de assinatura ou a nenhum destino e retorne um erro quando a fila de trabalho interna do Greengrass Core estiver cheia.

</div>

<div class="Section" id="1.4.0updates">

## 1.4.0 Atualizações[¶](#1.4.0updates "Permalink to this headline")

Adicionado suporte para Node.js 8.10 Lambda runtime. As funções do Lambda que usam o tempo de execução do Node.js 8.10 agora podem ser executadas em um núcleo do AWS IoT Greengrass. (As funções existentes do Lambda que usam o tempo de execução do Node.js 6.10 ainda podem ser executadas no núcleo do Greengrass, mas não podem ser atualizadas após 30/05/2019. Consulte a [Política de suporte do AWS Lambda Runtimes](https://docs.aws.amazon.com/lambda/latest/dg/runtime-support-policy.html).)

</div>

<div class="Section" id="1.3.1updates">

## 1.3.1 Atualizações[¶](#1.3.1updates "Permalink to this headline")

Granularidade de nível de log aprimorada.

</div>

<div class="Section" id="1.3.0updates">

## 1.3.0 Updates[¶](#1.3.0updates "Permalink to this headline")

O SDK oferece suporte ao cliente SecretsManager.

</div>

<div class="Section" id="1.2.0updates">

## 1.2.0 Updates[¶](#1.2.0updates "Permalink to this headline")

SDK and GGC compatibility check takes place in the background.

</div>

<div class="Section" id="1.1.0updates">

## 1.1.0 Updates[¶](#1.1.0updates "Permalink to this headline")

Lambda only accepted payload in JSON format. With this update, Invoking or publishing binary payload to a lambda is supported.

</div>

<div class="Section" id="1.0.1updates">

## 1.0.1 Atualizações[¶](#1.0.1updates "Permalink to this headline")

As operações de sombra não estavam recebendo respostas da sombra local corretamente. Isso foi corrigido.

O valor padrão de InvocationType da função Lambda Invoke era Event. Isso foi alterado para RequestResponse.

</div>

## Obtendo ajuda

*   [Pergunte em um fórum Greengrass](https://forums.aws.amazon.com/forum.jspa?forumID=254)

## Licença

Apache 2.0
