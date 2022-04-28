# Model as a Dependency (MaaD)

## Introdução
A estratégia MaaD é bem parecida com *static deployment*. Contudo, ao invés de empacotarmos o modelo como parte da aplicação, ele é definido como uma dependência e empacotado de forma que seja possível atualizá-lo individualmente.

Por exemplo, podemos empacotar um modelo como:

- Um pacote instalável (e.g. um pacote Python) definido como uma dependência da aplicação.
- Arquivo serializado que é importado (e deserializado) pela aplicação durante sua inicialização ou em tempo de execução
- Arquivo com os parâmetros do modelo que pode ser utilizado para atualizá-lo.

Neste cenário, para utilizar o modelo, a aplicação só precisa invocar um método de inferência deste.

## Vantagens x Desvantagens

### Vantagens

- Não é preciso enviar dados do usuário para um servidor (ou qualquer recurso) externo ao dispositivo do usuário
- O modelo estará sempre disponível, mesmo se o usuário estiver offline.
- Caso seja um modelo simples, sem necessidade de computações rápidas ou pesadas, o tempo de inferência é muito mais rápido quando comparado com qualquer outra estratégia
- O modelo pode ser atualizado sem a necessidade de atualizar toda a aplicação

### Desvantagens

- Executar o monitoramento de performance do modelo é extremamente difícil
- Se a computação do modelo for cara, executá-la no dispositivo do usuário pode ser ineficiente ou prejudicar a experiência do usuário
- Dependendo da estratégia de empacotamento do modelo, técnicas de engenharia reversa podem ser aplicadas para manipular o resultado das inferências

## Exemplos