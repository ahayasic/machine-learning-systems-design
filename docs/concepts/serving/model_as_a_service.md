# Model as a Service (MaaS)

## Introdução

A estratégia Model-as-a-Service é a mais adotada para servir modelos de ML. Nela, o modelo é abstraído em um serviço (tipicamente web) que recebe requisições de inferência. Após executada as inferências, o serviço retorna os resultados ao requerente.

Dependendo da implementação, o serviço pode receber tanto batches de dados para inferência, quanto entradas individuais.

![maas-generic](https://raw.githubusercontent.com/ahayasic/machine-learning-systems-design/main/docs/assets/serving/maas-generic.png)
<p class="post__img_legend">
  <b>Imagem:</b> Exemplo genérico da arquitetura Model-as-a-Service, onde um dispositivo faz requisições de inferência através de uma interface e então recebe como resposta o resultado da predição.
</p>

!!! info "Sobre Serviços"

    No contexto de arquitetura de software, um serviço é uma unidade de software auto-contida responsável por executar uma tarefa específica. Um serviço deve conter todo o código e dados necessários para executar sua tarefa, sendo implantando (geralmente) em um ambiente totalmente dedicado para si. Demais componentes do software (ou arquitetura) interagem com o serviço através de uma API definida sobre protocolos de comunicação de rede, tais como REST APIs e HTTP/HTTPS.

    O propósito principal de um serviço é fornecer, ao sistema, acesso a um conjunto de funcionalidades, de modo que o serviço provedor seja totalmente reutilizável e independente do resto do sistema. Com isso, podemos desenvolver, construir e implantar o serviço de forma totalmente desacoplada dos demais componentes.


## Vantagens x Desvantagens

Model as as Service é uma estratégia adequada para a maioria das situações. A principal ressalva é que inferências só estarão disponíveis caso o usuário esteja online.

### Vantagens

As principais vantagens de se implantar modelos de ML como serviços são:

- Integração com o restante do sistema, tecnologias e processos extremamente simplificada.
- Gerenciamento do modelo simplificado.

### Desvantagens

Já os contras são:

- Necessário mais aplicações para gerenciar.
- Não é possível realizar inferências offline.
- Latência de inferência consideravelmente maior quanto comparado com inferências offline, uma vez que os dados precisam ser enviados pela rede.
- Dados sensíveis do usuário são enviados pela rede e executados em um domínio externo ao dele.

### Arquiteturas baseadas em Máquinas Virtuais e Containers

Partindo de uma perspectiva de escalabilidade, podemos implantar os serviços de predição de duas formas principais: máquinas virtuais ou containers.

#### Máquinas Virtuais
Com máquinas virtuais (e.g. instâncias AWS EC2), usamos uma ou mais instâncias onde o serviço web roda em paralelo (no caso de mais de uma instância).

A necessidade de diversas instâncias se dá quando há um grande volume de requisições a ser atendido. Neste caso, também incluímos um load balancer que irá receber as requisições e redirecioná-las para a instância com maior disponibilidade.

Note, entretanto, que a necessidade de virtualização prejudica consideravelmente a eficiência de uso dos recursos de cada instância.

![maas-virtual-instances](https://raw.githubusercontent.com/ahayasic/machine-learning-systems-design/main/docs/assets/serving/maas-virtual-instances.png)
<p class="post__img_legend">
  <b>Fonte:</b> <a target="_blank" href="http://www.mlebook.com/">Machine Learning Engineering by Andriy Burkov (2020)</a>
</p>

#### Containers
Diferente de máquinas virtuais, containers são consideravelmente mais eficientes no uso de recursos, tornando os gastos menores sem perda de desempenho (onde desempenho significa atender uma alta demanda de requisições).

Dessa forma, podemos usar um orquestrador de containers como o Kubernetes para gerenciar um conjunto de containers executando em uma ou mais máquinas dentro de um cluster auto-escalável. Com essa estratégia, podemos reduzir o número de réplicas (ou seja, containers ativos em paralelo) para zero, quando não houver qualquer requisição e aumentar para um número suficientemente grande quando houver um grande volume de requisições.

No geral, a implantação de serviços de predição em containers é a mais indicada.

![maas-containers](https://raw.githubusercontent.com/ahayasic/machine-learning-systems-design/main/docs/assets/serving/maas-containers.png)
<p class="post__img_legend">
  <b>Fonte:</b> <a target="_blank" href="http://www.mlebook.com/">Machine Learning Engineering by Andriy Burkov (2020)</a>
</p>

### Protocolos de Comunicação

Assim como em qualquer serviço convencional (aplicações web, banco de dados, etc), no Model as a Service (MaaS) a interação com o modelo acontece através de APIs definidas sobre protocolos de comunicação em rede.

As arquiteturas de API mais comuns são:

- REST (Representational State Transfer) com protocolo HTTP
- gRPC (Google Remote Procedure Call) com HTTP 2.0.

## Exemplo

A fim de solidificar o conhecimento, segue um exemplo prático de serving (ou seria implantação? :grin:) de um modelo como um serviço.

### Model as a Service com FastAPI e Docker

O Python contém diversos pacotes como Flask, FastAPI e Uvicorn que nos permite definir facilmente uma API REST, tal como servidores altamente eficientes.

Para detalhes sobre a implementação de APIs em Python acesse a página de [criação de APIs em Python](#):

Supondo que já temos um modelo treinado (no caso, para a análise de sentimentos), a primeira etapa é definir o endpoint de requisição para inferências e qual método REST será utilizado. Geralmente, nomeamos o endpoint como  ``predict`` ou ``inference``  e usamos o método ``POST`` (uma vez que com ``POST`` podemos definir o corpo da requisição).

Para definir o corpo da requisição no FastAPI, utilizamos a estratégia de definir uma classe ``UserRequest`` derivada da classe ``BaseModel`` de ``pydantic``.

Em seguida, basta chamar o preditor para executar a inferência e então retornamos os resultados.

```python
# my_predictor.py

from fastapi import FastAPI
from textblob import TextBlob

app = FastAPI(title="ML Model as a Service")

class UserRequest(BaseModel):
    sentence: str

@app.post("/predict/")
async def predict(user_request: UserRequest):
    testimonial = TextBlob(user_request.sentence)
    return {
        "result": f"Polarity is {testimonial.sentiment.polarity} and subjectivity is {testimonial.sentiment.subjectivity}"  # type: ignore
    }
```

Para executar as requisições, iniciamos um servidor (com o uvicorn), fazendo a chamada no mesmo nível do arquivo Python onde está definda a API:

```shell
$ uvicorn my_predictor:app --port <port_number>
```

Então acessamos o endereço [`http://127.0.0.1:<port_number>/docs`](http://127.0.0.1:8000/docs) e usamos a documentação de API fornecida pela Swagger UI para enviar requisições.

Ainda, dado que o endpoint defindo é da forma `http://<ip_address>:<port_number>/predict/?data=<data>`, podemos enviar requisições de qualquer forma, incluindo via `curl`

```shell
curl -X 'POST' \
  'http://localhost:8000/predict/' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "sentence": "Life is beautiful, enjoy it!"
}'
```

Podemos também definir o endpoint para receber requisições ``GET``, eliminando a passagem de dados via corpo de requisição (embora isso seja mais uma vantagem do que desvantagem). 

```python
# my_predictor.py

from fastapi import FastAPI
from textblob import TextBlob

app = FastAPI(title="ML Model as a Service")

@app.get("/predict/")
async def predict(sentence):
    testimonial = TextBlob(sentence)
    return {
        "result": f"Polarity is {testimonial.sentiment.polarity} and subjectivity is {testimonial.sentiment.subjectivity}"  # type: ignore
    }
```

Então,  executamos as chamadas via web browser, por exemplo:

```shell
http://localhost:8000/predict/?sentence=%22Life%20is%20beautiful,%20enjoy%20it%22
```

#### Conclusão

Este foi um exemplo prático extremamente simples. No mundo real, há diversas outras considerações que devemos levar em conta durante o serving de um modelo como um serviço, por exemplo:

- Quando um modelo é atualizado, precisamos que isso se reflita no serviço. Contudo, não podemos simplesmente desligar e religar um serviço. Portanto, como atualizar os modelos para serviços em produção?
- É muito possível que existam períodos em que a quantidade de requisição é grande o suficiente para derrubar o serviço, tornando necessário o uso de um load balancer. Quando isso deve acontecer? Como deve ser o load balancer?

Comentários sobre estes problemas serão incluídos futuramente!

??? tip "Interessado em mais exemplos? :eyes:"

    Caso queira ver mais exemplos de MaaS acesse o repositório [ahayasic/model-as-a-service-examples](#). Mas já adianto que não há uma explicação aprofundada para nenhum dos exemplos. Apenas código ¯\\\_(ツ)\_/¯
