# Federated Learning

## Introdução

Hybrid-Serving, mais conhecido como Federated Learning é uma forma relativamente nova, porém, em alta, de servir modelos aos usuários, principalmente de dispositivos móveis.

Basicamente, trata-se de estratégia onde um modelo genérico é disponibilizado para uma grande quantidade de usuários e, então, cada usuário passa a ter um modelo específico para si que é retreinado (ou especializado) em seus dados.

Mais precisamente:

- Há um modelo genérico no lado do servidor (ou server-side) pré-treinado em dados do mundo real.
  - Tal modelo é usado como ponto de partida para novos usuários da aplicação.

- Do lado dos usuários (ou user-side), há modelos especializados e únicos para cada usuário (que partem do modelo genérico no server-side), de forma que o retreinamento (i.e. especialização) destes modelos para este usuário ocorre no dispositivo do usuário.
- Uma vez especializados, os (hiper)parâmetros de cada modelo são enviados para o servidor. Assim, o modelo do servidor é ajustado a fim de que as tendências reais de toda a comunidade de usuários sejam cobertas pelo modelo e, então, este novo modelo passa a ser o novo modelo inicial para todos os usuários.
  - Para que não haja desvantagens aos usuários, o processo de atualização dos modelos ocorre somente quando o aparelho está ocioso, conectado à uma rede WiFi e carregando.
  - Ainda, os testes são feitos nos dispositivos. Portanto, o modelo recém-adotado do servidor é enviado aos dispositivos e testado quanto à funcionalidade.

A principal vantagem dessa abordagem é que:

- Os dados pessoais necessários para o treinamento (e teste) nunca saem do domínio do usuario. Enquanto que ainda assim é possível atualizar os modelos com base nas tendências da comunidade.
  - Em outras palavras, é possível treinar modelos de alta precisão sem ter que armazenar toneladas de dados (provavelmente pessoais) na nuvem.

Porém, a grande desvantagem é que a especialização do modelo é custosa para usuários. Afinal, os modelos de ML são desenvolvidos com conjuntos de dados grandes e homogêneos em um hardware poderoso.

## Referências

- [Three Levels of ML Software](https://ml-ops.org/content/three-levels-of-ml-software#model-machine-learning-pipelines)
