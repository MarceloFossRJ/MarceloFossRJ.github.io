---
layout: post
title:  "Rest API style guide"
date:   2017-10-26 18:44:25 -0300
categories: [guide]
tags: [api, rest]
author: Marcelo Foss
---
http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api
https://sensedia.com/blog/apis/design-de-api-rest/
https://sensedia.com/blog/apis/como-medir-a-usabilidade-de-uma-api/
https://www.toptal.com/api-developers/5-golden-rules-for-designing-a-great-web-api
https://geemus.gitbooks.io/http-api-design/content/en/artifacts/provide-machine-readable-json-schema.html
https://github.com/interagent/http-api-design
https://www.thoughtworks.com/insights/blog/rest-api-design-resource-modeling
https://codeplanet.io/principles-good-restful-api-design/


#1 – Utilize substantivos e não verbos
Uma das grandes falhas de padronização ao criar APIs RESTful estão relacionadas ao padrão dos endpoints criados (URLs de acesso aos serviços). O padrão RESTful exige que sejam utilizados substantivos e não verbos ou nomes de métodos. Por exemplo:

```
/getAllCustomers  
/createNewCustomer  
/deleteCustomer  
```
Estas são formas incorretas de nomenclatura, que se assemelham a funções de alguma linguagem de programação orientada a objetos. Ao invés disso, para o Design mais adequado, utilize substantivos, como:

```
/customers
/customers/563
```

#2 – Use corretamente os métodos HTTP
O princípio fundamental do REST consiste em separar sua API em recursos lógicos. Esses recursos são manipulados utilizando requisições HTTP com os métodos GET, POST, PUT e DELETE.

Por exemplo, ao realizar uma requisição ao recurso /customers/563 utilizando o método GET, você estará solicitando que seja recuperado um cliente específico com o código 563.

Da mesma forma, ao realizar a mesma requisição (ou seja, no endpoint /customers/536), utilizando o método DELETE, você estará realizando uma exclusão de um cliente específico, de código 563.

#3 – Utilize nomes no plural
O nome do endpoint deve estar no singular ou no plural?

Esta é uma pergunta que costuma gerar muita discussão no Design de APIs RESTful.

De modo geral, cabe a você escolher. Particularmente, prefiro os nomes no plural, já que indicam um conjunto de recursos (como no caso dos clientes, acima).

Porém, uma coisa é certa: não faça mistura de endpoints no singular e no plural. A recomendação é que você simplifique e utilize todos os nomes no plural.

#4 – Utilize sub-recursos para os relacionamentos
Existem situações em que um recurso está relacionado a outro. É comum quando há uma hierarquia de objetos e recursos.

Por exemplo, se fosse uma API que retorna dados estatísticos sobre a geografia de um país, poderia existir sub-recursos para estados, dentro do país e municípios dentro dos estados.

Quando for o caso, utilize sub-recursos nos endpoints.

Por exemplo, a requisição

```
GET /customers/231/projects/
```
deve retornar a lista de projetos do cliente 231. Já a requisição

```
GET /customers/231/projects/4
```
deve retorna o projeto #4 do cliente 231.

#5 – Não altere estados com o método GET
Ao realizar operações que alteram o estado de um objeto, utilize preferencialmente os métodos POST, PUT e DELETE.

O método GET, como é intuitivo pelo nome, deve retornar apenas uma versão de leitura do recurso.

O HTTP oferece outros métodos para realizar escrita nos recursos, então use-os adequadamente. Nesse ponto, é importante lembrar das permissões e questões de Segurança de APIs, que já falamos nesse Webinar, o que nos leva ao nosso próximo tópico:

#6 – Utilize criptografia SSL
Sua API RESTful deve necessariamente utilizar criptografia SSL.

Como a sua API web pode ser acessada de qualquer lugar onde haja acesso à Internet, como uma praça de alimentação de um shopping center, uma livraria, um café ou um aeroporto, a preocupação em garantir um acesso seguro aos dados e serviços que sua API oferecem é sua.

Porém, nem todos esses locais conferem acesso seguro e criptografado.

Ter as informações que trafegam criptografadas é algo essencial. Além disso, utilizar SSL com tokens facilita a autenticação entre requisições, evitando-se que a cada requisição seja realizada nova autenticação.

Novamente, uma boa referência para estudar esse assunto é o Webinar de Segurança de APIs.

#7 – Crie versões para sua API
Como todo software, APIs devem crescer e evoluir. Por mais cuidadoso e experiente que você seja, sua API não será perfeita na primeira versão.

E nem precisa! Muitas vezes, é melhor expor a primeira versão da sua API e ir evoluindo-a aos poucos. Porém, tome cuidado em não alterar muito o seu design e acabar quebrando as aplicações que usavam as versões anteriores (como o Jeremiah Lee, do Fitbit, disse na entrevista para o Kleber, nosso CEO).

Portanto, ao criar uma nova versão para a sua API, garanta que a versão anterior ainda esteja disponível, não quebrando assim as funcionalidades de sistemas. Depois de comunicar as mudanças aos desenvolvedores, e dar um tempo de adaptação, versões antigas podem ser descontinuadas, ou você pode mantê-las no ar, sem oferecer suporte.

# Garanta o sucesso da sua API RESTful!
Os 7 passsos acima garantirão que o onboarding (começo do uso da sua API, por parte dos desenvolvedores) da sua API seja bem mais fácil!

Uma dica final é você ficar de olho na usabilidade da sua API, com as dicas desse artigo aqui.

E se você curtiu o assunto, e quiser se aprofundar nele, temos o material ideal: o Webinar de Design de APIs RESTful. Confere lá 

