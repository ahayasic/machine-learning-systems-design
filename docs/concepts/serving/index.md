# Model Serving

Model serving é uma das etapas do fluxo de criação e produtização de um modelo de machine learning (ML) cujo objetivo é disponibilizar o modelo para uso, em conjunto de estratégias de implantação (deployment).

A forma como um modelo é disponibilizado para a execução de inferências afeta diretamente, tanto a interação do usuário (ou aplicação) com o modelo, quanto seu desempenho e manutenibilidade. Por isso, ao dedicir qual estratégia utilizar, devemos levar em consideração fatores como:

- Padrão de treinamento e inferência (em lotes ou fluxo)
- Disponibilidade offline (possível fazer inferências offline ou apenas online)
- Latência de rede (caso necessário envio de dados)
- Segurança de dados sensíveis
- Padrão de atualização do modelo
- Recursos de computação disponível

De fato, esses são os principais aspectos que nos levam a escolher uma estratégia de *serving*.

!!! note "Serving x Deployment"
    Termos como _model serving_ e _model deployment_ são utilizados de forma intercambiável na interwebs da vida :smile:

    Para tornar as explicações mais claras e consistentes, vamos definir:

      - _Model serving_. **Forma** que um modelo é disponiblizado para inferências
      - _Model deployment_. **Como** a forma que o modelo é disponiblizado é realizada

    Por exemplo, podemos servir um modelo como um serviço encapsulado em um container e cuja implantação (ou _deployment_) se dá através de um processo de CI/CD em *Modo Canário*.

## Padrões de Treinamento e Inferência

Os padrões de treinamento e inferência são fatores fundamentais na decisão de como um modelo deve ser disponibilizado, uma vez que há estratégias de serving que não suportam um determinado padrão (ou combinação de padrões) de treinamento e inferência.

### Padrões de (re)Treinamento de Modelos

Considerando um cenário onde um modelo já está treinado e pronto para produção, a preocupação com seu padrão de treinamento se dá pela necessidade do seu **retreinamento**  Afinal, uma vez que o modelo está em produção, ele é exposto continuamente a novos dados do mundo real (e não apenas a uma amostra estática) e, consequentemente, torna-se obsoleto (fenômeno conhecido como *model decay*). Para que o modelo em produção mantenha um bom desempenho a maior parte do tempo, ele deve ser retreinado.

Modelos de ML são treinados de duas formas:

- Em Lotes (batches), conhecido como Aprendizado em Lotes ou Offline/Batch Learning.
- Incremental, conhecido como Aprendizado Incremental Online Learning.

Logo, o retreinamento também pode acontecer em lotes ou de forma incremental.

- No retreinamento em lotes, um modelo é retreinado após um tempo considerável em produção. Esse retreinamento pode acontecer em intervalos fixos (por exemplo, a cada 30 dias) ou quando um limite inferior de desempenho é atingindo. O principal problema do retreinamento em lotes é que, no caso de um intervalos fixo, a degradação do modelo pode acontecer em diferentes velocidades, o que torna difícil encontrar uma frequência de retreinamento ideal. Por outro lado, monitorar um modelo em produção e retreiná-lo com base em alguma métrica de desempenho, é um processo consideravelmente complexo tanto pela definição e cálculo da métrica em si, quanto pela necessidade de toda uma solução de monitoramento.
- No "aprendizado incremental", o modelo é treinado regularmente conforme novos dados são disponibilizados à aplicação (e.g. real-time data streams). O retreinamento pode ocorrer em um único dado novo ou pequenos grupos de dados, denominados *mini-batches*. O principal problema do Aprendizado Incremental é que, quando em produção, a entrada de dados ruins (e.g, ruídos) tende a prejudicar consideravalmente o desempenho do modelo.

## Padrões de Inferência do Modelo

Da mesma forma que treinados, modelos de ML podem ser dispostos para inferir dados de duas formas: Em Batches ou Sob-demanda.

- Na inferência em batches, o modelo executa predições sobre um "grande" volume de dados de uma vez e só então retorna os resultados.
- Na inferência em tempo real, as predições são executadas sob-demanda para cada dado (ou pequenos conjuntos) de entrada e, em seguida, o resultado já é retornado.

!!! note "O que é grande?"
    Grande é relativo, não é mesmo? De qualquer forma, no contexto de inferência em batches significa que ao invés de executar inferências sob-demanda em pequenos conjuntos de dados (e.g. 1 a ~300 instâncias), executa-se para várias entradas em conjunto (e.g., mais do que 500 ou 10 mil instâncias)

## Padrões de Model Serving

Existem diversas abordagens para servir um modelo, cada um com suas vantagens e desvantagens. Alguns exemplos são:

- Modelo como Parte da Aplicação (Static Deployment)
- Model-as-a-Service (MaaS)
- Model-as-a-Dependency (MaaD)
- Hybrid-Serving (Federated Learning)

### Modelo como Parte da Aplicação (Static Deployment)

Nesta abordagem $-$ que Andriy Burkov chama de Static Deployment $-$, o modelo é empacotado como parte da aplicação que então é instalada através de um arquivo de distribuição de aplicações, como, por exemplo: arquivo binário executável, JAR, APK, DEB, etc.

#### Vantagens

- Não é preciso enviar dados do usuário para um servidor (ou qualquer recurso) externo ao dispositivo do usuário
- O modelo estará sempre disponível, mesmo se o usuário estiver offline (sem conexão com a Internet)
- Caso seja um modelo simples, sem necessidade de computações rápidas ou pesadas, o tempo de inferência é muito mais rápido na abordagem "estático" quando comparado com qualquer outra estratégia

#### Desvantagens

- Para atualizar o modelo é necessário atualizar toda a aplicação (ou seja, reconstruir o arquivo de distribuição da aplicação mesmo que apenas o modelo tenha sofrido alterações)
- Executar o monitoramento de performance do modelo é extremamente difícil
- Se a computação do modelo for cara, executá-la no dispositivo do usuário pode ser ineficiente ou prejudicar a experiência do usuário

### Model-as-a-Dependecy (MaaD)

Padrão onde o modelo é **empacotado e definido como uma dependência da aplicação**. Assim, a aplicação invoca um método de inferência (passando os parâmetros apropriados) que deve retornar um resultado pelo modelo de ML previamente treinado e empacotado.

Mais detalhes e exemplos práticos em [Model-as-a-Dependency](model_as_a_dependency.md).

### Model-as-a-Service (MaaS)

Padrão (mais comum) onde o modelo de ML é **empacotado como um serviço independente com um servidor web dedicado** a partir do qual as aplicações enviam requisições através de, por exemplo, uma API REST.

Mais detalhes e exemplos práticos em [Model-as-a-Service](model_as_a_service.md).

### Serverless Servig/Deployment

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

??? abstract "Leitura Adicional"
    - [Three Levels of ML Software](https://ml-ops.org/content/three-levels-of-ml-software#model-machine-learning-pipelines)


## Referências

- [Three Levels of ML Software](https://ml-ops.org/content/three-levels-of-ml-software#model-machine-learning-pipelines)


