# Introdução ao MLOps

!!! attention "Attention! Atenção!"
    **(en_US)** This post is a translation (into pt_BR) of my post [Introducing MLOps](https://medium.com/daitan-tech/introducing-mlops-9d5d2d35de04) originally published on [Daitan's](https://medium.com/daitan-tech) blog.

    **(pt_BR)** Esta publicação é uma tradução (para pt_BR) do meu artigo [Introducing MLOps](https://medium.com/daitan-tech/introducing-mlops-9d5d2d35de04) publicado originalmente no blog da [Daitan](https://medium.com/daitan-tech).

Ciência de dados e aprendizado de máquina (ML, do inglês machine learning) têm se tornado estratégias prioritárias na resolução de diversos problemas complexos do mundo real.

Porém, embora implementar e treinar modelos de ML não sejam tarefas triviais, ainda assim

- O verdadeiro desafio não é construir modelos de ML
- O verdadeiro desafio é construir sistemas de ML integrados e que podem ser atulizados e operados continuamente em produção.

Afinal, para tirarmos máximo proveito de um modelo ML, precisamos colocá-lo em produção.

Contudo, de acordo com o relatório ["2020 State of Enterprise Machine Learning"](https://bit.ly/3e7HfaQ) da Algorithmia.

- A maior parte das empresas ainda não descobriram como atingir seus objetivos de ML/IA pois a lacuna entre a construção do modelo de ML e o deploy é desafiadora.
- Apenas [22% das companhias](https://designingforanalytics.com/resources/failure-rates-for-analytics-bi-iot-and-big-data-projects-85-yikes/) que usam aprendizado de máquina implantaram com sucesso um modelo de ML em produção.

Ao mesmo tempo, a própria construção do modelo e avaliação deste em escala é uma tarefa complicada.

- Em aplicações do "mundo real", avaliar o desempenho e impacto do modelo no problema que se busca resolver vai muito além de uma simples experimentação de *"treino e teste"*.
- Também é necessário levar em conta questões como complexidade do algoritmo, velocidade de inferência e enviesamento.

Além disso, de acordo com o artigo ["Hidden Technical Debt in Machine Learning Systems"](https://papers.nips.cc/paper/2015/file/86df7dcfd896fcaf2674f757a2463eba-Paper.pdf)

- Apenas uma pequena fração dos sistemas de ML do mundo real é composta por código de ML, enquanto que a infraestrutura envolvente necessária é vasta e complexa.

![image-20210209144401041](https://ahayasic.github.io/blog/assets/img/image-20210209144401041.png)
<p class="post__img_legend">
  <b>Fonte:</b> <a target="_blank" href="https://papers.nips.cc/paper/2015/file/86df7dcfd896fcaf2674f757a2463eba-Paper.pdf">Sculley, David, et al. "Hidden technical debt in machine learning systems." Advances in neural information processing systems 28 (2015): 2503-2511.</a>
</p>


Portanto, é necessário **estabelecer um conjunto de práticas e processos eficazes para projetar, construir e implantar modelos de ML em produção.**

## Definições

De acordo com a [MLOps SIG](https://github.com/cdfoundation/sig-mlops/blob/master/roadmap/2020/MLOpsRoadmap2020.md), MLOps é:

!!! quote "Citação"
    "A extensão da metodologia DevOps para incluir ativos de aprendizado de máquina e ciência de dados como cidadãos de primeira classe dentro da ecologia DevOps."

Porém, como uma área em ascensão, o termo MLOps não é estritamente definido, especialmente quando comparado com machine learning engineering (MLE). Portanto, a definição de Andriy Burkov sobre MLE também é aplicável à MLOps, onde

!!! quote "Citação"
    "Machine learning engineering é o uso de princípios científicos, ferramentas e técnicas de aprendizado de máquina e engenharia de software tradicional para projetar e construir sistemas de computação complexos. O MLE abrange todas as etapas, desde a coleta de dados, até a construção do modelo, a fim de disponibilizar o modelo para uso pelo produto ou  consumidores." $—$ Andriy Burkov

Assim, independente do termo utilizado (MLOps e MLE), o que importa é o objetivo da área de fornecer um processo de projeto e desenvolvimento de sistemas baseados em machine learning que sejam reprodutíveis, escaláveis e robustos.

## Benefícios do MLOps

Como dito, MLOps tem como objetivo fornecer um conjunto de práticas e processos eficazes para projetar, construir e implantar modelos escaláveis de ML em produção. Isso pode ser alcançado ao garantir capacidades e qualidades fundamentais, tanto para a aplicação quanto para o projeto em si. Alguns exemplos são:

- Redução do débito técnico ao longo do projeto de ML.
- Aplicação de Princípios Ágeis ao projeto de ML.
- Garantia de reprodutibilidade.
- Versionamento de dados, pipelines e modelos.
- Teste automatizando de artefatos de ML.[^1]
- Monitoramento de performance dos modelos em produção.
- Suporte a CI/CD para artefatos de ML, incluindo dados.
- Suporte a CT (Continuous training) para modelos e pipelines.
- Unificação do ciclo de entrega tanto para os modelos quanto para toda a aplicação.
- Escalabilidade, alta disponibilidade, tolerância à falhas, equidade e segurança no contexto de ML.

Note que a partir dessas capacidades, mais benefícios surgem, como velocidade no processo de introdução dos modelos à produção, custo reduzido de desenvolvimento e operações (em nível empresariaral), mitigação de riscos associados ao projeto, etc.

## Práticas Fundamentais

No mundo de MLOps, novas tendências e práticas surgem o tempo todo. Porém, há algumas práticas essenciais que fazem parte do coração do MLOps. Tais práticas são obrigatórias para se alcançar um processo de desenvolvimento de sistemas baseados em ML poderoso. Além disso, cada uma dessas práticas podem ser estendidas e melhoradas.

### Controle de Versão

Diferente do desenvolvimento convencional de software, aplicações baseadas em ML possuem três artefatos que devem ser trabalhados: dado, modelo e código[^2].

A prática de versionar dado, modelo e código é uma extremamente importante no âmbito de MLOps, visto que a partir do versionamento é possível melhorar a reprodutibilidade e garantir manutenibilidade, prevenção de erros e recuperação de desastres para todo o projeto.

Por exemplo, pode haver situações onde a atualização de um modelo em produção prejudica a performance da aplicação como um todo. Assim, é necessário reverter o modelo (i.e. rollback) para uma versão anterior de modo automático. Outro caso é a necessidade de um *tracking* pesado de alterações, uma vez que tanto o dado quanto o modelo são atualizados frequentemente de forma automática.

Assim, o versionamento de artefatos em projetos de ML permite:

- Manter tanto alterações no modelo quanto no dado rastreadas, possibilitando identificar inserção de *bugs* ou mudanças que ferem a performance da aplicação.
- Reverter a versão do modelo para uma versão anterior no caso de releases quebradas (ou que podem vir a quebrar em produção).
- Automatizar todo o pipeline de ML através de CI/CD e CT.

### Rastreamento de Experimentos

Devido a natureza experimental e iterativa de modelos de ML, manter um rastreamento sistemáþico de todas as informações relacionadas aos experimentos é essencial. Basicamente, o rastreio de experimentos é a prática de salvar (i.e. "loggar") todas as informações importantes relacionadas aos dados, modelo e código de cada iteração do experimento executado, de forma que seja possível se ter um conhecimento completo de cada informação gerada e o controle total sobre todas as modificações realizadas.

Por exemplo, ao desenvolvermos um modelo, podemos querer rastrear (e versionar) em cada iteração de um experimento:

- Scripts (código-fonte) usado.
- Arquivos de configuração.
- Dados e metadados utilizados para o treinamento, validação e teste.
- Parâmetros e hiperparâmetros do modelo.
- Resultados das métricas de avaliação do modelo.
- Resultados das métricas de performance da aplicação.

Uma vez tendo essas informações, podemos comparar os diferentes resultados alcançados, identificar o impacto de cada alteração no resultado final, identificar problemas de performance do sistema, etc. Portanto, a prática de rastrear os experimentos é fundamental tanto para a reprodutiblidade (de fato, é a principal forma de alcançá-la) quanto para o desenvolvimento da aplicação em si.

### Pipelines de ML Automatizados

A automação é outra prática fundamental em MLOps. No contexto de ML, a automação consiste em automatizar todos os pipelines do *workflow* de ML, incluindo pipelines de dados, construção de modelos e integração de código a fim de que todo o processo seja executado sem qualquer intervenção humana. Com isso:

- Os experimentos acontecem de forma mais rápida e com uma maior prontidão para mover todo o pipeline do desenvolvimento à produção.
- Os modelos em produção são automaticamente retreinados por meio dos dados atualizados (onde, o retreinamento é automaticamente ativado através de *triggers*).
- O pipeline implementado no ambiente de desenvolvimento é correspondente ao utilizado nos ambientes de (pré-)produção.
- O pipeline em produção está sempre atualizado, uma vez que a etapa de deployment do modelo também é automatizado.

Logo, considerando um pipeline típico de ML, que parte da coleta de dados até a disponibilização do modelo, podemos considerar (no geral) 3 níveis de automação.

#### Nível 1 - Processo Manual

O Nível 1 (Processo Manual) é o processo tradicional de ciência de dados, onde cada etapa do pipeline é executado usando ferramentas RAD (do inglês, Rapid Application Development), como Jupyter Notebooks.

Este nível de automação é caracterizado principalmente pela natureza experimental e iterativa.

![manual_process](https://ahayasic.github.io/blog/assets/img/manual_process.jpeg)
<p class="post__img_legend">
  <b>Fonte:</b> Example of a manual process. Adapted from: <a target="_blank" href="https://cloud.google.com/architecture/mlops-continuous-delivery-and-automation-pipelines-in-machine-learning">Google Cloud [4]</a>
</p>

#### Nível 2 - Pipeline de ML Automatizado

O Nível 2 de automação é um nível onde todo o processo de construção à validação do modelo é executado automaticamente conforme novos dados são disponibilizados ou o procedimento de retreino é disparado (baseado em uma política de agendamento ou threshold de performance). Assim, o objetivo e prover um processo treinamento contínuo (CT, do inglês continuous training) através da automatização de todo o pipeline de ML.

Este nível de automatação é caracterizado por:

- Experimentos orquestrados[^3]
- Modelos em produção que são continuamente atualizados automaticamente.
- Etapas de teste e deployment ocorrem manualmente.

![automated_ml_pipeline](https://ahayasic.github.io/blog/assets/img/automated_ml_pipeline.jpeg)
<p class="post__img_legend">
  <b>Fonte:</b> Example of an automated ML pipeline. Adapted from: <a target="_blank" href="https://cloud.google.com/architecture/mlops-continuous-delivery-and-automation-pipelines-in-machine-learning">Google Cloud [4]</a>
</p>

#### Nível 3 - Pipeline CI/CD

No Nível 3 de automação, todo o *workflow* ocorre automaticamente através de estratégias de CI/CD. Logo, diferente do anterior, as etapas de build, teste e deployment de cada um dos artefatos (dado, modelo e código) também ocorrem automaticamente.

Este nível de automação é caracterizado por:

- Experimentos orquestrados
- Automação completa de todo o pipeline de ML, incluindo build, teste e deployment de cada um dos artefatos relacionados ao pipeline.

![cicd_pipeline](https://ahayasic.github.io/blog/assets/img/cicd_pipeline.jpeg)
<p class="post__img_legend">
  <b>Fonte:</b> Example of a CI/CD pipeline. Adapted from: <a target="_blank" href="https://cloud.google.com/architecture/mlops-continuous-delivery-and-automation-pipelines-in-machine-learning">Google Cloud [4]</a>
  <br />
</p>

## Práticas Adicionais

### Teste Automatizados

Conforme a automação do sistema de ML se torna mais sofisticada, as rotinas de teste devem acompanhar a evolução do sistema e passarem a ser executadas automaticamente. Portanto, além dos testes unitários e de integração, devemos incluir testes específicos tanto para os modelos quanto dados.

Por exemplo, checar se:

- O modelo (em desenvolvimento e produção) não está enviesado.
- O modelo (em produção) não está obsoleto.
- O dado segue os esquemas definidos.

### Monitoramento

Após um modelo ir para produção, ele precisa ser monitoramento a fim de garantir que funcione como o esperado. No contexto de pipelines de ML, o monitoramento é um *pré-requisito* para uma automação apropriada. Em outras palavras, apenas através do monitoramento é possível acompanhar a performance do modelo em produção e automaticamente retreiná-lo quando ele se tornar obsoleto.

![monitoramento](https://ahayasic.github.io/blog/assets/img/ml_model_decay_monitoring.jpeg)
<p class="post__img_legend">
  <b>Fonte:</b> ML Model Decay Monitoring and Retraining. Source: <a target="_blank" href="https://ml-ops.org/content/mlops-principles#monitoring">https://ml-ops.org/</a>
  <br />
</p>

### Feature Stores

Uma *Feature Store* é um serviço centralizado de armazenamento e processamento de *features* através do qual as features são definidas, armazenadas e usadas tanto para o treinamento de modelos quanto para modelos em produção. Desse modo, feature stores devem ser capazes de armazenar um grande volume de dados e fornecer acesso com baixa latência para as aplicações.

Alguns benefícios de feature stores são:

- Reuso das features disponíveis através do compartilhamento do dado entre times e projetos (ao invés de recriá-las).
- Prevenção de features semelhantes mas com definições diferentes através da manutenção do pipeline de extração de features e dos metadados relacionados.
- Disponibilização em escala e com baixa latência das feaures, principalmente para rotinas de retreinamento.
- Garantira de consistência das features entre o processo de treinamento e deployment.

## Conclusão

Dado o crescente uso de ML em vários setores da indústria e a necessidade por aplicações baseadas em ML manuteníveis e escaláveis, a adoção da cultura de MLOps deve ser tornar um padrão para todos aqueles que trabalham com IA ao longo dos próximos anos. Afinal, MLOps tem se mostrado essencial em projetos de larga escala graças aos diversos benefícios indispensáveis que são gerados.

## Notas e Comentários

[^1]: Artefatos de ML são todos os (hiper)parâmetros, scripts e dados de treinamento e teste utilizados para a construção de um modelo.

[^2]: Dado compreende tanto o pipeline de dados quanto os dados utilizados para treinamento, validação e teste. Modelo compreende todos os artefatos associados à construção do modelo. Código compreende tanto aos códigos relacionados aos dados e modelo, quanto o código-fonte da aplicação ao qual o modelo deve ser integrado.

[^3]: Experimentos orquestrados são aqueles cujas transições entre cada etapa do experimento ocorre de maneira automática e com um rastramento rigoroso.

## Referências

- [Sculley, David, et al. “Hidden technical debt in machine learning systems.” Advances in neural information processing systems 28 (2015): 2503–2511.](https://papers.nips.cc/paper/2015/file/86df7dcfd896fcaf2674f757a2463eba-Paper.pdf)
- ["ML-Ops.org." MLOps, ml-ops.org/.](https://ml-ops.org/)
- [Burkov, Andriy. Machine learning engineering. True Positive Incorporated, 2020.](http://www.mlebook.com/wiki/doku.php)
- ["MLOps: Continuous Delivery and Automation Pipelines in Machine Learning." Google Cloud, cloud.google.com/architecture/mlops-continuous-delivery-and-automation-pipelines-in-machine-learning](https://cloud.google.com/architecture/mlops-continuous-delivery-and-automation-pipelines-in-machine-learning)
- [Breck, Eric, et al. "The ml test score: A rubric for ml production readiness and technical debt reduction." 2017 IEEE International Conference on Big Data (Big Data). IEEE, 2017.](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/aad9f93b86b7addfea4c419b9100c6cdd26cacea.pdf)