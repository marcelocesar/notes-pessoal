# DOCUMENTO DE PADRÕES DE DESENVOLVIMENTO CNART

## Nomenclatura de variáveis, métodos, classes e atributos de tabela do B.D.

Os nomes de variáveis, classes ou métodos bem como os nomes dos atributos em banco de dados devem ser definidos no idioma inglês, buscando sempre uma representação clara do domínio que aquilo está representando, por exemplo, em uma situação que temos no sistema uma supervisor(supervisor) e um gerente(manager) seus nomes de classes consequentemente devem ser supervisor e manager estando errado o uso de outras palavras para representar essas entidades como user e masterUser.

### Estilo de junção de palavras

* Fica determinado o uso de **camelCase** para variáveis e métodos no código -- Ex: varibleName, nameDocument, clientName
* Para o caso de nome de classes deve-se usar o **UpperCase** -- Ex: Client, Person, User, UserService, UserRepository
* Ao se tratar do banco de dados a junção de palavras é feito através do **snake_case** -- Ex: canc_clients, canc_users, canc_persons
* Para o uso de variáveis estáticas, deve-se utilizar o padrão **UPPERUPPER** -- DOCUMENT_PDF, PATH,

### Pluralização de palavras, prefixo e sufixo de nomes

Deve-se seguir as seguintes recomendações para determinar uma nomenclatura:

* Classes Entidades, Model -\> Seus nomes devem ser sempre no singular, a tabela de mapeamento no banco de dados deve ser no plural.
* Métodos -\> Seus nomes devem ser sempre no imperativo.
* Variáveis -\> Caso a mesma for um objeto, seja ele primitivo ou não, deve ser nomeado de maneira a usar o singular, caso a variável seja uma coleção, então usa-se o plural.
* Classes do Tipo Controller -\> Essas classes devem receber o sufixo **Controller** e descrever com clareza o domínio a qual ela pertence no plural ex:. PersonsController.
* Classes do Tipo Service -\> Essas classes devem receber o sufixo **Service** e descrever com clareza o domínio a qual ela pertence no plural ex:. PersonsService.
* Classes do Tipo Repository -\> Essas classes devem receber o sufixo **Repository** e descrever com clareza o domínio a qual ela pertence no plural ex:. PersonsRepository.
* Interface -\> As Interfaces devem possuir o prefixo **I** ex:. IGateOpener.
* Enum -\> Os Enums devem possuir o prefixo **En** e seu nome é no plural ex:. EnDaysAvailable.
* DTO -\> A classes do tipo DTO devem descrever seu **"motivo"** e conter o sufixo **DTO**, ex:. CreateCargoReleaseOperationDTO. Todo e qualquer DTO deve ser Records.
* Util -\> A classes do tipo Util devem descrever seu **"motivo"** e conter o sufixo **Util**, ex:. LogUtil.

### Anotações SWAGGER

* Todo e qualquer DTO, deverá possuir as anotações correspondente a lib de documentação (seja swagger ou outra), para que quando acessado, exemplificar e explicar o que é cada DTO e cada atributo

## Branches, Commits e Merges

Antes de iniciar o desenvolvimento deve-se a partir da branch develop criar uma nova branch sua nomenclatura seguir o seguinte modelo:

* Caso o que vá desenvolver seja uma **feature** nova o nome deve ser feature/task-\[codigo\]-\[descricao-breve\], ex:. feature/task-1549-solicitacao-de-abertura-gate.
* Caso o que vá fazer seja referente a um bug deve-se usar hotfix/task-\[codigo\]-\[descricao-breve\], ex:. hotfix/task-9558-retorno-shipData-nulo.

Para os commits deve-se usar o padrão de **conventional commits**, fazendo diversos commits durante o desenvolvimento de forma a categorizar as etapas e deixar claro o conteúdo de cada commit, para entender o que são os conventional commits de uma olhada aqui [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/).

Ao finalizar a tarefa deve-se fazer o push da branch para o repositório, o processo de criação do M.R. é automático, então deve-se informar um dos responsáveis pelo **code review** da criação desse M.R e aguardar aprovação ou recusa, em caso de recusa as correções devem ser feitas na mesma branch.

> Não deve-se dar squash nos commits da branch usada para desenvolver e nem no merge para a branch de develop, os responsáveis devem dar squash apenas nas demais branches.

## Boas Práticas

Para a análise do código no momento do code Review será levado em consideração além, dos processos acima citados também os conceitos a seguir:

* \[CleanCode\] - O uso dos ensinamentos do CleanCode será levado em conta no processo de desenvolvimento, devendo ter suas convenções aplicadas.
* \[SOLID\] - O uso do SOLID será levado em conta no processo de desenvolvimento, devendo ter seus acrônimos aplicados da melhor maneira possível.
* \[KISS\] - A complexidade do código desnecessária deve ser evitada a fim de manter a compreensão do código o mais simples possível
* \[YAGNI\] - O desenvolvimento de funções/métodos, classes ou variáveis sem uso não deve ser feito e deixado no código.
* \[DDD\] - Os conceitos de boa nomenclatura aplicados ao DDD serão verificados no C.R devendo as mesmas terem coesão e clareza.
* \[TESTES\] - Testes serão explanados em maiores detalhes no próximo tópico, mas a cobertura de testes unitário deve ser de no mínimo 80%.
* \[COMENTÁRIOS\] - comentários de explicação do funcionamento de trecho de códigos e lógica devem ser evitados ao máximo, deixando essa responsabilidade a legibilidade do seu código, já os comentários de documentação feitos sobre métodos e classes são necessárias, mais a frente nos próximos tópicos terá mais detalhes sobre comentários.
* \[Design Patterns\] - A aplicação correta de design patterns também será levado em consideração.
* \[Migrations\] - Toda a aplicação deve conter migration cobrindo todas as alterações necessárias para o banco de dados, inclusive seeds caso seja necessário.

## Testes

Para garantir uma melhor estabilidade do sistema e uma boa qualidade de código testes devem ser implementados nos micro serviços, para a aprovação de uma branch a aplicação deve ter no mínimo 80%.

Essa cobertura em testes unitários está relacionado intrinsecamente a camada de service, onde deve estar as regras de negócio da aplicação, é a cobertura dela que é levada em conta para o percentual de aceitação.

Os nomes de métodos de teste devem ser bem descritivos podendo ser mais extensos do que os usados no sistema propriamente dito, para não se preocupar com implementações abstrata de banco de dados para as aplicações desenvolvidas em java pode-se usar o banco de dados H2, limpando o mesmo a cada novo teste, para evitar resíduos de outros teste podendo assim causar falso positivos.

## Comentários

Os comentários para explicar a lógica de um método ou o funcionamento de um trecho de código devem ser evitados, deixando essa responsabilidade para a legibilidade de seu código.

Já os comentários documentais são necessários e obrigatórios, devendo comentar a classe com seu propósito e o autor, o mesmo vale para os métodos que devem dizer o autor qual, a função o que ele espera na entrada, o que ele espera na saída e se há exceções que podem vir a acontecer. Esses comentários auxiliam na criação de documentação e também no uso de código entre colaboradores sem a necessidade de ler o código do outro, aumentando a produtividade.

## Internacionalização

A internacionalização deve-se estar presente ao estourar uma exceção, pois o frontend utiliza-se de um padrão bem definido para trabalhar em cima dessa exceção.


> {"enTag":"COMMON.ERRORS.REQUEST_PROBLEM","errorMessage":"Houve um problema na sua requisição! Por favor, entre em contato com o nosso time de Experiência do Cliente"}

O front precisará apenas dessas informações, onde o atributo enTag sinaliza a internacionalização (dependendo do idioma selecionado) e o erroMessage -- a mensagem do erro bem específica do problema ocorrido

## Migrações

Todo e qualquer microserviço que tenha entidade, deverá possuir as migrations. O tipo de DDL por padrão deverá ser validate.

## Restrito - Má prática

Essa seção descreve o que não ficar utilizando no código, pois é considerado má prática ou existe caminho melhor.

#### Utilização do **printStackTrace()** em excesso.

O grafana já trabalha muito bem com a captura de erros através do log.error(), optar pela utilização dele. Ao printar a exceção, sempre utilizar o ex.getMessage(). **Exemplo de utilização:**

> ```
>  	log.error("{} Houve um erro ao chamar o serviço para permitir agendamentos por gkey. Error: {}", Util.LOG_PREFIX, ex.getMessage());
> ```

#### Métodos extensos

Se o método possuir mais do que 20 linhas, repense em refatorá-lo.

#### Classes extensas

Se a classe possuir mais do que 150 linhas, repense em refatorá-lo.

#### Repetição de trecho de código

Por padrão só deve existir uma única fonte de verdade, se tiver repetição de trecho de código no projeto, repense em refatorar. Consulte Padrões de projeto, DRY.

#### Nomes de métodos e variáveis

Os nomes dos métodos e variáveis precisam fazer sentido, caso tenha o método voar e dentro dele tenha a lógica para nadar, repense em refatorar.

#### Criação geral dos endpoints

- Nomes limpos e estruturados (ex: /users /products)
- Usar os verbos HTTP que fazem sentido (GET, POST, PUT, DELETE)
  * GET para recuperar dados
  * POST para criar dados
  * PUT para atualizar dados existentes
  * DELETE para apagar dados
- Comunicação atráves de arquivos JSON
- Versionamento caso necessário
  * /v1/users
  * /v2/users
- Paginação sempre para grande quantidades de registros
- Filtragem
  * Todo GET principal da entidade será filtrado


