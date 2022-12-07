## AWS Greengrass Core SDK para JavaScript

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

Sua operação _console.log_ será registrada como INFO. Uma operação _console.error_ será registrada como ERROR. Currently, our NodeJS SDK only allows you to log at INFO or ERROR level only.

## Supported Datatypes

From GGC version 1.5, you can send both JSON and binary data as a payload when you invoking other Lambdas or publishing a message using IotData service. In order to make your lambda be able to handle binary payload, you need to configure the lambda in Greengrass console to mark it using binary input payload so that GGC can know how to deal with the data.

## Supported Context

In Greengrass, you can send a context object in a JSON format to be passed to another Lambda that is being invoked. The context format looks like this: `{ custom: { customData: 'customData', }, }`

<div class="section" id="compatibility">

## Compatibility[¶](#compatibility "Permalink to this headline")

As new features are added to AWS IoT Greengrass, newer versions of the AWS IoT Greengrass SDK may be incompatible with older versions of the AWS IoT Greengrass core. The following table lists the compatible SDKs for all GGC releases.


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

## 1.7.0 Updates[¶](#1.7.0updates "Permalink to this headline")

Stream manager supports automatic data export to AWS S3 and AWS IoT SiteWise, provides new API method to update existing streams, and pause or resume exporting.

</div>

<div class="Section" id="1.6.0updates">

## 1.6.0 Updates[¶](#1.6.0updates "Permalink to this headline")

Added support for StreamManager, see [AWS Docs](https://docs.aws.amazon.com/greengrass/latest/developerguide/stream-manager.html)
for more information.

### Compatibility

StreamManager has adds a new dependency to this package, `cbor==5.0.1`. 
Please make sure to include it in your lambda or else the SDK will not function.
In addition to the new dependency, the 1.6.0 version of this SDK now requires NodeJS version 12
or greater since NodeJS 6 and 8 are end-of-life. See [https://docs.aws.amazon.com/lambda/latest/dg/runtime-support-policy.html](https://docs.aws.amazon.com/lambda/latest/dg/runtime-support-policy.html).

### StreamManager Usage

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

## 1.5.0 Updates[¶](#1.5.0updates "Permalink to this headline")

Added support for publish() parameter queueFullPolicy which can be set to 'AllOrError' to enforce that the published message is either delivered to all subscription destinations or delivered to no destinations and returns an error when Greengrass Core's internal work queue is full.

</div>

<div class="Section" id="1.4.0updates">

## 1.4.0 Updates[¶](#1.4.0updates "Permalink to this headline")

Added support for Node.js 8.10 Lambda runtime. Lambda functions that use Node.js 8.10 runtime can now run on an AWS IoT Greengrass core. (Existing Lambda functions that use Node.js 6.10 runtime can still run on Greengrass core, but they can't be updated after 5/30/2019. Please refer to [AWS Lambda Runtimes Support Policy](https://docs.aws.amazon.com/lambda/latest/dg/runtime-support-policy.html).)

</div>

<div class="Section" id="1.3.1updates">

## 1.3.1 Updates[¶](#1.3.1updates "Permalink to this headline")

Improved log level granularity.

</div>

<div class="Section" id="1.3.0updates">

## 1.3.0 Updates[¶](#1.3.0updates "Permalink to this headline")

SDK supports SecretsManager client.

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

## 1.0.1 Updates[¶](#1.0.1updates "Permalink to this headline")

Shadow operations were not receiving responses from the local shadow properly. This has been fixed.

Lambda Invoke function's InvocationType's default value was Event. This has been changed to RequestResponse.

</div>

## Getting Help

*   [Ask on a Greengrass forum](https://forums.aws.amazon.com/forum.jspa?forumID=254)

## License

Apache 2.0
