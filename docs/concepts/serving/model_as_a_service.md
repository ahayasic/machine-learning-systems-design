# Model as a Service (MaaS)

## Introdução

A maneira mais comum de servir um modelo de ML é como um serviço. Nessa abordagem, o modelo é abstraído em um serviço $-$ totalmente dedicado a servir o modelo $-$ que recebe requisições de inferência onde são passados os dados de entrada. Após executada as inferências, o serviço retorna os resultados ao requerente.

Dependendo da implementação, o serviço pode receber tanto batches de dados para inferência, quanto entradas individuais.

!!! info "Sobre Serviços"

    No contexto de arquitetura de software, um serviço é uma unidade de software auto-contida responsável por executar uma tarefa específica. Um serviço deve conter todo o código e dados necessários para executar sua tarefa, sendo implantando (geralmente) em um ambiente totalmente dedicado para si. Demais componentes do software (ou arquitetura) interagem com o serviço através de uma API definida sobre protocolos de comunicação de rede, tais como REST APIs e HTTP/HTTPS.

    O propósito principal de um serviço é fornecer, ao sistema, acesso a um conjunto de funcionalidades, de modo que o serviço provedor seja totalmente reutilizável e independente do resto do sistema. Com isso, podemos desenvolver, construir e implantar o serviço de forma totalmente desacoplada dos demais componentes.

As principais vantagens de se implantar modelos de ML como serviços são:

- Integração com o restante do sistema, tecnologias e processos extremamente simplificada.
- Gerenciamento do modelo simplificado.

Já os contras são:

- Necessário mais aplicações e políticas de monitoramento.
- Não é possível realizar inferências offline
- Latência de inferência consideravelmente maior quanto comparado com inferências offline
- Necessário um [load balancer](#) para lidar com grandes quantidades de requisições simultâneas.

### Protocolos de Comunicação

Assim como em qualquer serviço convencional (aplicações web, banco de dados, etc), no Model as a Service (MaaS) a interação com o modelo acontece através de APIs definidas sobre protocolos de comunicação em rede.

As arquiteturas de API mais comuns são:

- REST (Representational State Transfer) com protocolo HTTP
- gRPC (Google Remote Procedure Call) com HTTP 2.0.

## Ferramentas Utilizadas

A implementação de MaaS pode ser feita tanto de forma manual, definindo sua própria API (com Flask ou FasAPI, por exemplo) e demais recursos, quanto através de ferramentas como TensorFlow Serving.

## Exemplos

A fim de solidificar o conhecimento, seguem um exemplo prático de implantação de um modelo como um serviço.

### Model as a Service com FastAPI

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

## Conclusão

Este foi um exemplo prático extremamente simples. No mundo real, há diversas outras considerações que devemos levar em conta durante o serving de um modelo como um serviço, por exemplo:

- A operação de importar um modelo com milhões de parâmetros é muito custosa. Logo, qual a melhor forma de importar os modelos para serem usados pela API?
- Quando um modelo é atualizado, precisamos que isso se reflita no serviço. Contudo, não podemos simplesmente desligar e religar um serviço. Portanto, como atualizar os modelos para serviços em produção?
- É muito possível que existam períodos em que a quantidade de requisição é grande o suficiente para derrubar o serviço, tornando necessário o uso de um load balancer. Quando isso deve acontecer? Como deve ser o load balancer?

Comentários sobre estes problemas serão incluídos futuramente!
