# Model as a Service (MaaS)

## Introdução

A maneira mais comum de servir um modelo de ML é como um serviço. Nessa abordagem, o modelo é abstraído em um serviço $-$ totalmente dedicado a servir o modelo $-$ que recebe requisições de inferência onde são passados os dados de entrada. Após executada as inferências, o serviço retorna os resultados ao requerente.

Dependendo da implementação, o serviço pode receber tanto batches de dados para inferência, quanto entradas individuais.

## Serviços e Protocolos de Comunicação

No contexto de arquitetura de software, um serviço é uma unidade de software auto-contida responsável por executar uma tarefa específica. Um serviço deve conter todo o código e dados necessários para executar sua tarefa, sendo implantando (geralmente) em um ambiente totalmente dedicado para si. Demais componentes do software (ou arquitetura) interagem com o serviço através de uma API definida sobre protocolos de comunicação de rede, tais como REST APIs e HTTP/HTTPS.

O propósito principal de um serviço é fornecer, ao sistema, acesso a um conjunto de funcionalidades, de modo que o serviço provedor seja totalmente reutilizável e independente do resto do sistema. Com isso, podemos desenvolver, construir e implantar o serviço de forma totalmente desacoplada dos demais componentes.

As principais vantagens de se implantar modelos de ML como serviços são:

- Integração com o restante do sistema, tecnologias e processos extremamente simplificada.
- Gerenciamento do modelo simplificado.

Já os contras são:

- Necessário mais aplicações e políticas de monitoramento.
- Não é possível realizar inferências offline
- Latência de inferência consideravelmente maior quanto comparado com inferências offline
- Necessário um [load balancer](#) para lidar com uma grande quantidade de requisições.

### Protocolos de Comunicação

Assim como em qualquer serviço convencional (aplicações web, banco de dados, etc), no Model as a Service (Maas) a interação com o modelo acontece através de APIs definidas sobre protocolos de comunicação em rede.

As arquiteturas de API mais comuns são: REST (Representational State Transfer) com protocolo HTTP; gRPC (Google Remote Procedure Call) com HTTP 2.0.

## Ferramentas mais Utilizadas

A implementação de MaaS pode ser feita tanto de forma manual, definindo sua própria API (com Flask ou FasAPI, por exemplo) e demais recursos, quanto através de ferramentas como TensorFlow Serving.

## Exemplos

A fim de solidificar o conhecimento, seguem alguns exemplos práticos de implantação de um modelo como um serviço.

### Model as a Service com FastAPI

Implantar um modelo como um serviço é consideravelmente fácil. O Python contém diversos pacotes como Flask, FastAPI e Uvicorn que nos permite definir facilmente uma API, tal como servidores altamente eficientes.

Para detalhes sobre a implementação de APIs em Python acesse [...]:

Supondo que já temos um modelo (para a análise de sentimentos) treinado, a primeira etapa é definir [...]

### Extra: Streamlit
