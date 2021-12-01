# Automação de Pipelines de ML

!!! attention "Attention! Atenção!"
    **(en_US)** This post is a synthesis and translation (into pt_BR) of the post [MLOps: Continuous delivery and automation pipelines in machine learning](https://bit.ly/3tMV9ol) by Google Team.

    **(pt_BR)** Esta publicação é uma síntese e tradução (para pt_BR) do artigo [MLOps: Continuous delivery and automation pipelines in machine learning](https://bit.ly/3tMV9ol) do Google Team.


Ciência de Dados e Aprendizado de Máquina (ML, do inglês Machine Learning) têm-se tornado estratégias prioritárias na resolução de diversos problemas complexos do mundo real.

De fato, implementar e treinar modelos de ML não são tarefas triviais. Contudo,

- O verdadeiro desafio não é construir modelos de ML
- O verdadeiro desafio é construir sistemas de ML integrados e que podem ser atulizados e operados continuamente em produção.

De acordo com o [Sculley et al](https://web.kaust.edu.sa/Faculty/MarcoCanini/classes/CS290E/F19/papers/tech-debt.pdf),

- Apenas uma pequena fração dos sistemas de ML em produção são compostos por códigos referente à ML
- Por outro lado, a infraestrutura é vasta e complexa.

Portanto, para desenvolver e operar sistemas complexos como esses, você pode recorrer aos princípios do DevOps aos sistemas de ML (MLOps).

- MLOps é uma cultura e prática de engenharia de ML que visa unificar o desenvolvimento do sistema de ML (Dev) e a operação do sistema de ML (Ops).
- Praticar MLOps significa que você defende a automação e o monitoramento em todas as etapas da construção do sistema de ML, incluindo integração, teste, releasing, deployment e gerenciamento de infraestrutura.*

Ainda, além das práticas aplicadas à sistemas de software convencionais — uma vez que sistemas de ML também são sistemas de software — sistemas de ML possuem algumas características específicas que exigem novas práticas, como, por exemplo.

- **Desenvolvimento.** ML é experimental por natureza. Logo, é estritamente necessário manter rastreado todos os experimentos feitos, a fim de permitir a identificação do que funcionou, o que não funcionou e porquê, ao mesmo tempo que reprodutibilidade e reutilização de código devem estar presentes.
- **Teste.** Além dos testes executados em sistemas de softwares convencionais (e.g. testes unitários e de integração), em sistemas de ML também são necessários teste e validação tanto de dados quanto de modelos.
- **Deployment.** Em sistemas de ML, o deploy pode requer um alto nível de complexidade que dificulta tanto o desenvolvimento quanto a operação do modelo em produção.
- **Production.**  Em produção, os modelos de ML podem alcançar desempenhos ruins não apenas devido a qualidade de código, mas também a inconsistência dos dados. Assim, o monitoramento do sistema precisa ser muito mais rigoroso e complexo.

## Etapas de Machine Learning
Os níveis de automação destas etapas definem a maturidade do pipeline do sistema de ML.

Em geral, podemos dividir a maturidade de automação em três níveis:

- **Nível \#0.** Não há automação e todos os processos são executados manualmente
- **Nível \#1.** Os pipelines de ML são automatizados. Logo,
    - Os experimentos são orquestrados.
    - Os modelos em produção são continuamente atualizados através de um pipeline de ML automatizado acionado por triggers (que são disparados periodicamente ou com base em um certo conjunto de regras comumente relacionadas ao desempenho do modelo em produção).
    - Etapas de *build*, *test* e *deployment* são executadas manualmente.
- **Nível \#2.** Todo o pipeline, incluindo as etapas de *build*, *test* e *deployment* são automatizadas. Logo,
    - Os experimentos são orquestrados
    - Os modelos em produção são continuamente atualizados através de um pipeline de ML automatizado acionado por triggers (que são disparados periodicamente ou com base em um certo conjunto de regras comumente relacionadas ao desempenho do modelo em produção).
    - Etapas de *build*, *test* e *deployment* são executadas automaticamente através de mecanismos de CI/CD.

## Automação Nível \#0 — Processo Manual
!!! info "Work in progress"

## Automação Nível \#1 — ML Pipeline Automatizado
Um primeiro nível (\#1) de automação consiste em:

- Experimentação orquestrada.
- Executar (ou permitir) o treinamento contínuo (CT, do inglês Continuos Training) do modelo por meio da automação do pipeline de ML em produção.

Para uma automação confiável e de rápida execução é necessário incluir:

- Source Control.
- Orquestrador de Experimentos.
- Validação (automática) de Dados e Modelos.

- ML Metadata Store.
- Feature Store.
- Model Registry.
- Triggers.

### Etapas
Um primeiro nível de automação consiste (em suma) das seguintes etapas:

1. **Experimentação Orquestrada.** Através de um ambiente de experimentações orquestradas, novos algoritmos de ML são experimentados iterativamente.

    A saída desta etapa é o código-fonte do pipeline (ou o modelo de ML resultante do experimento) que é armazenado em um repositório de códigos-fonte *(source control)*.

    !!! note "Nota"
        A experimentação orquestrada pode resultar apenas em um novo modelo ou em um pipeline totalmente novo.

        Porém, mesmo um novo modelo pode ser visto como uma nova versão de um pipeline antigo, assim como alterações em demais componentes (e.g., etapa de extração de dados ou transformações).

        Logo, o que será atualizado em produção pode desde componentes individuais, até pipelines inteiros.

2. **Treinamento Contínuo.** Após a execução manual das etapas de  *build*, *test* e *deployment*, o pipeline (ou modelo) é atualizado em produção, onde é executado automaticamente com base em uma periodicidade pré-definida ou em resposta a um trigger.

    A saída desta etapa é um modelo treinado que é armazenado no Model Registry.

3. Alternativamente, a etapa de CT pode ser executada simplesmente para atualizar o conhecimento do modelo atual em produção. Neste caso,

    - O pipeline é acionado por meio de triggers.
    - Os triggers podem ser disparados através de regras como: desempenho mínimo alcançado.

    A saída desta etapa é um pipeline ou modelo que é armazenado em um Model Registry.

### Características

- **Experimentos Orquestrados.** Todo o experimento, etapas e transições entre as etapas são automatizadas. Assim, cada iteração de experimentação acontece de forma rápida e há maior prontidão para mover todo o pipeline do desenvolvimento para a produção.
- **Treinamento Contínuo (CT) do Modelo em Produção.** O modelo em produção é automaticamente retreinado usando dados atualizados. O (re)treinamento é acionado através triggers.
- **Simetria Experimental-Operacional.** A implementação do pipeline usada no ambiente de desenvolvimento é também usada no ambiente de pré-produção e produção (o que é um aspecto-chave da prática de MLOps para unificar DevOps).

### Componentes

#### Source Control
!!! info "Work in progress"

#### Orquestrador de Experimentos
!!! info "Work in progress"

#### Validação de Dados e Modelos
Os pipelines de ML em produção são disparados por meio de triggers.

Assim, para garantir o funcionamento correto tanto do modelo quanto de todo pipeline:

- É necessário validar os dados que serão utilizados como sinais de entrada para o novo modelo, assim como validar o próprio modelo em si.

Por ser um processo automático, é necessário que a validação do dado e modelo também sejam automáticas.

- **Validação de Dados.** Etapa executada antes do treinamento do modelo  cujo objetivo é decidir se os dados são aptos para o retreinamento do modelo ou não. Alguns exemplos de situações em que os dados não são aptos:
  - **Data Schema Skews.**
  - **Data Values Skews.**
- **Validação de Modelo.** Etapa executada após o retreinamento do modelo com os novos dados. O modelo é avaliado e validado da forma offline tradicional antes de, de fato, entrar em produção, ou seja:

#### ML Metadata Store
!!! info "Work in progress"

#### Feature Store
!!! info "Work in progress"

#### Model Registry
!!! info "Work in progress"

#### Triggers
!!! info "Work in progress"

### Desafios
Se um sistema é composto por uma grande quantidade de pipelines em produção e, alguns modelos precisam entrar rapidamente em produção ou serem testados, o **build, test e deployment pipeline manualmente acaba, se tornando inviáveis.** Assim, é necessário um ambiente de CI/CD.

### Ferramentas para Implementação de Automação Nível \#1

- TensorFlow Extended (TFX).
- Apache Airflow.
- MLFlow.
- Kedro.

## Automação Nível \#2 — CI/CD Pipeline
Um segundo nível (\#2) de automação consiste em:

- Experimentação orquestrada.
- Executar (ou permitir) a atualização rápida e confiável de pipelines em produção através da automação de todo o fluxo de desenvolvimento e operação.
    - Automatização do pipeline de ML (CT).
    - Sistemas de CI/CD.

Para uma automação de nível \#2 é necessário incluir componentes como:

- Source Control.
- Orquestrador de Experimentos.
- Validação (automática) de Dados e Modelos.
- Serviços de Teste e Build.
- Serviços de Deployment.
- ML Metadata Store.
- Feature Store.
- Model Registry.
- Triggers.

### Etapas
Um segundo nível de automação consiste, em suma, das seguintes etapas:

1. **Experimentação Orquestrada.** Através de um ambiente de experimentações orquestradas, novos algoritmos de ML são experimentados iterativamente.

    A saída desta etapa é o código-fonte do pipeline (ou o modelo de ML resultante do experimento) que é armazenado em um repositório de códigos-fonte *(source control)*.

2. **Integração Contínua do Pipeline.** O código-fonte é "buildado" e passa por um grande quantidade de testes unitários e de integração.

    A saída desta etapa são todos os artefatos necessários para o deployment  futuro do pipeline (e.g. pacotes e executáveis).

3. **Entrega Contínua do Pipeline.** Os artefatos produzidos na etapa de integração contínua são disponibilizados (deploy) no ambiente de produção (ou qualquer outro ambiente alvo).

    A saída desta etapa é um pipeline atualizado e disponibilizado (deployed) em produção, com uma nova implementação do modelo.

4. **Treinamento Contínuo.** Em produção, o pipeline é executado automaticamente com base em uma scheduling ou em resposta a um trigger.

    A saída desta etapa é um modelo treinado que é armazenado no Model Registry.

5. **Entrega Contínua do Modelo.** O modelo mais recente (ou melhor ranqueado) do Model Registry é servido como um serviço (ou motor) de inferências.

    A saída deste estágio é um serviço de inferências.

6. **Monitoramento.**  Estatísticas sobre a performance do modelo em produção são coletadas .

    A saída deste estágio é o disparo de um trigger que deve provocar uma nova execução do pipeline em produção ou indicar a necessidade de um novo ciclo de experimentação.

As etapas de análise explotarória de dados e análise de modelo ainda são etapas manuais e que necessitam de um trabalho extensivo por parte do cientista de dados, por exemplo.

### Características

- **Experimentos Orquestrados.** Todo o experimento, etapas e transições entre as etapas são automatizadas.

    Assim, cada iteração de experimentação acontece de forma rápida e há maior prontidão para mover todo o pipeline do desenvolvimento para a produção.

- **Integração e Entrega Contínua do Pipeline.**
- **Treinamento Contínuo (CT) do Modelo em Produção.** O modelo em produção é automaticamente retreinado usando dados atualizados e acionado através triggers.
- **Simetria Experimental-Operacional.** A implementação do pipeline usada no ambiente de desenvolvimento é também usada no ambiente de pré-produção e produção (o que é um aspecto-chave da prática de MLOps para unificar DevOps).
- **Entrega Contínua do Modelo.** *(work in progress)*

### Componentes

#### **Serviços de Teste e Build**
!!! info "Work in progress"

#### **Serviços de Deployment**
!!! info "Work in progress"

### Desafios
Em um segundo nível de automação, praticamente todos os processos são executados automaticamente. Por conta disso, há um grande complexidade envolvida na implementação do sistema.
