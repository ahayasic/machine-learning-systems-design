# Data-Centric AI

## Introdução

O desenvolvimento de modelos é um processo altamente iterativo. Em resumo, a partir de um conjunto de dados, escolhemos um algoritmo de aprendizado e selecionamos o melhor conjunto de hiperparâmetros para este algoritmo. Em seguida, fazemos uma análise de erros (preferencialmente, criteriosa) e, caso necessário, reexecutamos todo o processo a fim de encontrar um modelo que passe pela análise de erros.

De acordo com Andrew Ng, há duas maneiras que podemos adotar para desenvolver modelos de Inteligência Artificial (IA):

- Model-centric (centrado no modelo). Estratégia onde dado um conjunto de dados fixo, aplicamos diferentes algoritmos e estratégias para encontrar o melhor modelo para aqueles dados e, consequentemente, o melhor modelo para a tarefa em questão.
- Data-centric (centrado no dado). Estratégia onde dado um conjunto de dados fixo, aplicamos diferentes estratégias de processamento de dados a fim de torná-los o mais representativos possível, de modo que qualquer algoritmo minimamente complexo seja capaz de aprendê-los e assim performar bem na tarefa em questão.

Embora o uso do Model-centric seja muito comum na academia, na indústria é fortemente recomendado (principalmente por Andrew Ng) o uso da abordagem Data-centric. Mais precisamente, dado um problema resolvível por meio de ML, devemos buscar aumentar ao máximo possível a qualidade dos dados, tornando-os "bom dados" (good data), sendo "good data":

- Definidos consistentemente (a definição de cada categoria é não-ambíguia)
- Todos os casos importantes são cobertos (cobertura de todas as possibilidades de entrada)
- Feedback constante de alterações (concept drift e data drift são monitorados)
- Quantidade de dados apropriada.

## Referências

- [A Chat with Andrew on MLOps: From Model-centric to Data-centric AI](https://www.youtube.com/watch?v=06-AZXmwHjo)
- [Data-centric AI Community](https://github.com/HazyResearch/data-centric-ai)