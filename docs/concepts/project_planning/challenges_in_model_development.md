# Desafios no Desenvolvimento de Modelos

## Introdução

O desenvolvimento de modelos é uma tarefa consideravelmente complexa. Porém, não necessariamente por conta do modelo em si (ou seja, qual o melhor modelo para este conjunto de dados?), mas sim por conta dos dados e a combinação entre métricas de desenvolvimento e métricas de negócios.

## Principais Problemas

De acordo com Andrew Ng, os principais problemas enfretados no desenvolvimento (e manutenção) de modelos de IA são:

### Desempenho no Desenvolvimento x Em Produção

O erro do modelo nas partições de validação e teste, geralmente, é pouco informativo quanto à performance do modelo. De fato, a performance real de um modelo aparece somente quando este vai para produção, pois apenas a partir deste momento é que o modelo está exposto a dados reais.

Consequentemente, são necessárias estratégias para atualizar rapidamente o modelo em produção, tal como experimentá-lo em produção antes de que seja disponibilizado totalmente para todos os usuários.

### Métrica de Desenvolvimento x Métrica de Negócios

Métricas de desenvolvimento são métricas comuns utilizadas para testar a performance de um modelo durante seu desenvolvimento, como: acurácia, precisão, revocação, etc.

Já métricas de negócios são métricas que indicam precisamente o quão bem (ou eficiente) um problema em questão está sendo resolvido através de uma estratégia específica.

No geral, tais métricas acabam sendo KPIs. Ou seja, indicadores-chaves de desempenho que medem quantitativamente o quão eficiente está sendo uma estratégia específica (no caso, modelos de IA) em agregar valor ao negócio.

Por serem medirem aspectos totalmente diferentes, nem sempre o modelo com melhor acurácia é aquele que possui melhor KPI. Consequentemente, desenvolver um modelo que atenda com sucesso ambas métricas é uma tarefa consideravelmente complexa.

### Dados Desbalanceados

Dados desbalanceados é um dos problemas mais comuns no desenvolvimento de modelos. Desde classes desbalanceadas até distribuições consideravelmente enviesadas, é muito difícil tratar conjuntos de dados desbalanceados, pois há muita informação sobre certo "pedaço" do conjunto de dados, enquanto há pouca informação sobre qualquer outro "pedaço".

Embora existam técnicas reamostragem a fim de minimizar os impactos do desbalanceamento, no geral elas são incapazes de produzir modelos confiáveis. Afinal, como podemos garantir que:

- O modelo desfavoreça pessoas por conta de característica étnico-raciais?
- As classes minoritárias representam corretamente o mundo real?
- Os dados não possuem tendenciosidades implícitas?

De fato, o principal problema da construção de modelos de IA na indústria são os dados.

### Concept Drift e Data Drift

!!! info "Work in progress"