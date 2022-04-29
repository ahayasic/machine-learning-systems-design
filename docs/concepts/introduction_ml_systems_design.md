# Introdução ao Projeto de Sistemas de ML

## Introdução

Projetar um sistema de machine learning (ML) é um processo altamente iterativo e complexo.

Chip Huyen define o projeto de sistemas de ML como:

!!! quote "Citação"
    "Processo de definir todos os componentes de um sistema de ML, incluindo hardware, infraestrutura, interfaces, dados e algoritmos, de forma que o sistema atenda aos objetivos de negócio ao mesmo tempo que seja confiável, escalável, manutenível, adaptável e capaz de cumprir um conjunto restritivo de exigências." $-$ Chip Huyen

De fato, os algoritmos de ML são apenas uma pequena parte de um sistema de ML em produção. Afinal, um sistema em produção é muito mais do que apenas um algoritmo. Ele deve conter interfaces para que usuários e desenvolvedores interajam com o sistema, infraestrutura para executar as operações, engenharia e governança de dados para o gerenciamento dos dados, etc.

![image-20210209144401041](https://ahayasic.github.io/blog/assets/img/image-20210209144401041.png)
<p class="post__img_legend">
  <b>Fonte:</b> <a target="_blank" href="https://papers.nips.cc/paper/2015/file/86df7dcfd896fcaf2674f757a2463eba-Paper.pdf">Sculley, David, et al. "Hidden technical debt in machine learning systems." Advances in neural information processing systems 28 (2015): 2503-2511.</a>
</p>

Além disso, dada a escala de muitos sistemas de ML que $-$ consumem grandes quantidades de dados, exigem um grande recurso computacional e afeta milhares de vidas $-$ a simples necessidade de colocá-lo em produção já é um grande desafio de engenharia e social. Quando este desafio não é bem compreendido, o sistema de ML pode causar grandes prejuízos tanto à companhia quanto a vida das pessoas.

O conjunto de práticas e processos eficazes para projetar, construir e implantar modelos de ML em produção ainda está sendo definido. Contudo, já existem diversas abordagens interessantes que podemos aplicar em projeto de ML, como é o caso de [MLOps](mlops/index.md).

Aqui, vamos abordar as práticas mais comuns aplicadas em cada etapa do ciclo de desenvolvimento de ML que, juntas, formam um primeiro conjunto de estratégias fundamentais para o projeto de sistemas de ML.

### Pesquisa x Produção

Quando comparamos o desenvolvimento de sistemas de ML na indústria e algoritmos de ML na academia percebemos que os objetivos são diferentes.

Na academia, (geralmente) estamos preocupados em alcançar o estado-da-arte (SOTA, do inglês state-of-the-art) em algumas tarefas específicas. Por conta disso, é muito comum que os modelos resultantes sejam custosos demais para serem utilizados na indústria.

Modelos com bilhões de parâmetros, por exemplo, são extremamente custosos para treinar e operacionalizar. Dependendo de onde será feita a sua implantação (e.g. dispositivo móvel), o uso de algo tão complexo é inviável.

!!! note "Nota"
    Eventualmente os "big models" vão se tornar menores e mais rápidos. Porém, a diferença de prioridades entre a academia e indústria raramente irá permitir que os métodos SOTA sejam utilizados em produção.

Ainda, em projetos de pesquisa é muito comum que os dados sejam algum "benchmarking". Logo, são dados geralmente limpos e bem-formatados, o que permite o foco total no desenvolvimento de modelos de aprendizado. Por outro lado, sistemas em produção precisam lidar com falta de dados, dados extremamente desorganizados, ruídosos e que mudam constantemente.

De fato, tanto os objetivos quanto os desafios são consideralvemente diferentes em cada contexto. Contudo, ainda assim é muito comum os profissionais de dados focarem unicamente no desenvolvimento do modelo e encarar com menor importância as demais tarefas. Esta atitude é um exemplo de quando o desafio de colocar sistemas de ML em produção não é bem compreendido.

### Softwares Tradicionais x Sistemas de ML

Diferente de softwares tradicionais, sistemas de ML não compreendem apenas código, mas também dados e modelos. A adição de mais dois artefatos torna o desenvolvimento de aplicações baseadas em ML significativamente mais complexo, pois além de testar e versionar códigos, temos que testar e versionar dados e modelos.

Consequentemente, desafios únicos ao projeto de sistemas de ML surgem, desde etapas como desenvolvimento, até o teste, integração, compilação e monitoramento do sistema.

## Etapas de um Projeto de ML

Como dito anteriormente, projetar um sistema de ML é um processo altamente iterativo, complexo e, geralmente, interminável (uma vez que o sistema precisa ser monitorado e atualizado continuamente).

Este processo $-$ também chamado de *ciclo de vida* $-$ é composto por quatro etapas principais (que por sua vez, podem ser divididas em mais etapas). São elas: escopo, preparação dos dos, modelagem e implantação (do inglês, deployment).

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

<figure>
    <img src="https://gblobscdn.gitbook.com/assets%2F-MFCNLySTC0Jf6imOp3y%2F-MQwPKwMF0lj8wta91d7%2F-MQwPQlInVbfXE_6Q2ZZ%2Frisk-table.png?alt=media&token=e9ae7777-1f6d-4b18-b865-90d63cc5b7f0" style="max-width: 750px;">
    <figcaption class="post__img_legend">
        <b>Fonte:</b> <a target="_blank" href="https://course.productize.ml/productize-it/business-objectives">Business Objectives – ML Life Cycle by ProductizeML</a>
    </figcaption>
</figure>

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

- [Designing Machine Learning Systems by Chip Huyen](https://www.oreilly.com/library/view/designing-machine-learning/9781098107956/)
- [Machine Learning Engineering by Andriy Burkov](http://www.mlebook.com/)
- [Introduction to Machine Learning in Production by Coursera](https://www.coursera.org/learn/introduction-to-machine-learning-in-production)
- [An Overview of the End-to-End Machine Learning Workflow by MLOps](https://ml-ops.org/content/end-to-end-ml-workflow)
- [ML Life Cycle by ProductizeML](https://course.productize.ml/productize-it/ml-lifecycle)

    [1]: ../evaluation/
    [2]: ../data_preparation/
    [3]: ../training/
    [4]: ../deployment/
    [5]: ../serving/
    [6]: ../monitoring/