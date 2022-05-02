# Sobre Machine Learning Engineering

## Introdução

Projetar, construir e manter soluções de machine learning não são tarefas fáceis. Muito pelo contrário! São complexas e exigem melhorias contínuas.

De acordo com o relatório ["2020 State of Enterprise Machine Learning"](https://bit.ly/3e7HfaQ) da Algorithmia.

- A maior parte das empresas ainda não descobriram como atingir seus objetivos de ML/IA pois a lacuna entre a construção do modelo de ML e o deploy é desafiadora.
- Apenas [22% das companhias](https://designingforanalytics.com/resources/failure-rates-for-analytics-bi-iot-and-big-data-projects-85-yikes/) que usam aprendizado de máquina implantaram com sucesso um modelo de ML em produção.

E isso ocorre pois **apenas uma pequena fração dos sistemas de ML do mundo real são compostos por código de ML**. Um **sistema em produção é composto de diversos componentes**, como, por exemplo: interfaces para que usuários e desenvolvedores interajam com o sistema, infraestrutura para executar a aplicação, engenharia e governança de dados para o gerenciamento e confiabilidade dos dados, entre outros.

![image-20210209144401041](https://raw.githubusercontent.com/ahayasic/machine-learning-systems-design/main/docs/assets/serving/image-20210209144401041.png)
<p class="post__img_legend">
  <b>Fonte:</b> <a target="_blank" href="https://papers.nips.cc/paper/2015/file/86df7dcfd896fcaf2674f757a2463eba-Paper.pdf">Sculley, David, et al. "Hidden technical debt in machine learning systems." Advances in neural information processing systems 28 (2015): 2503-2511.</a>
</p>

Além disso, considerando a escala de muitos sistemas de ML que $-$ consumem grandes quantidades de dados, exigem um grande recurso computacional e afeta milhares de vidas $-$ a simples necessidade de colocá-lo em produção já é um grande desafio de engenharia e social. Como diz nossa querida Chip Huyen:

!!! quote "Citação"
    "Quando este desafio não é bem compreendido, o sistema de ML pode causar grandes prejuízos tanto à companhia quanto a vida das pessoas." $-$ Chip Huyen

Portanto, precisamos de um **conjunto de práticas e processos eficazes para projetar, construir, implantar e manter modelos de ML em produção de forma escalável e confiável**. Andriy Burkov define **machine learning engineering** como a área responsável pela produtização, operacionalização e manutenção de sistemas de ML, sendo a definição:

!!! quote "Definição de Machine Learning Engineering"
    "Machine learning engineering é o uso de princípios científicos, ferramentas e técnicas de aprendizado de máquina e engenharia de software tradicional para projetar e construir sistemas de computação complexos. O MLE abrange todas as etapas, desde a coleta de dados, até a construção do modelo, a fim de disponibilizar o modelo para uso pelo produto ou  consumidores." $—$ Andriy Burkov

Porém, há pessoas que dizem que MLOps é a área responsável por lidar com modelos em produção, sendo MLOps definido pela [MLOps SIG](https://github.com/cdfoundation/sig-mlops/blob/master/roadmap/2020/MLOpsRoadmap2020.md) como:

!!! quote "Definição de MLOps"
    "A extensão da metodologia DevOps para incluir ativos de aprendizado de máquina e ciência de dados como cidadãos de primeira classe dentro da ecologia DevOps."

Contudo, **independente do termo utilizado (MLOps ou MLE)**, o que importa é o objetivo da área de fornecer um processo para o projeto e desenvolvimento de sistemas baseados em machine learning que sejam reprodutíveis, escaláveis e robustos.

**Aqui**, MLOps estará restrito a tarefas relacionadas a operacionalização dos modelos de ML. Demais tarefas correspondentes a outras [etapas de um projeto de ML](#etapas-de-um-projeto-de-ml) terão práticas classificadas como pertencentes a área **machine learning engineering**.

!!! warning "Sistema x Aplicação"
    Embora eu já tenha escrito "sistema", "sistema" e "sistema", não deixei claro sobre o que eu estou falando. O que é um sistema de ML? É a aplicação? Ou a solução de ML que a aplicação usa? E essa solução de ML... Usa alguma plataforma de ML para auxiliar na produção de modelos? Se sim, a plataforma é o sistema de ML?

    No caso, há pessoas que consideram *aplicações baseadas em ML* (i.e. que usam recursos de ML em algum momento, como uma aplicação que usa algoritmos de recomendação) e *sistemas de ML* como a mesma coisa. Porém, *"sistemas de ML"* não me parece transmitir bem essa ideia.

    Então, vamos considerar **aqui** que um **sistema de ML é qualquer aplicação ou solução que possui alguma dependência com algoritmos de ML e, portanto, necessitam de práticas específicas para ML.** Ainda, também vamos dizer que uma **plataforma de ML** é uma aplicação que fornece um conjunto de recursos a execução de práticas MLOps.

## Machine Learning na Indústria

Muitas pessoas possuem o senso comum de que:

- O desenvolvimento de algoritmos e sistemas de ML é o mesmo tanto na indústria quanto na academia
- Desenvolver aplicações baseadas em ML é o mesmo que desenvolver softwares tradicionais.

Essa perspectiva não apenas está errada, como também é perigosa. **Sistemas de ML possuem diversos desafios próprios e, como já dito anteriormente, não compreender tais desafios pode levar a consequências ruins**.

### ML na Pesquisa x ML na Indústria (Mercado)

O desenvolvimento de modelos de ML na academia possui um propósito diferente quando comparado com a indústria.

Na academia, (geralmente) estamos preocupados em alcançar o estado-da-arte (SOTA, do inglês state-of-the-art) em um determinado problema. Por conta disso, é muito comum que os modelos resultantes sejam custosos demais para serem utilizados na indústria.

Modelos com bilhões de parâmetros, por exemplo, são extremamente custosos para treinar e operacionalizar. Dependendo de onde será feita a sua implantação (e.g. dispositivo móvel), o uso de algo tão complexo é inviável.

!!! note "Nota"
    Eventualmente os "big models" vão se tornar menores e mais rápidos. Porém, a diferença de prioridades entre a academia e indústria raramente irá permitir que os métodos SOTA sejam utilizados em produção.

Ainda, em projetos de pesquisa é muito comum que os dados sejam algum "benchmarking". Logo, são dados **geralmente** limpos e que permitem o foco total no desenvolvimento de modelos de aprendizado. Por outro lado, sistemas em produção precisam lidar com falta de dados, dados desorganizados, ruídosos e que mudam constantemente.

De fato, tanto os objetivos quanto os desafios são consideralvemente diferentes em cada contexto. Contudo, ainda assim é muito comum os profissionais de dados focarem unicamente no desenvolvimento do modelo e encarar com menor importância as demais tarefas. Esta atitude é um exemplo de quando o desafio de colocar sistemas de ML em produção não é bem compreendido.

### Softwares Tradicionais x Sistemas de ML

Diferente de softwares tradicionais, sistemas de ML não compreendem apenas código, mas também dados e modelos. A adição de mais dois artefatos torna o desenvolvimento de aplicações baseadas em ML significativamente mais complexo, pois além de testar e versionar códigos, temos que testar e versionar dados e modelos.

Consequentemente, desafios únicos ao projeto de sistemas de ML surgem, desde etapas como desenvolvimento, até o teste, integração, compilação e monitoramento do sistema.

## Etapas de um Projeto de ML

O projeto de um sistema de ML é processo formado por várias etapas que partem desde a concepção até a manutenção da solução encontrada. Este processo $-$ também chamado de *ciclo de vida* $-$ é composto por quatro etapas principais (que por sua vez, podem ser divididas em mais etapas). São elas:

- Escopo
- Preparação dos Dados
- Modelagem
- Implantação (do inglês, deployment).

!!! info "Not so shallow...\ \ \ :grin:"
    Além de descritas abaixo, cada uma dessas etapas contém seções particulares onde são abordadas com mais detalhes, incluindo estratégias sobre o que fazer em cada momento da etapa e como fazer.

!!! warning "A graça do mundo está na diversidade"
    O ciclo de vida apresentado aqui é o considerado por Andrew Ng. No entanto, há diversas figuras importantes na área de ML com perspectivas diferentes sobre o ciclo de vida e suas etapas.

    Por exemplo, este ciclo de vida pode transmitir uma noção de ser algo direto e não repetitivo. Porém, muito pelo contrário, na prática o processo de criação de um modelo de ML é extremamente iterativo, ciclo e dificilmente termina. Afinal, uma vez que o modelo está em produção, precisamos mantê-lo! Consequentemente, temos que desenvolver toda uma estrutura para que isso seja feito da forma mais dinâmica possível. Nesse sentido, **preparação de dados** é a etapa onde toda a arquitetura de dados para o projeto é definida, por exemplo.

### Escopo

O escopo é a etapa onde o projeto é definido. Logo:

- Qual problema será atacado
- Como o problema será atacado
- Qual o **critério de sucesso**

Os principais objetivos desta etapa são identificar o que deve ser feito e o quão viável é o que deve ser. Assim, é muito importante que o **objetivo do projeto** seja claro e bem definido.

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

Até o momento não existem métodos de estimação de complexidade de um projeto de ML que são amplamente utilizados pela indústria. Ainda, projetos de ML são incertos por natureza uma vez que nem sempre os recursos necessários para a solução do problema existem ou são viáveis.

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

### Preparação dos Dados

Esta é a etapa onde os dados necessários para a execução do projeto e construção do modelo são coletados e processados.

Note que a preparação de dados é absolutamente importante, visto que erros nos dados são propagados ao longo de todo o projeto, o que pode resultar em problemas críticos.

Além disso, a etapa de preparação de dados é (geralmente) composta pelas seguintes tarefas:

- **Ingestão de Dados.** Coleta e armazenamento de dados oriundos de diversas fontes em diversos mecanismos de armazenamento, tais como Data Lakes e Data Warehouses. Essa etapa também pode incluir o enriquecimento de dados e/ou geração de dados sintéticos.
- **Exploração e Validação.** Perfilamento de dados a fim de obter informações sobre sua estrutura que, por sua vez, são utilizadas para definir possíveis esquemas de dados, assim como rotinas de teste e validação.
- **Data Cleaning.** Processo de formatação dos dados e correção de erros (e.g. valores faltantes ou inconsistentes) de forma que se enquadrem nos esquemas definidos.
- **Data Splitting.** Divisão do dados em conjuntos dedicados ao treinamento, validação e teste dos modelos produzidos.

Entramos em mais detalhes sobre a preparação de dados na seção [Preparação de Dados](data_preparation/index.md).

### Modelagem

Esta é a etapa onde os modelos cogitados para atacar o problema definido no [escopo](#escopo) são treinados, testados, selecionados e reavaliados.

As subetapas principais da etapa de modelagem são:

- **Treinamento & Seleção de Modelos.** Processo onde modelos são treinados e o melhor $-$ com base em métricas de desempenho para a tarefa em questão (e.g. acurácia, erro quadrático médio, etc) $-$ é selecionado como candidato à produção.
- **Avaliação.** Processo onde são executadas análises de erro a fim de verificar se o modelo $-$ além de possuir boas métricas de desempenho (do ponto de vista de treinamento) $-$ resolve o problema definido. Nesta etapa também é comum avaliar um possível enviesamento do modelo, assim como requisitos não funcionais (e.g. tempo de inferência, custo computacional, etc.)

    Além disso, é neste momento que comparamos o modelo construído com possíveis baselines e analisamos quais pontos podem ser melhorados.

Entramos em mais detalhes sobre o treinamento e avaliação de modelos nas seções [Construção de Modelos](training/index.md) e [Avaliação](evaluation/index.md), respectivamente.

### Implantação

Etapa onde o modelo construído é colocado em produção. Logo, além das práticas convencionais aplicadas no desenvolvimento de modelos de ML, durante o deployment (implantação) práticas de engenharia de software são aplicadas com mais intensidade, incluindo testes unitários e de integração, integração do modelo com o restante do sistema, definição de políticas de monitoramento, etc.

A etapa de implantação é geralmente composta pelos seguintes processos:

- **Model Serving.** Processo de disponibilização do modelo em um ambiente de produção para acesso pelos usuários.
- **Monitoramento de Performance.** Processo de monitoramento da performance do modelo em dados não vistos anteriormente a fim de identificar possíveis falhas no seu desenvolvimento e queda de desempenho ao longo do tempo.

!!! note "Nota"
    Mesmo após o deployment inicial, a manutenção de tanto o sistema quanto o modelo é necessária. Portanto, é comum reexecutarmos etapas anteriores frequentemente.

Entramos em mais detalhes sobre implantação, *serving* e monitoramento nas seções [Implantação](deployment/index.md), [Model Serving](serving/index.md) e [Monitoramento](monitoring/index.md), respectivamente.

## Referências

- [Designing Machine Learning Systems by Chip Huyen](https://www.oreilly.com/library/view/designing-machine-learning/9781098107956/)
- [Machine Learning Engineering by Andriy Burkov](http://www.mlebook.com/)
- [Introduction to Machine Learning in Production by Coursera](https://www.coursera.org/learn/introduction-to-machine-learning-in-production)
- [An Overview of the End-to-End Machine Learning Workflow by MLOps](https://ml-ops.org/content/end-to-end-ml-workflow)
- [ML Life Cycle by ProductizeML](https://course.productize.ml/productize-it/ml-lifecycle)
