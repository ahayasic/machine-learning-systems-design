# Qualidades Desejáveis em Projetos de Machine Learning Ponta-a-Ponta

A fim de garantir o sucesso de um projeto de ML, são necesárias algumas qualidades essenciais.

É importante ressaltar que, diferente de um projeto de desenvolvimento de software convencional, aplicações baseadas em Machine Learning possuem três artefatos que devem ser trabalhados: **Dado**, **Modelo**, **Código**.

## Qualidade \#1: Reprodutibilidade

A reprodutibilidade em um fluxo de trabalho de aprendizado de máquina significa que:

- Todas as fases do processamento de dados, do treinamento do modelo de ML e da implantação do modelo de ML **devem produzir resultados idênticos com a mesma entrada**.

A Tabela abaixo contém algumas dicas de como garantir reprodutibilidade em diferentes etapas do ML Workflow.

<table>
<thead>
  <tr>
    <th>Fase</th>
    <th>Desafios</th>
    <th>Dicas para Garantir Reprodutibilidade</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>Coleta de Dados</td>
    <td>Dados de treinamento não podem ser reproduzidos (e.g devido a constantes mudanças no banco de dados)</td>
    <td>1) Fazer (sempre) backup dos dados<br>2) Salvar snapshots do conjunto de dados<br>3) Projetar fontes de dados com <i>timestamps</i> a fim de que uma visão dos dados em qualquer ponto possa ser recuperada<br>4) Versionar dados</td>
  </tr>
  <tr>
    <td>Feature Engineering</td>
    <td>1) Valores faltantes imputados aleatoriamente ou através de médias de valores<br>2) Uso de métodos não determinísticos na extração de <i>features</i></td>
    <td>1) Versionar o código responsável pela geração de <i>features</i><br>2) Exigir reprodutibilidade da etapa "Coleta de Dados"</td>
  </tr>
  <tr>
    <td>Model Training&nbsp;&nbsp;/ Model Build</td>
    <td>Não Determinismo</td>
    <td>1) Garantir que a ordem das <i>features</i> seja sempre a mesma<br>2) Documentar e automatizar a seleção e transformação de <i>features</i><br>3) Documentar e automatizar a escolha de hiperparâmetros<br>4) Documentar e automatizar a combinação de modelos (em Ensemble Learning)</td>
  </tr>
  <tr>
    <td>Model Deployment</td>
    <td>1) Treinamento do modelo de ML foi realizado com uma versão de software diferente do ambiente de produção<br>2) Os dados de entrada exigidos pelo modelo de ML estão ausentes no ambiente de produção</td>
    <td>1) Versões de software e dependências devem ser iguais no ambiente de desenvolvimento e produção<br>2) Usar containers (Docker) e documentar sua especificação, como a versão da imagem<br></td>
  </tr>
</tbody>
</table>

_**Fonte.** [ml-ops.org](https://ml-ops.org/content/end-to-end-ml-workflow)_ 

### Leitura Adicional
-   [Chapter 3.12 - Burkov, Andriy. *Machine Learning Engineering*. True Positive Incorporated, 2020](http://www.mlebook.com/)
-   [Chapter 4.12 - Burkov, Andriy. *Machine Learning Engineering*. True Positive Incorporated, 2020](http://www.mlebook.com/)
-   [Chapter 6.7 - Burkov, Andriy. *Machine Learning Engineering*. True Positive Incorporated, 2020](http://www.mlebook.com/)
-   [Chapter 10: Model and Data Life Cycle Management - Ameisen, Emmanuel. *Building Machine Learning Powered Applications*. O’Reilly Media, 2020](https://www.amazon.com.br/Building-Machine-Learning-Powered-Applications/dp/149204511X)
-   Chapter 1.x: Responsible AI
-   Chapter 5.x: From Novice to Master Predictor: Maximizing Convolutional Neural Network Accuracy

## Qualidade \#2: Tracking de Experimentos

O desenvolvimento de modelos de ML é um processo altamente iterativo e centrado na experimentação. Logo, vários experimentos tendem a ser executados antes de colocar um modelo em produção.

Por conta disso, o acompanhamento de experimentos com todos os “ativos de ML” (por exemplo, modelo de ML, seus parâmetros e hiperparâmetros, scripts de treinamento, dados de treinamento e teste) é fundamental.

## Qualidade \#3: Versionamento

A capacidade de versionamento dos artefatos (**Dado**, **Modelo** e **Código**) de um projeto de Machine Learning é fundamental para: **reprodutibilidade**, **manutenção** e **prevenção de erros** (tanto para o modelo, quanto para toda toda a aplicação).

Por exemplo, pode haver situações em que:

- Após a atualização de um dado a **performance do modelo piore**.
  - Portanto, é necessário investigar o **porquê** trocando de uma versão para outra.

- Os dados são mantidos e atualizados em diversas fontes diferentes frequentemente; O modelo é atualizado automaticamente com frequência.
  - Assim, é necessario manter um rastreamento pesado de alterações.

Logo, prover versionamento de artefatos de um projeto de ML ajuda a principalmente

- Manter o **rastreamento de modificações** tanto dos **dados** quanto do **modelo**
  - com isso, é possível identificar inserção de bugs ou alterações que causaram uma queda de desempenho no modelo
- **Reverter** a **versão** dos **dados** ou modelo para versões anteriores no caso de releases quebradas (ou que venham a quebrar em produção)
- **Automatizar** através de CI/CD (e CT) a **integração** tanto dos **dados** quanto de **modelos** ao restante do sistema.

Note que **as versões dos três elementos** a seguir devem estar sempre sincronizadas:

1. Dados de Treinamento
2. Feature Extractor
3. Modelo

Assim, cada atualização nos dados deve produzir uma nova versão no repositório de dados. Já o modelo treinado usando uma versão específica dos dados deve ser colocado no repositório de modelos com o mesmo número de versão dos dados usados para treinar o modelo. 

- Os três devem ser implantados e/ou revertidos ao mesmo tempo.

### Leitura Adicional

-   [Chapter 3.12 - Burkov, Andriy. *Machine Learning Engineering*. True Positive Incorporated, 2020](http://www.mlebook.com/)
-   [Chapter 4.12 - Burkov, Andriy. *Machine Learning Engineering*. True Positive Incorporated, 2020](http://www.mlebook.com/)
-   [Chapter 6.7 - Burkov, Andriy. *Machine Learning Engineering*. True Positive Incorporated, 2020](http://www.mlebook.com/)
-   Chapter 9.x: Desirable Qualities in a Production-Level Serving System

## Qualidade \#4: Teste

Como aplicações baseadas em ML compreendem três artefatos principais (Dado, Modelo e Código), a prática de testes inclui: Teste em Dados, Teste em Modelo e Testes na Infraestrutura do Modelo.

Ainda, **os testes de cada artefato também devem incluir testes unitários e de integração**.

### Teste em Dados

O Teste em Dados visa garantir a qualidade e taxinomia dos dados para modelos de ML, tanto em um ambiente de desenvolvimento quanto teste. Alguns exemplos de teste em dados:

- **Validação de Dados.** Checagem automática de dados e *features*, incluindo limpeza e padronização.
- **Teste de Conformidade Política (e.g., GDPR e LGPD).** Garantir que os pipelines de dados e *features* estão de acordo com as legislações.

Novamente, Testes Unitários devem estar presentes em todo o escopo.

### Teste em Modelo

O Teste de Modelo visa garantir que os algoritmos tomem decisões alinhadas ao objetivo de negócios, tanto em um ambiente offline quanto online (i.e. em produção). Alguns exemplos:

- **Validação de Performance do Modelo.** Em um ambiente offline, garantir através de conjuntos de testes e KPIs adequados o alinhamento do modelo com os objetivos de projeto.

  Já em um ambiente online, avaliar através de técnicas de experimentação controlada (e.g., Teste A/B) a capacidade do modelo em resolver o problema para o qual foi desenvolvido e gerar valor.

- **Teste de Envisamento e Preconceito do Modelo.** Garantir que o modelo não possua viés ou preconceito estrutural implícito, promovendo desigualdade.

- **Teste de Obsolência do Modelo.** O modelo é definido como obsoleto se o modelo treinado não incluir dados atualizados e / ou não atender aos requisitos de impacto nos negócios. O Teste de Obsolência tem como objetivo identificar se o modelo continua hábil a estar em produção com o decorrer do tempo.

Ainda, Testes Unitários devem estar presentes em todo o escopo.

### Teste em Infraestrutura do Modelo

Os Testes em Infraestrutura do Modelo visam garantir a estabilidade, corretude e disponibilidade do modelo em produção. Alguns exemplos:

- **Teste de Reprodutibilidade.** O pipeline de construção dos modelos de ML deve ser reproduzí(tí)vel: O que significa que a construção do modelo de ML nos mesmos dados deve produzir modelos de ML idênticos.
- **Teste de Carga e Estresse.** Garantir que o modelo é capaz de continuar executando independente da carga de dado, seja em um ambiente de desenvolvimento ou produção.
- **Teste de Desempenho do Modelo em Desenvolvimento e Produção.** Garantir que o desempenho do modelo no ambiente de desenvolvimento seja semelhante no ambiente de produção.
- **Testes de Integração.** Garantir que todo o pipeline, de todos os três artefatos, devem estar com a integração apropriadamente testada.

### Leitura Adicional

-   [Chapter 7: Model Evaluation - Burkov, Andriy. *Machine Learning Engineering*. True Positive Incorporated, 2020](http://www.mlebook.com/)
-   [Chapter 2: Create a Plan - Ameisen, Emmanuel. *Building Machine Learning Powered Applications*. O’Reilly Media, 2020.](https://www.amazon.com.br/Building-Machine-Learning-Powered-Applications/dp/149204511X)
-   [Chapter 3: Build Your First End-to-End Pipeline - Ameisen, Emmanuel. *Building Machine Learning Powered Applications*. O’Reilly Media, 2020.](https://www.amazon.com.br/Building-Machine-Learning-Powered-Applications/dp/149204511X)
-   [Chapter 6: Debug Your ML Problems - Ameisen, Emmanuel. *Building Machine Learning Powered Applications*. O’Reilly Media, 2020.](https://www.amazon.com.br/Building-Machine-Learning-Powered-Applications/dp/149204511X)
-   [Chapter 10: Build Safeguards for Models - Ameisen, Emmanuel. *Building Machine Learning Powered Applications*. O’Reilly Media, 2020.](https://www.amazon.com.br/Building-Machine-Learning-Powered-Applications/dp/149204511X)
-   [Chapter 11: Monitor and Update Models - Ameisen, Emmanuel. *Building Machine Learning Powered Applications*. O’Reilly Media, 2020.](https://www.amazon.com.br/Building-Machine-Learning-Powered-Applications/dp/149204511X)
-   Chapter 3.x: Test the Model
-   Chapter 5.x: Common Techniques for Machine Learning Experimentation
-   Chapter 9.x: Desirable Qualities in a Production-Level Serving System

-   [The ML Test Score: A Rubric for ML Production Readiness and Technical Debt Reduction](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/aad9f93b86b7addfea4c419b9100c6cdd26cacea.pdf)

## Qualidade \#4: Automação

O objetivo da automação é:

- Automatizar todos os pipelines do fluxo de trabalho de ML, incluindo Pipeline de Dados, Modelo e Código, de forma que todo o fluxo ocorra sem qualquer intervenção manual.

Em linhas gerais, um projeto de ML possui três níveis de automação:

1. **Processo Manual.** Processo típico de Ciência de Dados, onde cada etapa do pipeline, desde a preparação dos dados até a construção do modelo é executada de forma manual através de Rapid Application Development (RAD) tools, tal como Jupyter Notebooks.
   - O Processo Manual é caracterizado pela natureza experimental e iterativa.
2. **ML Pipeline Automatizado.** Estágio onde todo o processo de construção e validação do modelo é executado de forma automática à medida que novos dados são disponibilizados.
   - Neste estágio, também é possível a automatização tarefas de validação tanto de dados quanto de modelos.
3. **CI/CD Pipeline.** Estágio onde todo o fluxo de trabaho de ML, desde a aquisição até o deployment do modelo é executado através de sistemas CI/CD.
   - Neste estágio, diferente do anterior, também criamos, testamos e implantamos automaticamente os pipelines de cada um dos artefatos presentes: **Dado**, **Modelo** e **Código.**

Note que aqui, o uso de CI/CD aplica-se não apenas ao código, mas também a Dados e Modelos. Assim,

- **Continuous Integration (CI).** Além do teste e validação de código, também há testes e validação para dados e modelos.
- **Continuous Delivery (CD).** Entrega contínua de um pipeline de treinamento de ML que automaticamente retreina um modelo e executa seu deploy.

Ao mesmo tempo, também há necessidade de novas estratégias de [continuidade], tais como:

- **Continuous Training (CT)**. Re-treinamento e re-deployment de modelo executados continuamente de forma automática.
- **Continuous Monitoring (CM).** Monitoramento tanto dos dados quanto modelo em produção através de métricas que expressam o objetivo do negócio.

### Leitura Adicional

-   [Chapter 11: Monitor and Update Models - Ameisen, Emmanuel. *Building Machine Learning Powered Applications*. O’Reilly Media, 2020.](https://www.amazon.com.br/Building-Machine-Learning-Powered-Applications/dp/149204511X)

-   [MLOps: Continuous delivery and automation pipelines in machine learning](https://cloud.google.com/solutions/machine-learning/mlops-continuous-delivery-and-automation-pipelines-in-machine-learning#top_of_page)

## Qualidade \#5: Monitoramento

Depois que o modelo de ML foi implantado, **ele precisa ser monitorado para garantir que o modelo de ML funcione conforme o esperado.**

Algumas ações que podem ser tomadas são:

- **Monitorar invariantes de dados em treinamento e fornecimento de entradas.** 
  - Alertar se os dados não corresponderem ao esquema que foi especificado na etapa de treinamento.
- **Monitorar a estabilidade numérica do modelo de ML.**
  * Alertar no caso de ocorrências de NaNs ou valores infinitos.
- **Monitorar a perfomance computacional do sistema de ML.**
  - Mensurar a performance da versão de cada componente de dado, modelo e código.
  - Coletar o uso do sistema através de métricas como uso de GPU, Memória, Processador, Disco, Tráfego de Rede, etc (Note que essas métricas são muito úteis para *estimativas de custos de nuvem*)
- **Monitorar obsolescência do sistema e modelo de ML.**
  - Mensurar o decaímento de performance do modelo.
  - Mensurar o envisamento (estatístico) das predições e inferências.

![image-20210211164928195](assets/img/image-20210211164928195.png)

### Leitura Adicional

-   [Chapter 11: Monitor and Update Models - Ameisen, Emmanuel. *Building Machine Learning Powered Applications*. O’Reilly Media, 2020.](https://www.amazon.com.br/Building-Machine-Learning-Powered-Applications/dp/149204511X)
-   [Chapter 9.4: Model Monitoring - Burkov, Andriy. *Machine Learning Engineering*. True Positive Incorporated, 2020](http://www.mlebook.com/)
-   [The ML Test Score: A Rubric for ML Production Readiness and Technical Debt Reduction](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/aad9f93b86b7addfea4c419b9100c6cdd26cacea.pdf)
-   Chapter 9.x: Desirable Qualities in a Production-Level Serving System

## Qualidade \#6: Alta Disponibilidade

Alta disponibilidade é a capacidade do sistema (ou serviço) de estar disponível praticamente o tempo todo.

- Afinal, usuários não vão usar e confiar em uma aplicação instável que está indisponível frequentemente. 

Para a maioria de *players* sérios (e.g Amazon, Google Facebook), a disponibilidade de uma aplicação é mensurada termos de "Números de Nove", onde cinco 9s é considerado o santo-graal.

- Qualquer medida menor que três 9s é considerada inviável para um sistema em produção de alta qualidade.

![image-20210218102836811](assets/img/image-20210218102836811.png)

### Leitura Adicional

-   Chapter 9.x: Desirable Qualities in a Production-Level Serving System

## Qualidade \#7: Escalabilidade (Horizontal)

O tráfego gerenciado por serviços de produção quase nunca é uniforme em um certo período. Por exemplo:

- Netflix normalmente experimenta um aumento no tráfego entre tarde do dia e tarde da noite.
- Amazon experimenta muito mais tráfego na Black Friday e durante a época de Natal.

Portanto, construir um sistema escalável e capaz de lidar com muitas requisições em diferentes situações é fundamental.

### Leitura Adicional

-   Chapter 9.x: Desirable Qualities in a Production-Level Serving System

## Qualidade \#8: Baixa Latência

De acordo com um estudo (infográfico) publicado pela Amazon em 2008:

- Cada aumento de 100ms na latência  resultou em uma perda de lucro de 1%.
  - Em valores absolutos, o atraso de um segundo no carregamento do site causou uma perda de USD 16 bilhões!

Já a [Google descobriu que](https://www.thinkwithgoogle.com/marketing-strategies/app-and-mobile/mobile-page-speed-new-industry-benchmarks/):

- Uma latência de 500ms em sites móveis resultou em uma queda de tráfego de 20%
  - Logo,  uma redução de 20% nos anúncios de oportunidade de veiculação.
- Se uma página da web leva mais de três segundos para carregar em um smartphone, 53% dos usuários a abandonam.

Portanto, tempo é dinheiro e aplicações de ML também devem contar com baixa latência.

- Note ainda que a latência pode aumentar se a aplicação não for escalada para fornecer recursos adequados. Assim, latência, alta disponibilidade e escalabilidade estão interligadas.

### Leitura Adicional

-   Chapter 9.x: Desirable Qualities in a Production-Level Serving System

## Qualidade \#9: Disponibilidade Geográfica

> TODO

### Leitura Adicional

-   Chapter 9.x: Desirable Qualities in a Production-Level Serving System

## Qualidade \#10: Tolerância à Falhas

> TODO

-   Chapter 9.x: Desirable Qualities in a Production-Level Serving System

## Qualidade \#11: Segurança

Um modelo implantado em execução em uma estrutura de serviços pode apresentar, naturalmente, vários problemas de segurança que variam de falhas de baixo nível a engenharia social.

Com machine learning, há uma nova gama de ameaças potenciais, como, por exemplo:

- Um invasor pode fornecer dados maliciosos projetados para fazer com que o modelo cometa um erro.

Um exemplo moderno de problema de segurança do modelo de aprendizado de máquina é um ataque adversário em redes neurais profundas:

- Dado um modelo de classificação de imagens, o atacante pode aplicar modificações na imagem que podem parecer pequenas ou mesmo invisíveis para um olho humano, mas que muda drasticamente a inferência do modelo.

- Invasores podem utilizar tais estratégias para benefícios próprios.

Outro exemplo de segurança é se o invasor tiver acesso aos dados de treinamento:

- Neste caso, ele pode injetar dados projetados para gerar inferências desejadas.
- Esse tipo de ataque é tradicionalmente conhecido como **Data Poison Attack**.

> *Um exemplo famoso é o chatbot do Twitter lançado pela Microsoft em 2016.* 
>
> *Apenas algumas horas após o lançamento, o bot começou a postar tweets ofensivos. Isso foi causado pelo bot se adaptando à sua entrada; ao perceber que alguns usuários enviaram uma grande quantidade de conteúdo ofensivo, o bot começou a se replicar.*
>
> *Em tese, um ataque de envenenamento pode ocorrer em decorrência de uma intrusão ou mesmo, de forma mais sofisticada, por meio de modelos pré-treinados.* 
>
> *Na prática, entretanto, devemos nos preocupar principalmente com os dados coletados de fontes de dados facilmente manipuláveis. Os tweets enviados para uma conta específica são um exemplo particularmente claro.*

## Qualidade \#12:  Arquitetura de Baixo Acoplamento

> TODO

# Resumo de Qualidades e Boas Práticas em Projetos de ML

<table>
<thead>
  <tr>
    <th>Qualidades</th>
    <th>Dado</th>
    <th>Modelo</th>
    <th>Código</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>Versionamento</td>
    <td>1) Data preparation pipelines<br>2) Features store<br>3) Datasets<br>4) Metadata</td>
    <td>1) ML model training pipeline<br>2) ML model (object)<br>3) Hyperparameters<br>4) Experiment tracking</td>
    <td>1) Application code<br>2) Configurations</td>
  </tr>
  <tr>
    <td>Testing</td>
    <td>1) Data Validation (error detection)<br>2) Feature creation unit testing</td>
    <td>1) Model specification is unit tested<br>2) ML model training pipeline is integration tested<br>3) ML model is validated before being operationalized<br>4) ML model staleness test (in production)<br>5) Testing ML model relevance and correctness<br>6) Testing non-functional requirements (security, fairness, interpretability)</td>
    <td>1) Unit testing<br>2) Integration testing for the end-to-end pipeline</td>
  </tr>
  <tr>
    <td>Automation</td>
    <td>1) Data transformation<br>2) Feature creation and manipulation</td>
    <td>1) Data engineering pipeline<br>2) ML model training pipeline<br>3) Hyperparameter/Parameter selection</td>
    <td>1) ML model deployment with CI/CD<br>2) Application build</td>
  </tr>
  <tr>
    <td>Reproducibility</td>
    <td>1) Backup data<br>2) Data versioning<br>3) Extract metadata<br>4) Versioning of feature engineering</td>
    <td>1) Hyperparameter tuning is identical between dev and prod<br>2) The order of features is the same<br>3) Ensemble learning: the combination of ML models is same<br>4) The model pseudo-code is documented</td>
    <td>1) Versions of all dependencies in dev and prod are identical<br>2) Same technical stack for dev and production environments<br>3) Reproducing results by providing container images or virtual machines</td>
  </tr>
  <tr>
    <td>Deployment</td>
    <td>1) Feature store is used in dev and prod environments</td>
    <td>1) Containerization of the ML stack<br>2) REST API<br>3) On-premise, cloud, or edge</td>
    <td>1) On-premise, cloud, or edge</td>
  </tr>
  <tr>
    <td>Monitoring</td>
    <td>1) Data distribution changes (training vs. serving data)<br>2) Training vs serving features</td>
    <td>1) ML model decay 2) Numerical stability 3) Computational performance of the ML model</td>
    <td>1) Predictive quality of the application on serving data</td>
  </tr>
</tbody>
</table>

Em conjunto das qualidades, um conjunto de melhores práticas que tendem a ajudar a reduzir o “débito técnico”do projeto de ML:

<table>
<thead>
  <tr>
    <th>MLOps Best Practices</th>
    <th>Data</th>
    <th>ML Model</th>
    <th>Code</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>Documentation</td>
    <td>1) Data sources<br>2) Decisions, how/where to get data<br>3) Labelling methods</td>
    <td>1) Model selection criteria<br>2) Design of experiments<br>3) Model pseudo-code</td>
    <td>1) Deployment process<br>2) How to run locally</td>
  </tr>
  <tr>
    <td>Project Structure</td>
    <td>1) Data folder for raw and processed data<br>2) A folder for data engineering pipeline<br>3) Test folder for data engineering methods</td>
    <td>1) A folder that contains the trained model<br>2) A folder for notebooks<br>3) A folder for feature engineering<br>4) A folder for ML model engineering</td>
    <td>1) A folder for bash/shell scripts<br>2) A folder for tests<br>3) A folder for deployment files (e.g Docker files)</td>
  </tr>
</tbody>
</table>

## Leitura Adicional

- [Hidden Technical Debt in Machine Learning Systems](https://web.kaust.edu.sa/Faculty/MarcoCanini/classes/CS290E/F19/papers/tech-debt.pdf)

