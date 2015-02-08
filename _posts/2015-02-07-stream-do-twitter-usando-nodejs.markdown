---
layout: post
title:  "Stream do Twitter com node.js"
date:   2015-02-07 16:58:55
categories: nodejs, apis, stream
---
O objetivo deste post é ensinar como fazer a stream da timeline de um determinado usuário do twitter, usando node.js. Para isto, nós usaremos a biblioteca **[ntwitter](https://www.npmjs.com/package/ntwitter)**, além de ter a necessidade de um [app registrado no twitter](http://apps.twitter.com) para termos acesso aos tokens.

### Instalando as dependências
{% highlight bash %}
npm init
npm install ntwitter --save
{% endhighlight %}

**OBS:** Usei a opção **--save** para adicionar a dependência ntwitter no nosso *package.json*

### Entendendo a mágica
Usando a lib **ntwitter** tudo fica muito simples, como mágica, mas precisamos entender o que ela está fazendo.

A lib está usando a API [User Stream](https://dev.twitter.com/streaming/userstreams) a qual nos concede o poder de trabalhar com os dados e eventos de um usuário específico.

#### TL:DR;

Apesar a User Stream ser bem semelhante com a Site Stream, existe uma diferença: Nós queremos pegar a timeline de um usuário específico, "Many-To-One". Se estivéssemos em uma relação "Many-To-Many", vários usuários pegando os dados de suas respectivas contas, usaríamos a Site Stream.

### Developando

Precisamos chamar a a lib e setar nossas variáveis, o que pode ser facilmente feito:
{% highlight javascript linenos %}
var twitter = require('ntwitter');

var consumer_key = "";
var consumer_secret = "";
var access_token_key = "";
var access_token_secret = "";

var t = new twitter({
  consumer_key: consumer_key
  , consumer_secret: consumer_secret
  , access_token_key: access_token_key
  , access_token_secret: access_token_secret
});

{% endhighlight %}

O access_token_key e access_token_secret você irá conseguir somente se tiver o seu aplicativo registrado no twiiter. Já as consumer_key e consumer_scecret, pertence a cada usuário, você pode usar as mesmas da conta que você usou para registrar o aplicativo.

Usaremos a função stream na biblioteca ntwitter para podemos ter acesso a API do twiiter, sendo a chamada composta por:
{% highlight javascript linenos %}
t.stream (API, track, callback)
{% endhighlight %}

* API corresponde as rotas disponibilizadas pelo twitter, como GET users, GET sites e várias outras, disponibilizadas na documentação do twitter.
* Track corresponde ao que queremos pegar: Usuários, tweets de uma determinada hashtag, posts em uma região (usando latitude e longitude).

A função para pegar o stream ficará:

{% highlight javascript linenos %}
t.stream('user'
  , { track: 'dev_trend' }
  , function(stream){
    stream.on('data', function(tweet){
      // Quando você "curte" um comentário, ele retorna o texto como undefined
      console.log(tweet.text);
    });
  }
);
{% endhighlight %}

Para ver a mágica acontecer, basta usar **node nomeDoArquivo.js** e você verá no console todos os tweets da sua timeline. 
O resultado será:
![](https://dl-web.dropbox.com/get/Public/teste%20stream%20nodejs.png?_subject_uid=96668646&w=AABjdrU-feeZeFfLN4l5A738ocvFj1o3m7jqJusCpUOnHg)

#### TL:DR;
O objeto tweet é um JSON contendo todas as informações a respeito do usuário que postou, do usuário que está vendo e informações acerca do tweet. Ele possui vários dados interessantes. Vale a pena 'brincar' com ele.

### Talk is cheap, show me the code.

{% gist lukaswilkeer/d0926e084106d0117de8 stream.js %}

It's all folks!

---------------------------------

Este post faz parte de uma série contanto cada passo que eu darei no desenvolvimento de um app de curadoria de conteúdo usando machine learning e mineração de dados.