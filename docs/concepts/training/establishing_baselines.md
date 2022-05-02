# Estabelecendo Baselines

## Introdução

No contexto de projetos de ML, um **baseline é um modelo simples mas que alcança resultados razoáveis no problema que desejamos resolver** e cujos resultados são utilizados como ponto de partida (ou seja, define um desempenho mínimo) para o desempenho que queremos alcançar e, consequentemente, a construção de modelos mais complexos.

!!! nota "Nota"
    Por simples, queremos dizer que é um modelo fácil de treinar, implantar e que não exige grande expertise. No geral, modelos assim também são fáceis de explicar e analisar.

De fato, definir um ponto de partida de performance **(baseline level performance)** é uma prática muito importante para a evolução de desempenho de um modelo. Afinal, este ponto de partida nos ajuda a definir:

- Qual o desempenho mínimo aceitável para o modelo entrar em produção e o quão factível esse desempenho é.
- Quais processamentos precisamos fazer nos dados a fim de melhorar sua qualidade.
- O quanto de recurso computacional (e humano) será necessário para construir o modelo desejado.

Por exemplo:

- **Tarefas de Regressão.** Regressão Linear.
- **Tarefas de Classificação em Dados Estruturados.** K-Nearest Neighbors.
- **Tarefas de Visão Computacional ou NLP.** Modelos Pré-treinados.

## Tipos de Baseline

[Emmanuel Ameisen](#referencias) define quatro tipos de baseline de performance:

- **Performance Trivialmente Alcançável.** Desempenho obtido da maneira mais simples possível. É esperado que qualquer modelo ultrapasse esse desempenho.
- **Performance de Nível Humano (HLP, Human Level Performance).** Desempenho obtido por humanos na tarefa em questão. Quando um modelo ultrapassa esse desempenho, então é um bom candidato a entrar em produção. O uso de HLP é recomendável principalmente em tarefas que envolvam a classificação de dados não estruturados.
- **Performance Automatizada Razoável.** Desempenho obtido por um modelo consideravelmente simples. Esse desempenho nos ajuda a julgar se um modelo complexo está performando bem o suficiente em relação a sua complexidade de implementação.
- **Performance Mínima para Deployment.** Desempenho mínimo necessário para que um modelo possa entrar em produção.

Qual tipo de baseline será adotada depende do domínio do problema e objetivos. Contudo, é aconselhável sempre definir uma performance mínima para deployment, assim como o desempenho obtido por um modelo simples (performance automatizada razoável). Se estivermos trabalhando com dados não-estruturados, podemos incluir HLP.

## Referências

- [Topic 3 - Baselines](https://blog.ml.cmu.edu/2020/08/31/3-baselines/)
- [Always Start with a Stupid Model, No Exceptions by Emmanuel Ameisen](https://blog.insightdatascience.com/always-start-with-a-stupid-model-no-exceptions-3a22314b9aaa)
- [Introduction to Machine Learning in Production by Coursera](https://www.coursera.org/learn/introduction-to-machine-learning-in-production)