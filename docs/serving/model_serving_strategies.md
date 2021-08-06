# Estratégias para Servir Modelos

Ao disponibilizar (ou "servir") um modelo de (ML), **duas considerações principais** devem ser levadas em conta:

- Padrão de Treinamento do Modelo
- Padrão de Inferência do Modelo

A partir destas considerações, torna-se mais fácil definir a forma como o modelo deverá ser "servido".

## Padrões de Treinamento e Inferência

### Padrões de Treinamento do Modelo

Modelos de ML são treinados de duas formas:

- Em Lotes (batches), conhecido como Aprendizado em Lotes (ou Offline Learning).
- Incremental, conhecido como Aprendizado Incremental (Incremental Learning ou Online Learning).

#### Aprendizado em Lotes

No Aprendizado em Lotes, o modelo é treinado em um conjunto de dados pré-existente.

- O retreinamento ocorre apenas depois de um longo período de tempo em produção, no qual foi exposto a novos dados do mundo real e, consequentemente, tornou-se obsoleto.

O principal problema do Aprendizado Offline é a obsolecencia do modelo ao longo do tempo.

> Tal fenômeno é conhecido como *"Model Decay"*

#### Aprendizado Incremental

No Aprendizado Incremental(*), o modelo é treinado regularmente conforme novos dados são disponibilizados à aplicação (e.g. real-time data streams).

- O retreinamento pode ocorrer em um único dado novo ou pequenos grupos de dados, denominados *mini-batches*.

O principal problema do Aprendizado Incremental é que, quando em produção, a entrada de dados ruins (e.g, ruídos) tende a prejudicar consideravalmente o desempenho tanto do modelo quanto de toda a aplicação.

## Padrões de Inferência do Modelo

Modelos de ML podem ser dispostos para inferir instâncias de duas formas: Em Batches ou Sob-demanda.

### Inferência em Batches

As inferências são realizadas sobre um conjunto de dados (históricos).

Abordagem interessante para quando:

- Os dados que não dependem do tempo.
- O tempo de saída para o dado de entrada não é um fator crítico (ou mesmo necessário).

### Inferência em Tempo Real

As inferências são realizadas em tempo-real (e sob-demanda) para cada dado de entrada. Assim, a saída para o dado de entrada também ocorre em tempo real.

Abordagem interessante para quando:

- O tempo de saída para o dado de entrada é um fator crítico.

## Padrões de Model Serving

Com base no padrão de treinamento e inferência do modelo que deve ser colocado em produção, podemos adotar diferentes estratégias de *serving*. Alguns exemplos são:

- *Model-as-a-Service (MaaS)*
- *Model-as-a-Dependency (MaaD)*
- *Precompute Serving Pattern*
- *Model-on-Demand*
- *Hybrid-Serving (Federated Learning)*

### Model-as-a-Service (MaaS)

Padrão (mais comum) onde o modelo de ML é **empacotado como um serviço independente com um servidor web dedicado** a partir do qual as aplicações enviam requisições através de, por exemplo, uma API REST.

- As formas mais tradicionais de se implementar uma arquiteutra MaaS é através de:
  - Microserviços
  - REST API (simples)
  - Serviço gRPC
- Recomendado para qualquer padrão de treinamento e inferência.

###  Model-as-a-Dependecy (MaaD)

Padrão onde o modelo é **empacotado e definido como uma dependência da aplicação**. Assim, a aplicação invoca um método de inferência (passando os parâmetros apropriados) que deve retornar um resultado pelo modelo de ML previamente treinado e empacotado.

- Recomendado para modelos de:
  - Aprendizado Em Lotes
  - Inferência em Batches e Tempo Real

### Pré-Computação

Padrão onde o **modelo previamente treinado pré-executa a realização de inferências sobre um dado conjunto de dados e armazena os resultados em um banco de dados**. Assim, as requisições feitas são respondidas através de consultas no banco de dados.

- Recomendado para modelos de:
  - Aprendizado Em Lotes
  - Inferência em Batches.

#### Leitura Adicional

- https://www.slideshare.net/mikiobraun/bringing-ml-to-production-what-is-missing-amld-2020

### Modelo Sob-Demanda

Padrão onde o modelo de ML também é **tratado como uma dependência que está disponível em tempo de execução (da aplicação)**. Porém, ao contrário do padrão Model-as-Dependency, no Model-on-Demand o **modelo tem seu próprio ciclo de lançamento e é publicado de forma independente**.

- Recomendado para qualquer padrão de treinamento e inferência.

#### Leitura Adicional

- [Event-driven architecture](https://learning.oreilly.com/library/view/software-architecture-patterns/9781491971437/ch02.html)
- [Web services vs. streaming for real-time machine learning endpoints](https://towardsdatascience.com/web-services-vs-streaming-for-real-time-machine-learning-endpoints-c08054e2b18e)

### (Hybrid-Serving) Federated Learning

Hybrid-Serving, mais conhecido como Federated Learning é uma forma relativamente nova, porém, em alta, de servir modelos aos usuários, principalmente de dispositivos móveis.

Basicamente, trata-se de estratégia onde um modelo genérico é disponibilizado para uma grande quantidade de usuários e, então, cada usuário passa a ter um modelo específico para si que é retreinado (ou especializado) em seus dados.

Mais precisamente:

- Há um modelo genérico no lado do servidor (ou server-side) pré-treinado em dados do mundo real.
  - Tal modelo é usado como ponto de partida para novos usuários da aplicação.

- Do lado dos usuários (ou user-side), há modelos especializados e únicos para cada usuário (que partem do modelo genérico no server-side), de forma que o retreinamento (i.e. especialização) destes modelos para este usuário ocorre no dispositivo do usuário.
- Uma vez especializados, os (hiper)parâmetros de cada modelo são enviados para o servidor. Assim, o modelo do servidor é ajustado a fim de que as tendências reais de toda a comunidade de usuários sejam cobertas pelo modelo e, então, este novo modelo passa a ser o novo modelo inicial para todos os usuários.
  - Para que não haja desvantagens aos usuários, o processo de atualização dos modelos ocorre somente quando o aparelho está ocioso, conectado à uma rede WiFi e carregando.
  - Ainda, os testes são feitos nos dispositivos. Portanto, o modelo recém-adotado do servidor é enviado aos dispositivos e testado quanto à funcionalidade.

A principal vantagem dessa abordagem é que:

- Os dados pessoais necessários para o treinamento (e teste) nunca saem do domínio do usuario. Enquanto que ainda assim é possível atualizar os modelos com base nas tendências da comunidade.
  - Em outras palavras, é possível treinar modelos de alta precisão sem ter que armazenar toneladas de dados (provavelmente pessoais) na nuvem.

Porém, a grande desvantagem é que a especialização do modelo é custosa para usuários. Afinal, os modelos de ML são desenvolvidos com conjuntos de dados grandes e homogêneos em um hardware poderoso.

#### Leitura Adicional

- [Three Levels of ML Software](https://ml-ops.org/content/three-levels-of-ml-software#model-machine-learning-pipelines)


## Referências

- [Three Levels of ML Software](https://ml-ops.org/content/three-levels-of-ml-software#model-machine-learning-pipelines)


