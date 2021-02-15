# DesSerCon

## Base A

Entendendo que nao precisa de tanto de performance mas sim de segurança dos dados eu escolheria o banco de dados PostgreSQL para armazenar a Base A.

Implementações da Base:
- Deve ser feito com autenticação e criptografia forte.
- Sendo os dados são sensíveis, acessos podem ser marcados no ElasticSerach para registra-los.
- Os retornos devem ser:
  - Completos se o propio usuário estiver pesquisando.
    - Além dos dados do usuário pode-se retornar como ele se encontra em relação a quantidade de dívidas das outras pessoas da sua cidade, estado, país
  - Apenas nome e quantidade de Dívidas se algum outro usuário logado em uma conta diferente da que está buscando fizer a busca para proteger os dados do cliente.

Modelagem do banco de dados:

Teremos umas 3 tabelas basicas: Divida, Usuario, Endereço

Divida:
Um id para identifica-lo em forma de hash md5
Data de criação, modificação e remoção
E o id do usuario associado a tabela usuario por FK

Usuario:
Um id para identifica-lo em forma de hash md5
Nome do usuario
Sobrenome do usuario
CPF
Data de criação, modificação e remoção
E o id do endereço associado a tabela endereço por FK

Endereço:
Um id para identifica-lo em forma de hash md5
Logradouro
Pais
Estado
Cidade
Data de criação, modificação e remoção

Todas as tabelas devem possuir data de criação, edição e remoção criadas de forma automatica quando fosse feita a operação.

Lista de tarefas:
- [ ] Criar banco de dados e migração
- [ ] Desenvolver a api
- [ ] Desenvolver testes unitários

## Base B

Entendendo que a banco de dados não deve sofrer com perdas de performance e vai ser acessada frequentemente por ferramentas automatizadas. Usaria um banco não relacional como Cassandra.

> O Cassandra é um banco de dados não relacional e colunar, ele é considerado um banco altamente escalável e distribuído. Como ele não tem transações em determinados momentos, a leitura dos dados pode não ter consistência, tendo em vista que o Cassandra demora alguns milissegundos para reorganizar o cluster.

Implementações da Base:
- Deve ser feito com autenticação e criptografia forte.
- Sendo os dados são sensíveis, acessos podem ser marcados no ElasticSerach para registra-los.
- Os retornos devem ser:
  - Completos se o propio usuário estiver pesquisando.
    - Além disso deve-se calcular seu score pessoal de crédito e formas de aumentá-lo.
      - Esse cálculo será feito partindo do pré-suposto que a pessoa possuí crédito 0
        - O cálculo do score será (salário x 1/5) considerando que ele não tenha dividas vou assumir que se ele receber 5000 ele pode possuir um score 1000
        - O resultado deve ser somado aos bens x um numero que pode ser 300, 400 ou 500, caso o usuário possua um  carro, apartamento ou casa.
    - É possível ver como está seu score em relação a outros usuários da mesma idade, da mesma cidade e estado.
  - É possivel apenas ver o score de outros usuários.
  - Apenas nome e quantidade de Dívidas se algum outro usuário logado em uma conta diferente da que está buscando fizer a busca para proteger os dados do cliente.

Modelagem do banco de dados.

Tabela de usuário:
  - id
  - idade
  - endereco_id

Tabela de endereço:
  - id
  - rua
  - cidade
  - estado
  - pais

Tabela de lista de lista de bens:
  - id
  - bem
  - tipo
  - usuario_id

Tabela de fonte de renda:
  - id
  - descrição
  - valor
  - usuario_id

Todas as tabelas devem possuir data de criação, edição e remoção criadas de forma automatica quando fosse feita a operação.

Lista de tarefas
- [ ] Criar banco de dados e migração
- [ ] Desenvolver a api
- [ ] Desenvolver testes unitários

## Base C

Entendo que a performance é o foco principal dessa base. O melhor banco para esse proposito é o Redis, por salvar seus dados em memória tem uma velocidade de leitura e escrita muito mais rapida que os bancos SQL/NoSQL.

Implementações da base:
- Acesso deve ser feito com autenticação.
- Não é necessário registrar logs.
- Os retornos devem ser:
  - Completos e apenas deve ser possível buscar a si mesmo.
  
Modelagem do banco de dados.

Tabela de consultas do CPF:
  - id
  - cpf
  - ultimo_acesso
  - ultima_compra_id

Tabela de movimentação do CPF:
  - id
  - usuario_id
  - descrição
  - valor
  - data

Tabela de lista de última compra no CPF:
  - compra_id
  - usuario_id
  - valor
  - data
  - a_vista

Lista de tarefas
- [ ] Criar banco de dados e migração
- [ ] Desenvolver a api
- [ ] Desenvolver testes unitários

> Será usado um serviço da AWS chamado [Cognito](https://aws.amazon.com/pt/cognito/) que gerencia cadastros, logins, acessos e permissões de usuário, utilizado em diversas plataformas importantes.

> Será utilizado Docker para subir as aplicações na AWS, Java (Spring Boot) no Backend, PostgreSQL, Cassandra, Redis para os bancos das bases A,B,C, ElasticSearch para registrar os acessos das bases A e B.
