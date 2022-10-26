# Node.js: Utilizando filas de tarefas assíncronas com Bull+Redis

Created: December 22, 2021 8:24 AM
Finished: No
Source: https://higher-order-programmer.medium.com/node-js-utilizando-filas-de-tarefas-ass%C3%ADncronas-com-bull-redis-71540f4be6ec
Tags: #nodejs

Caro dev, a intenção desse post é mostrar passo a passo como implementar
 filas de tarefas assíncronas com a lib Bull e a gravação de seus logs no banco
 NoSQL [Redis](https://redis.io/) com Node.js.

Obs: *Será apresentada apenas uma forma de implementação, a que melhor funcionou pra mim*.

(Caso já queira acessar o repositório: [https://github.com/prog-lessons/jobs-queue-redis](https://github.com/prog-lessons/jobs-queue-redis)).

# Cenário exemplo

Um funcionário foi contratado e o sistema executa as tarefas: **1**) Envia e-mail do RH para ele. **2**) Envia e-mail para o lider da equipe, formalizando. **3**) Faz a persistencia dos dados do funcionário num txt. Teremos duas filas; uma para os jobs de envio de e-mails (*MailJobsQueue*) e outra para persistencia em arquivos (*PersistenceJobsQueue*). E dois “modelos” de jobs (*MailJob* e *FilePersistenceJob*), permitindo haver ***n*** jobs de determinado modelo vinculado a uma determinada fila. O gatilho para esse processo será disparado por meio de uma web API.

# Ambiente

Primeiro, vamos subir o Redis num container docker.

```
docker pull redis
docker images
docker run --name redis -p 6379:6379 -d -t 84c5f6e03bf0
```

Inicie o projeto com **npm init** no diretório desejado, aqui dei o nome*background-jobs-queue-redis*. Após responder as perguntas iniciais, será
 gerado o arquivo package.json.

Adicione os seguintes pacotes ao projeto:

Adicione “start”, “queue” aos scripts em package.json:

![Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1YBYXyo7NVayD2WvAyNldVQ.png](Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1YBYXyo7NVayD2WvAyNldVQ.png)

***Utils:***

- Para testar o envio dos e-mails (lib Nodemailer), uso oserviço [Mailtrap](https://mailtrap.io/). Ao criar uma conta, serão fornecidas instruções de uso.

![Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1bZemAR1P3ab2u8vrw-Gw6Q.png](Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1bZemAR1P3ab2u8vrw-Gw6Q.png)

# Iniciando

Abra a pasta do projeto com editor de sua preferência (aqui uso o VS Code).

Crie o arquivo nodemon.json, o qual indicará ao nodemon, que os fontes JS
 do projeto serão executados com sucrase-node e não diretamente com o
 executavel do node.

Depois, o arquivo .env, que irá conter as variaveis de ambiente que serão
 usadas nos fontes JS do projeto.

![Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1J2QqpuY05JgSgow2-gRQFA.png](Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1J2QqpuY05JgSgow2-gRQFA.png)

![Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1wNNL8RXOGuur0MaL4SmcEA.png](Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1wNNL8RXOGuur0MaL4SmcEA.png)

Estrutura de arquivos do projeto

## *src/config*

Esses fontes apenas exportam objetos literais com propriedades de
 configuração para envio dos e-mails e conexão com o Redis.

![Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1ijBkXNLZVSYN1D3weVml_A.png](Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1ijBkXNLZVSYN1D3weVml_A.png)

# Definindo as filas

## src/app/queues

Aqui, cada fonte JS corresponde a uma fila da aplicação. Apenas
 exportam objetos literais com o nome da fila e opções de configuração.

![Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1MmvnZRLcaihRprCzdbA5HA.png](Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1MmvnZRLcaihRprCzdbA5HA.png)

Index.js exporta um objeto literal, sendo suas propriedades Q1, Q2, [objetos
 aninhados](https://www.tutorialspoint.com/how-to-access-nested-json-objects-in-javascript) contendo as propriedades *[name]*, *[options]* do fonte associado.

# Definindo modelos de jobs

## src/app/job-models

Aqui, cada fonte JS descreve um “modelo” de job, o qual é vinculado à
 uma fila. A função *handle()* será passada como argumento ([callback function](https://developer.mozilla.org/en-US/docs/Glossary/Callback_function)) para o método *process()* do Bull (interface *Queue*), que apenas registra a função que deve ser executada quando entrarem novos jobs em determinada fila. No caso de *MailJob.js*, *handle()* foi declarada como assíncrona, pois nao sabemos quanto tempo o servidor de e-mails levará pra responder, concluindo a tarefa (linha 11), então, enquanto isso, libera a aplicação pra continuar executando. Significa que a função *handle()* fica suspensa/pausada, ou seja, a execução volta para a próxima linha de onde *handle()* foi chamada. Quando o método *sendMail()* estiver concluído, o fluxo de execução volta imediatamente para dentro de *handle(), n*a próxima linha apoś **await** (linha 12).

O conteúdo do parâmetro *data* é passado pelo Bull quando *handle()* for
 invocada. Repare que *data* está entre {}, bem como a variavel *emailData*. Esse é o conceito de [desestruturação](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) do JS.

![Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1HmJJ6-HSDv-Rx2mBZbYQzw.png](Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1HmJJ6-HSDv-Rx2mBZbYQzw.png)

MailJob.js

# Componentes base da aplicação

## *src/app/lib*

***GlobalDefs.js***: Define tipos de jobs, algo como o enum em outras linguagens.

![Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1MGfcP4JDWQMv_1WWHJ8n5A.png](Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1MGfcP4JDWQMv_1WWHJ8n5A.png)

***Mail.js***: Exporta um objeto da classe *Mail* (lib nodemailer) retornado por*createTransport(),* que terá seu método *sendMail()* invocado em *src/app/job-models/MailJob.js*:11.

![Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1L5Al-v0csZAkOTgaOQAugw.png](Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1L5Al-v0csZAkOTgaOQAugw.png)

***Queue.js***: Esse é, possivelmente, o fonte mais importante do projeto, onde a
 coisa realmente acontece.

![Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1glYK-Kg3Wb6L1RLQ4GVoIA.png](Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1glYK-Kg3Wb6L1RLQ4GVoIA.png)

Na linha 4 é importado um objeto literal (*queues*) contendo todas as filas e na linha 5 (*jobs*) com todos os modelos de jobs.

Na linha 7, *[Object.values(](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/values)queues)* retorna um array de objetos, onde cada
 elemento corresponde a (*Q1*, *Q2*, …).

![Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1KTepdmMtKLrn8IBetQTVNw.png](Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1KTepdmMtKLrn8IBetQTVNw.png)

console.table(Object.Values(queues))

O método *[map()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)* de *[Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)* em JS tem como parâmetro uma callback function, que é executada sobre cada elemento do array, retornando um novo array.

Na linha 7, *map()* recebe uma [expressão lambda com arrow function](https://www.vinta.com.br/blog/2015/javascript-lambda-and-arrow-functions/) como argumento, adicionando pra cada elemento do array, uma nova propriedade *[bull]*, que contém uma instancia(objeto) de *Queue*. Esse objeto controlará a adição de jobs às filas e seus processamentos. A opção ***stalledInterval: 0*** é usada neste exemplo, pois nenhum job manterá a CPU muito ocupada ([Bull](https://optimalbits.github.io/bull/), seção “Stalled jobs”).

***addJob(**type**,** data**)**:* Como está nos comentários, basicamente busca o modelo de job (*job*) em *AllJobs* pelo tipo (*type)* e, uma vez localizado, busca em *AllQueues* a fila (*q*), tal que *q.bull.name* === *job.queue*. Obtida *q*, adiciona-lhe os dados referentes ao job (*data*) e as opções de execução para esse job (*job.options*).

***process()***: Percorre todos os modelos de jobs e, para cada um, identifica qual a fila vinculada e faz o mapeamento entre ela e a função que deve ser executada para seus jobs.

# REST API

## src/app/controllers

Aqui ficam os controladores das APIs. Esses fontes contém funções/manipuladores (handlers) dos dados enviados pela requisição HTTP e devolvem o resultado (geralmente um JSON). Aqui poderíamos considerar o [endpoint](https://stackoverflow.com/questions/29734121/what-is-endpoint-in-rest-architecture) *http://localhost:8080/users* uma [Web API](https://en.wikipedia.org/wiki/Web_API).

***UserController.js***: Exporta a função *store(req, res)* que irá tratar as requisições referentes ao recurso¹ ***users***. [*req.body*] contém os campos/valores que foram enviados e [*res*] é para devolver a resposta ao cliente.

![Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1rifRVQr_XX2vB687oY14xQ.png](Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1rifRVQr_XX2vB687oY14xQ.png)

[1] “Toda aplicação gerencia algumas informações. Uma aplicação de um E-commerce, por exemplo, gerencia seus produtos, clientes, vendas, etc. Essas **coisas** que uma aplicação gerencia são chamadas de **recursos** no modelo REST.” (**[REST: Princípios e boas práticas](https://blog.caelum.com.br/rest-principios-e-boas-praticas/amp/)**, **“**Identificação dos Recursos”)

# Pontos de entrada

A aplicação será executada a partir de 2 fontes: *server.js* e *queue.js*. É interessante essa implementação que separa a execução em 2 processos. Suponha que o processo que adiciona jobs às filas tenha algum problema em algum ponto e aborte. Voce poderá solucionar o problema e reinicia-lo, enquanto o processo que efetivamente executa os jobs continua no ar.

A linha 6 é necessária para que aplicação possa trabalhar com dados enviados com método POST (ou PUT) no formato JSON.

Na linha 8, *store()* tratará as requisições HTTP com método POST para a rota ‘/users’.

Na linha 10 é onde o servidor web é levantado, na porta passada como argumento para *listen()*.

# Executando

Inicie os 2 scripts.

Abra o Postman (ou app de preferencia) e envie a requisição HTTP (método POST) com os dados do corpo da mensagem no formato JSON para a URL (*http://localhost:8080/users*).

![Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1kiDUrpSVcIEFA9rJwLF1yQ.png](Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1kiDUrpSVcIEFA9rJwLF1yQ.png)

Confirme os dados de resposta e o STATUS 200 (OK).

![Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/15pZLawq13dXq7jE3QLewvA.png](Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/15pZLawq13dXq7jE3QLewvA.png)

No site do Mailtrap, veja que os e-mails foram enviados.

![Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1HS_OWVBlRAaDaXh94Wfxmg.png](Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1HS_OWVBlRAaDaXh94Wfxmg.png)

## Verificando logs dos jobs (Redis)

Acesse o cliente do Redis conforme comando na imagem. Digite o comando *keys ** para listar todas chaves gravadas.

![Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1aXOFPdtR4Omt6v2Z_QcUYw.png](Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/1aXOFPdtR4Omt6v2Z_QcUYw.png)

Foram completados com sucesso os 2 jobs da fila de envio de e-mails e o job de persistencia em arquivo texto.

Para maiores detalhes sobre algum job especifico digite comando *HGETALL <chave>*.

![Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/17oPOVB__dmaDhMRk8Nl2ew.png](Node%20js%20Utilizando%20filas%20de%20tarefas%20assi%CC%81ncronas%20c%20535237be8d054fc5a19583450ea5d009/17oPOVB__dmaDhMRk8Nl2ew.png)

## Persistência em txt