# Introdução ao Projeto de Sistemas de Machine Learning

Projetar um sistema de machine learning (ML) na indústria é um processo altamente iterativo e consideralvemente diferente da academia.

Na academia, (geralmente) estamos preocupados em alcançar o estado-da-arte (SOTA, do inglês state-of-the-art) em algumas tarefas padrões ou conjuntos de dados de **benchmarking**. Assim, é muito comum que os modelos resultantes sejam custosos demais para serem utilizados na indústria.

Modelos com bilhões de parâmetros, por exemplo, são extremamente custosos para treinar e operacionalizar. Dependendo de onde será feita a sua implantação (e.g. dispositivo móvel), o uso de algo tão complexo é inviável.

!!! note "Nota"
    Eventualmente os "big models" vão se tornar menores e mais rápidos. Porém, a diferença de prioridades entre a academia e indústria raramente irá permitir que os métodos SOTA sejam utilizados em produção.

Portanto, temos que ter em mente que ao projetar um sistema de machine learning, nossa prioridade não é alcançar um método estado-da-arte, mas sim um modelo que atenda aos objetivos de negócio, ao mesmo tempo que seja escalável e capaz de cumprir um conjunto restritivo de exigências. Mas, como fazer isso?

O conjunto de práticas e processos eficazes para projetar, construir e implantar modelos de ML em produção ainda está sendo definido. Contudo, já existem diversas abordagens interessantes que podemos aplicar no ciclo de vida de um projeto de ML, como é o caso de [MLOps](mlops/introduction.md). Além disso, por conta da diversidade das etapas do ciclo de vida de projetos de ML, são exigidos processos diferentes em cada momento.

Aqui, vamos abordar as práticas mais comuns aplicadas em cada etapa do ciclo de desenvolvimento de ML que, juntas, formam um primeiro conjunto de estratégias fundamentais para o projeto de sistemas de ML.

## Etapas de um Projeto de ML

O ciclo de vida de um projeto de ML corresponde a todas as etapas de desenvolvimento de um projeto, com foco na resolução de problemas através de ML.

Geralmente dividimos o ciclo de vida de um projeto de ML em quatro etapas principais (que por sua vez, podem ser divididas em mais etapas). São elas: escopo, preparação dos dos, modelagem e implantação (do inglês, deployment).

!!! warning "Not so shallow...\ \ \ :grin:"
    Além de descritas abaixo, cada uma dessas etapas contém seções particulares onde são abordadas com mais detalhes, incluindo estratégias sobre o que fazer em cada momento da etapa e como fazer.

### Escopo

Etapa onde o projeto é definido, qual **problema será atacado**, **como** e qual o **critério de sucesso**.

Visto que os principais objetivos desta etapa são: identificar o que deve ser feito e o quão viável é o que deve ser; é muito importante que o **objetivo do projeto** seja claro e bem definido.

Perguntas que podem nos ajudar a definir o objetivo do projeto são:

- Qual é o problema que queremos resolver usando ML?
- Por que queremos aplicar ML?
- Quais são as entradas que serão consumidas pelo modelo? Quais as saídas que devem ser geradas pelo modelo?
- Quais métricas e critérios definem o sucesso do modelo?
- Quais métricas e critérios definem o sucesso do projeto?

!!! note "Nota"
    O objetivo do modelo não necessariamente precisa ser o mesmo objetivo do ponto de vista de negócios (ou seja, o que o cliente busca alcançar).

    Por exemplo, uma empresa de e-commerce pode ter como objetivo maximizar os lucros com base no preço dos produtos.
    Já o modelo pode ter como objetivo encontrar o preço de um conjunto de produtos que maximize a probabilidade de compra conjunta destes produtos.

Outra prática comum que pode nos ajudar a definir o escopo do projeto com mais rigor (tal como sua execução) é o uso de Canvas, como o [ML Canvas](#).

#### Risco e Impacto do Projeto

Uma vez definido o que deve ser feito, é comum avaliarmos a viabilidade do projeto através de estimativas de riscos e impacto.

Afinal, até o momento não existem métodos de estimação de complexidade de um projeto de ML que são largamente utilizados pela indústria. Ainda, projetos de ML são incertos por natureza dado que nem sempre os recursos necessários para a solução do problema existem ou são viáveis.

Por exemplo, é difícil definir com precisão quais serão os dados necessários, a quantidade de dados necessários, se os modelos existentes na literatura resolvem o problema em questão, etc.

Portanto, o uso de modelos de risco-impacto é uma abordagem segura e efetiva. Um exemplo de modelo de risco-impacto para projetos de ML é o apresentado na figura abaixo.

!!! failure "TODO"
    Incluir Figura

#### Custos do Projeto

O custo de desenvolvimento e operação de um projeto de ML é um fator decisivo na balança de risco e impacto.

De acordo com Andriy Burkov, há três fatores principais que influenciam consideravelmente o custo de um projeto de ML.

- **Dificuldade do problema.** Quanto maior a dificuldade do problema, maior o custo de execução e engenharia. Perguntas que nos ajudam a identificar a dificuldade do problema são:
    - Há empresas que já resolveram o mesmo problema ou semelhante?
    - Há soluções para problemas parecidos na academia?
    - Há implementações disponíveis de algoritmos capazes de resolver o problema em questão?
    - O quanto de poder computacional é necessário para construir e executar o modelo em produção?
- **Custo de aquisição dos dados.** Coletar a quantidade necessária dos dados corretos tende a ser custoso, principalmente se há necessidade de categorização manual dos dados. Perguntas que nos ajudam a identificar o custo de aquisição dos dados são:
    - Quais os dados necessários?
    - Qual a quantidade de dados necesários?
    - Os dados podem ser gerados automaticamente?
    - É necessário categorizar amostras? Se sim, quantas? Qual o custo?
- **Necessidade de assertividade.** Quanto maior a necessidade de assertividade do modelo, maior o custo associado (que crescerá exponencialmente). Afinal, a assertividade de um modelo não depende apenas do modelo em si, mas também dos dados disponíveis (geralmente, quanto mais dados, melhor) e dificuldade do problema.

!!! note "Nota"
    Novamente, aqui o uso de Canvas para ML também é útil tanto para fazermos as perguntas certas quanto encontrarmos as respostas corretas e assim estimarmos custos com maior precisão.

#### Definindo Baselines

Do ponto de vista de um projeto de ML, uma baseline é o desempenho base a partir do qual queremos melhorar. Portanto, antes de começarmos a implementarmos nossos próprios modelos, é importante pesquisarmos soluções já existentes para o problema que queremos atacar (ou então, semelhantes ao problema que queremos atacar).

O [Model Zoo](https://modelzoo.co/), por exemplo, é uma plataforma onde diversos modelos pré-treinados (de deep learning) são disponibilizados, de forma que o desenvolver tenha apenas que "plugá-lo" no sistema ou processo de desenvolvimento.

Contudo, não precisamos definir uma baseline exatamente durante o escopo do projeto. Podemos fazer isso apenas antes de construirmos nossos próprios modelos. Afinal, neste ponto pelo menos há uma quantidade de dados (de boa qualidade) possivelmente suficiente.

Entramos em mais detalhes sobre a definição de baselines na seção [Avaliação][1].

### Preparação dos Dados

Etapa de coleta e processamento dos dados necessários para e execução do projeto e construção do modelo.

Esta etapa de preparação de dados é absolutamente importante, visto que erros nos dados são propagados ao longo de todo o projeto, o que pode resultar em problemas críticos.

Além disso, a etapa de preparação de dados é (geralmente) composta pelas seguintes tarefas:

- **Ingestão de Dados.** Coleta e armazenamento de dados oriundos de diversas fontes em diversos mecanismos de armazenamento, tais como Data Lakes e Data Warehouses. Essa etapa também pode incluir o enriquecimento de dados e/ou geração de dados sintéticos.
- **Exploração e Validação.** Perfilamento de dados a fim de obter informações sobre sua estrutura que, por sua vez, são utilizadas para definir possíveis esquemas de dados, assim como rotinas de teste e validação.
- **Data Cleaning.** Processo de formatação dos dados e correção de erros (e.g. valores faltantes ou inconsistentes) de forma que se enquadrem nos esquemas definidos.
- **Data Splitting.** Divisão do dados em conjuntos dedicados ao treinamento, validação e teste dos modelos produzidos.

Entramos em mais detalhes sobre a preparação de dados na seção [Preparação de Dados][2].

### Modelagem

Etapa onde os modelos cogitados para atacar o problema definido no [escopo](#escopo) são treinados, testados, selecionados e reavaliados.

As subetapas principais da etapa de modelagem são:

- **Treinamento & Seleção de Modelos.** Processo onde modelos são treinados e o melhor $-$ com base em métricas de desempenho para a tarefa em questão (e.g. acurácia, erro quadrático médio, etc) $-$ é selecionado como candidato à produção.

- **Avaliação.** Etapa onde são executadas análises de erro a fim de verificar se o modelo $-$ além de possuir boas métricas de desempenho (do ponto de vista de treinamento) $-$ resolve o problema definido. Nesta etapa também é comum avaliar um possível enviesamento do modelo, assim como requisitos não funcionais (e.g. tempo de inferência, custo computacional, etc.)

    Além disso, é nesta etapa que comparamos o modelo construído com possíveis baselines e analisamos quais pontos podem ser melhorados no nosso modelo.


Entramos em mais detalhes sobre o treinamento e avaliação de modelos nas seções [Treinamento][3] e [Avaliação][1], respectivamente.

### Implantação

Etapa onde o modelo construído é colocado em produção. Logo, além das práticas convencionais aplicadas no desenvolvimento de modelos de ML, durante o deployment (i.e. implantação) práticas de engenharia de software são aplicadas com mais intensidade, incluindo testes unitários e de integração, integração do modelo com o restante do sistema, definição de políticas de monitoramento, etc.

A etapa de implantação é geralmente composta pelos seguintes processos:

- **Model Serving.** Processo de disponibilização do modelo em um ambiente de produção para acesso pelos usuários.
- **Monitoramento de Performance.** Processo de monitoramento da performance do modelo em dados não vistos anteriormente a fim de identificar possíveis falhas no seu desenvolvimento e queda de desempenho ao longo do tempo.

!!! note "Nota"
    Mesmo após o deployment inicial, a manutenção de tanto o sistema quanto o modelo é necessária. Portanto, é comum reexecutarmos etapas anteriores frequentemente.

Entramos em mais detalhes sobre implantação, *serving* e monitoramento nas seções [Implantação][4], [Model Serving][5] e [Monitoramento][6], respectivamente.

## Referências

- [Machine Learning Engineering by Andriy Burkov](http://www.mlebook.com/)
- [Machine Learning Systems Design by Chip Huyen](https://github.com/chiphuyen/machine-learning-systems-design)
- [Introduction to Machine Learning in Production by Coursera](https://www.coursera.org/learn/introduction-to-machine-learning-in-production)
- [An Overview of the End-to-End Machine Learning Workflow by MLOps](https://ml-ops.org/content/end-to-end-ml-workflow)
- [ML Life Cycle by ProductizeML](https://course.productize.ml/productize-it/ml-lifecycle)

    [1]: ../evaluation/
    [2]: ../data_preparation/
    [3]: ../training/
    [4]: ../deployment/
    [5]: ../serving/
    [6]: ../monitoring/
