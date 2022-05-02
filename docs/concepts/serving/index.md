# Model Serving

## Introdução

O **model serving** é a etapa do fluxo de criação de um modelo de machine learning (ML) que tem como objetivo disponibilizar o modelo para uso em conjunto de estratégias de implantação (deployment).

A forma como um modelo é disponibilizado para a execução de inferências afeta diretamente, tanto interação do usuário (ou aplicação) com ele, quanto seu desempenho e manutenibilidade. Por isso, ao dedicir qual estratégia utilizar, devemos levar em consideração fatores como:

- Padrão de treinamento e inferência (em lotes ou fluxo)
- Disponibilidade offline (possível fazer inferências offline ou apenas online)
- Latência de rede (caso necessário envio de dados)
- Segurança de dados sensíveis
- Padrão de atualização do modelo
- Recursos de computação disponível

De fato, esses são os principais aspectos que nos levam a escolher uma estratégia de *serving*.

!!! note "Serving x Deployment"
    Termos como _model serving_ e _model deployment_ são utilizados de forma intercambiável na interwebsv:smile:

    Para tornar as explicações mais claras e consistentes, vamos definir:

      - _Model serving_. **Forma** que um modelo é disponiblizado para inferências
      - _Model deployment_. **Como** a forma que o modelo é disponiblizado é realizada

    Por exemplo, podemos servir um modelo como um serviço encapsulado em um container e cuja implantação (ou _deployment_) se dá através de um processo de CI/CD em *Modo Canário*.

## Padrões de Treinamento e Inferência

Os padrões de treinamento e inferência são fatores fundamentais na decisão de como um modelo deve ser disponibilizado, uma vez que há estratégias de serving que não suportam um determinado padrão (ou combinação de padrões) de treinamento e inferência.

### Padrões de (re)Treinamento de Modelos

Considerando um cenário onde um modelo já está treinado e pronto para produção, a preocupação com seu padrão de treinamento se dá pela necessidade do seu **retreinamento**.

Afinal, uma vez que o modelo está em produção, ele é exposto continuamente a dados novos do mundo real (e não apenas a uma amostra estática) e, consequentemente, torna-se obsoleto (fenômeno conhecido como *model decay*). Para que o modelo em produção mantenha um bom desempenho na maior parte do tempo, ele deve ser retreinado com uma certa frequência.

Modelos de ML são treinados de duas formas:

- Em Lotes (batches), conhecido como Aprendizado em Lotes ou Offline/Batch Learning.
- Incremental, conhecido como Aprendizado Incremental Online Learning.

Logo, o retreinamento também pode acontecer em lotes ou de forma incremental.

#### Em Lotes

No retreinamento em lotes, um modelo é retreinado após um tempo considerável em produção. Esse retreinamento pode acontecer em intervalos fixos (por exemplo, a cada 30 dias) ou quando um limite inferior de desempenho é atingindo.

O principal problema do retreinamento em lotes é que, no caso de um intervalos fixo, a degradação do modelo pode acontecer em diferentes velocidades, o que torna difícil encontrar uma frequência de retreinamento ideal.

Por outro lado, monitorar um modelo em produção e retreiná-lo com base em alguma métrica de desempenho, é um processo consideravelmente complexo tanto pela definição e cálculo da métrica em si, quanto pela necessidade de toda uma solução de monitoramento.

#### Aprendizado Incremental

No "aprendizado incremental", o modelo é treinado regularmente conforme novos dados são disponibilizados à aplicação (e.g. real-time data streams). O retreinamento pode ocorrer em um único dado novo ou pequenos grupos de dados, denominados *mini-batches*.

O principal problema do Aprendizado Incremental é que, quando em produção, a entrada de dados ruins (e.g, ruídos) tende a prejudicar consideravalmente o desempenho do modelo.

###Padrões de Inferência do Modelo

Da mesma forma que treinados, modelos de ML podem ser dispostos para inferir dados de duas formas: Em Batches ou Sob-demanda.

- Na inferência em batches, o modelo executa predições sobre um "grande" volume de dados de uma vez e só então retorna os resultados.
- Na inferência em tempo real, as predições são executadas sob-demanda para cada dado (ou pequenos conjuntos) de entrada e, em seguida, o resultado já é retornado.

!!! note "O que é grande?"
    Grande é relativo, não é mesmo? De qualquer forma, no contexto de inferência em batches significa que ao invés de executar inferências sob-demanda em pequenos conjuntos de dados (e.g. 1 a ~300 instâncias), executa-se para várias entradas em conjunto (e.g., mais do que 500 ou 10 mil instâncias)

## Padrões de Model Serving

Existem diversas abordagens para servir um modelo, cada um com suas vantagens e desvantagens. Alguns exemplos são:

- Modelo como Parte da Aplicação (Static Deployment)
- Model-as-a-Dependency (MaaD)
- Model-as-a-Service (MaaS)
- Serverless Servig/Deployment
- Hybrid-Serving (Federated Learning)

## Referências

- [Three Levels of ML Software](https://ml-ops.org/content/three-levels-of-ml-software#model-machine-learning-pipelines)
- [Machine Learning Engineering by Andriy Burkov](http://www.mlebook.com/)
