# Rastreamento e Versionamento de Experimentos

## Introdução

Uma prática extremamente importante nos projetos de ML é o rastreamento dos experimentos, tal como o versionamento do código e artefatos produzidos pelo experimento.

Ao rastrear e versionar os experimentos, conseguimos:

- Acompanhar os resultados de diferentes iterações de um experimento, visto que cada iteração retornará valores de métricas diferentes.
- Acompanhar os resultados alcançados por cada (hiper)parâmetro.
- Registrar, para cada iteração, o código do experimento, assim como os artefatos (i.e. modelo e dados utilizados para construir o modelo).
- Manter rastreada a exata versão do código de uma iteração, tal como o dado e modelo resultante desta versão.

Basicamente, o rastreio de experimentos é a prática de salvar (i.e. "loggar") todas as informações importantes relacionadas aos dados, modelo e código de cada iteração do experimento executado, de forma que seja possível se ter um conhecimento completo de cada informação gerada e o controle total sobre todas as modificações realizadas.

Por exemplo, ao desenvolvermos um modelo, podemos querer rastrear (e versionar) em cada iteração de um experimento:

- Scripts (código-fonte) usado.
- Arquivos de configuração.
- Dados e metadados utilizados para o treinamento, validação e teste.
- Parâmetros e hiperparâmetros do modelo.
- Resultados das métricas de avaliação do modelo.
- Resultados das métricas de performance da aplicação.

Uma vez tendo essas informações, podemos:

- Comparar os diferentes resultados alcançados
- Identificar o impacto de cada alteração no resultado final
- Identificar problemas de performance do sistema, etc.

Portanto, a prática de rastrear os experimentos é fundamental tanto para a reprodutiblidade (de fato, é a principal forma de alcançá-la) quanto para o desenvolvimento da aplicação em si.

Rastrear manualmente experimentos pode ser extremamente complexo. Por isso usamos ferramentas free e open-source disponíveis, como é o caso do MLflow Tracking.

## MLflow Tracking

O MLflow Tracking é uma ferramenta para loggar parâmetros, métricas, versões de código e artefatos, arquivos de dados, etc. Podemos loggar os experimentos através da API Python, REST API e CLI. Ainda, a ferramenta também fornece uma UI que nos permite visualizar e comparar os resultados.

O MLflow Tracking gira em torno do conceito de **runs**.

Uma *run* nada mais é que a execução de um pedaço de código (por isso, usaremos "run" e execução de forma intercambiável).

No caso, usamos tal comportamento para definirmos iterações de um experimento. Assim, cada run é uma execução de um experimento com um certo conjunto de parâmetros.

Para cada run são salvos:

- **`Code version`.** Hash do commit (Git) usado na execução (se o experimento estiver em um repositório).
- **`Start & End time`.** Hora de início e término da run.
- **`Source`.** Nome do arquivo (source code) de registro do experimento.

Ainda, são salvos outros metadados úteis para a ferramenta.

!!! tip "Dica"
    Se as execuções estiverem organizadas em um *MLflow Project*, também são registrados o URI do projeto e a versão do código-fonte.

Para acompanhar os experimento rastreados, utilize a UI invocando o web server:

```bash
$ mlflow ui
```

!!! warning "Atenção"
    A UI deve ser invocada no mesmo diretório onde os dados dos experimentos são logados. Veja a seção [Como Execuções são Armazenadas](#como-execucoes-entidades-e-artefatos-sao-armazenados)


### Criando Experimentos

Uma vez que execuções são agrupadas em experimentos, para criá-los utilizamos o método `set_experiment()`.

- **`set_experiment(experiment_name)`.** Atribui o experimento passado por parâmetro como o experimento ativo. Caso o experimento não exista, então ele é criado.

```python
import mlflow

mlflow.set_experiment("Experiment #1")
```

### Criando Execuções

Uma vez definido o experimento, precisamos definir a iteração de execução do experimento a partir do qual as entidades (i.e. parâmetros, métricas, etc.) e artefatos (i.e. modelo e dados) serão logados.

Podemos definir iterações através da chamada simples dos métodos `mlflow.start_run()` e `mlflow.end_run()` ou usando `mlflow.start_run()` como um gerenciador de contexto.

- **`mlflow.start_run(run_id, experiment_id, run_name, nested, tags)`.** Caso exista uma execução ativa, então a retorna. Caso contrário, cria uma nova iteração.
- **`mlflow.end_run()`.** Encerra uma execução ativa, caso exista.

    ```python
    mlflow.start_run()
    mlflow.log_param("param", 1.0)
    mlflow.end_run()
    ```

Contudo, o uso mais recomendado dos métodos é usando gerenciadores de contexto.

```python
with mlflow.start_run() as run:
    mlflow.log_param("param", 1.0)
```

#### Parâmetros

- **`run_id`.** Executa a iteração sobre uma execução passada cujo identificador é `run_id` (UUID)
- **`experiment_id.`** Executa a iteração sob um experimento cujo identificador é `experiment_id`.
- **`run_name`.** Nome da execução (salvo na tag/arquivo `mlflow.runName`).

#### Boas Práticas

Sempre que o código referente a iteração de um experimento é executado, é criado um novo diretório (cujo nome é o `run_id`, na forma de UUID) onde as entidades e artefatos são armazenados.

Logo, é importante manter o `run_name` consistente, pois ao acessarmos a UI poderá haver várias execuções com o mesmo `run_name`.

### Loggando Entidades e Artefatos

Cada execução (i.e. run) é registrada na forma de um diretório. Dentro do diretório há um arquivo de metadados denominado `meta.yml` e quatro diretórios:

- **`params/`.** Diretório onde são armazenados os parâmetros logados.
- **`metrics/`.** Diretório onde são armazenadas as métricas logadas.
- **`artifacts/`.** Diretório onde são armazenados os artefatos.
- **`tags/`.** Diretório onde são armazenadas as tags.

No caso dos parâmetros e métricas, cada parâmetro indicado pelo argumento `key` é um arquivo de texto cujo conteúdo é a `key` e o respectivo valor definido.

Os métodos de logging mais importantes são:

- **`log_param(key, value)`.** Loga um parametro de nome `key` e valor `value`
- **`log_params(dict)`.** Semelhante a `log_param`, mas loga um conjunto de parâmetros organizados em um dicionário.

    !!! warning "Atenção!"
        Não é possível logar o mesmo parâmetro mais de uma vez usando o método `log_param`. Caso isso seja necessário, use `log_params`.

        Ainda, é recomendado o uso consistente dos métodos. Logo, opte por sempre usar um ou outro.

- **`log_metric(key, value, step)`.** Análogo ao `log_param` com a diferença do argumento `step`, que nos permite logar vários resultados para uma mesma métrica (assim, podemos registrar experimentos executados com Validação Cruzada, por exemplo).
- **`log_metrics(dict, step)`.** Análogo ao `log_metric` mas para conjuntos de métricas.
- **`log_artifact(local_path, artifact_path)`.**
- **`log_artifacts(local_dir, artifact_path)`.**

#### Loggando Modelos

!!! failure "TODO"


#### Boas Práticas

Por padrão, ao loggar modelos, apenas o binário do modelo e as dependências para sua execução são armazenadas.

Porém, é interessante manter junto ao modelo os dados utilizados para sua construção. Afinal, código, dado e modelo precisam estar **sempre** sincronizados.

Dessa forma, práticas que podemos adotar são:

1. **(Recomendada) Loggar os dados como artefatos.** A primeira e mais simples alternativa é salvar os dados como artefatos junto do modelo.

    ```python
    # [...]
    # df = pd.read_csv("path/to/dataset.csv")

    mlflow.log_artifact(local_path="path/to/dataset.csv")
    ```

    O problema dessa abordagem é que tanto as iterações quanto o armazenamento em si pode ficar custoso.

    Podemos amenizar o custo de armazenamento, ao loggarmos tanto dado quanto modelo em *objects storage* (e.g. Hadoop ou AWS S3).

    Contudo, o custo de upload do dado ainda tende a ser custoso.

2. **(Não recomendada) Usar uma ferramenta de versionamento de dado, tal como DVC.** Embora interessante a princípio, essa estratégia possui várias complicações*.

### Como Execuções (Entidades e Artefatos) são Armazenados

As execuções (runs) do MLflow Tracking podem ser armazenadas no sistema local de arquivos (como arquivos locais), bancos de dados compatíveis com SQLAlchemy ou remotamente para um serviço de tracking.

Já os artefatos podem ser armazenados tanto localmente quanto em diversos serviços de armazenamento de arquivos.

O MLflow utiliza dois componentes para o armazenamento dos dados:

- **Backend Store.** Armazena as entidades MLflow (metadados de execução, parâmetros, métricas, tags, notas, etc.)
- **Artifact Store.** Armazena os artefatos (arquivos, modelos, dados, imagens, objetos, etc.)

Para definirmos **como** e **onde** as execuções serão registradas, usamos o método `set_tracking_uri`.

#### Armazenando no Sistema de Arquivos Local

Ao utilizar o sistema de arquivos local, ambos os componentes backend store e artifact store armazenam os dados no diretório `./mlruns`. As interfaces para tal são:

- `LocalArtifactRepository` para armazenar os artefatos.
- `FileStore` para armazenar as entidades.

Assim, para cada experimento:

1. MLflow cria um diretório (cujo nome são números inteiros) dentro de `./mlruns` para armazenar as execuções. Por padrão, o MLflow sempre criará o diretório `0`. Este diretório é de propósito geral e utilizado para armazenar as execuções que não organizadas em experimentos.

2. Cada execução possui um diretório dentro da pasta do respectivo experimento onde são armazenados os artefatos e entidades. O nome do diretório de cada execução é o UUID da respectiva execução.


!!! note "Nota"
    Ao especificarmos um caminho para o sistema de arquivos local, devemos sempreutilizar a sintaxe: `file:///path/to/dir/mlflow/`.

    Isso porque caso a gente não adicione o diretório `mlflow` no final dasintaxe, não serão criados os diretórios `mlruns/` e nem `mlruns/0/`.Consequentemente:
        - A UI do MLflow não irá conseguir rastrear as execuções.
        - Não haverá onde adicionar execuções que não estiverem organizadas emexperimentos.

!!! warning "Atenção"
    Se a URI de rastreamento não for definida $-$ seja através da variável de ambiente `MLFLOW_TRACKING_URI` ou `set_tracking_uri` $-$ o MLflow automaticamente irá criar o diretório `mlruns/` na mesma pasta onde o script Python for chamado.

## Versionamento com Git e MLflow Tracking

Além do MLflow Tracking, também podemos utilizar o MLflow Project.

O MLflow Project é um formato de empacotamento de projetos orientados a dados de forma que sejam reutilizáveis e reprodutíveis.

Essencialmente, um projeto no formato MLflow Project é apenas um diretório ou repositório Git com os arquivos relacionados ao projeto e um arquivo adicionado denominado MLproject onde são definidas as configurações do projeto permitindo-o que seja executável.

Ainda, no caso de um projeto a partir de um repositório Git o MLflow associa a versão de cada código fonte a hash do último commit em que o código foi adicionado ou modificado.

Portanto, ao usar o Git com o MLflow Tracking podemos (sem necessariamente adicionar o arquivo MLproject, cerne do  MLflow Project) logar as entidades e artefatos de cada execução de um experimento de forma que o código, dado e modelos estejam sincronizados.


### Boas Práticas

Dado que a versão de cada código fonte (e consequentemente, dos artefatos relacionados) é definida com base na hash de um commit, o versionamento correto é responsabilidade total do usuário, o que pode ser sujeito a erros.

Por exemplo, podemos modificar o código de um experimento e executá-lo diversas vezes sem "commitá-lo". Com isso, várias iterações serão registradas para um mesmo arquivo de código fonte (assim como modelo e dado). Porém, embora todos tenham a mesma versão, o conteúdo de cada execução é diferente. Dessa forma, fica impossível identificar o código que gerou os artefatos da respectiva execução. 

Portanto, as práticas que podemos adotar são:

1. Sempre que o código do experimento for alterado, ele **deve** ser commitado antes de ser executado novamente.
2. Utilizar uma ferramenta para auto-commit (abordagem recomendada). Com isso, toda alteração permanecerá rastreada. Ao mesmo tempo, há um potencial de poluição do histórico de commits, visto que alterações pequenas ou pouco significantes também serão incluídas como commits individuais.
3. Considerar apenas a última execução.

    !!! note ""
        Ao optar por essa abordagem, caso a nova execução tenha resultados inferiores, não será possível reverter o código para a última versão (uma vez que ele não foi versionado)


