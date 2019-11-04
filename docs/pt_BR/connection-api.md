# Connection APIs

* [API principal](#main-api)
* [`Connection` API](#connection-api)
* [`ConnectionManager` API](#connectionmanager-api)

## API principal

* `createConnection()` - Cria uma nova conexão e a registra no ConnectionManager.
Se as opções de conexão forem omitidas, então as opções serão lidas do arquivo de `ormconfig` ou das variáveis de ambiente.

```typescript
import {createConnection} from "typeorm";

const connection = await createConnection({
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "test",
    password: "test",
    database: "test"
});
```

* `createConnections()` - Cria várias conexões e as registra no ConnectionManager.
Se as opções forem omitidas então elas serão lidas do do arquivo `ormconfig` ou das variáveis de ambiente.

```typescript
import {createConnections} from "typeorm";

const connection = await createConnections([{
    name: "connection1",
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "test",
    password: "test",
    database: "test"
}, {
    name: "connection2",
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "test",
    password: "test",
    database: "test"
}]);
```

* `getConnectionManager()` - Pega o ConnectionManager onde todas as conexões criadas (usando `createConnection()` ou `createConnections()`) estão guardadas.

```typescript
import {getConnectionManager} from "typeorm";

const defaultConnection = getConnectionManager().get("default");
const secondaryConnection = getConnectionManager().get("secondary");
```

* `getConnection()` - Pega a conexão que foi criarda usando o método `createConnection`.

```typescript
import {getConnection} from "typeorm";

const connection = getConnection();
// Se a conexão tem um nome você pode passá-lo como primeiro parâmetro:
const secondaryConnection = getConnection("secondary-connection");
```

* `getEntityManager()` - Pega `EntityManager`.
O nome da conexão pode ser espeficada para indicar que qual instância do EntityManager usar.

```typescript
import {getEntityManager} from "typeorm";

const manager = getEntityManager();
// você pode usar os métodos do manager

const secondaryManager = getEntityManager("secondary-connection");
// você pode usar os métodos da conexão 'secondary-connection'
```

* `getRepository()` - Pega o `Repository` para determinada entidade da conexão.
O nome da conexão pode ser especificada para indicar qual instância do EntityManager usar.

```typescript
import {getRepository} from "typeorm";

const userRepository = getRepository(User);
// você pode usar os métodos do repositório

const blogRepository = getRepository(Blog, "secondary-connection");
// você pode usar os métodos do repositório da conexão "secondary-connection"
```

* `getTreeRepository()` - Pega `TreeRepository` para determinada entidade da conexão.
O nome da conexão pode ser especificado para indicar qual instância do EntityManager usar.

```typescript
import {getTreeRepository} from "typeorm";

const userRepository = getTreeRepository(User);
// você pode usar os métodos do repositório

const blogRepository = getTreeRepository(Blog, "secondary-connection");
// você pode usar os métodos do repositório da conexão "secondary-connection"
```

* `getMongoRepository()` - Pega `MongoRepository` para determinada entidade da conexão.
O nome da conexão pode ser especificado para indicar qual instância do EntityManager usar.

```typescript
import {getMongoRepository} from "typeorm";

const userRepository = getMongoRepository(User);
// você pode usar os métodos do repositório

const blogRepository = getMongoRepository(Blog, "secondary-connection");
// você pode usar os métodos do repositório da conexão "secondary-connection"
```

## `Connection` API

* `name` - Nome da conexao. Se você criar uma conexão sem nome, então ela terá o nome de "default".
Você usa esse nome quando trabalha com várias conexões e chama `getConnection(connectionName: string)`

```typescript
const connectionName: string = connection.name;
```

* `options` - Configurações usadas para criar essa conexão.
Leia mais em [Configurações da conexão](./connection-options.md).

```typescript
const connectionOptions: ConnectionOptions = connection.options;
// você pode das cast de connectionOptions para MysqlConnectionOptions
// ou qualquer outro xxxConnectionOptions dependendo de qual banco de dodos você use
```

* `isConnected` - Indica se uma coneção real com o banco de dados foi estabelecida.

```typescript
const isConnected: boolean = connection.isConnected;
```

* `driver` - O drive usado para realizar essa conexão.

```typescript
const driver: Driver = connection.driver;
// você pode dar cast de connectionOptions para MysqlDriver
// ou qualquer outro xxxDriver dependendo do banco de dados usado
```

* `manager` - `EntityManager` usado para trabalhar com entidades.
Leia mais em [Entity Manager e Repository](working-with-entity-manager.md).

```typescript
const manager: EntityManager = connection.manager;
// você pode chamar os métodos de manager, por exemlo find:
const user = await manager.findOne(1);
```

* `mongoManager` - `MongoEntityManager` usado para trablhar com entidades no mongodb.
Para mais informações sobre MongoEntityManger veja documentação do [MongoDB](./mongodb.md).

```typescript
const manager: MongoEntityManager = connection.mongoManager;
// você pode chamar métodos do manager ou métodos específicos do mongodb-manager, por exemplo find:
const user = await manager.findOne(1);
```

* `connect` - Executa coneção com banco de dados. 
Quando você usa `createConnection` automaticamente chama `connect` e você não precisa chamar.

```typescript
await connection.connect();
```

* `close` - Fecha uma conexão com o banco de dados. 
Geralmente, você chama esse método quando sua aplicação será desligada.

```typescript
await connection.close();
```

* `synchronize` - Sincroniza os schems do banco de dados. Quando `synchronize: true` está configurado nas opções da conexao, este método é automaticamente chamado. 
Geralmente, você pode chamar este método quando sua aplicação está iniciando.

```typescript
await connection.synchronize();
```

* `dropDatabase` - Remove tudo que tem no banco de dados, incluindo os dados.
Cuidado com este método em produção, já que ele irá apagar todas as tabelas e dados do seu banco de dados
Pode ser usado depois que a conexão com o banco de dados foi estabelecida.

```typescript
await connection.dropDatabase();
```

* `runMigrations` - Executa todas as migrações pendentes.

```typescript
await connection.runMigrations();
```

* `undoLastMigration` - Reverte a última migração executada.

```typescript
await connection.undoLastMigration();
```

* `hasMetadata` - Verifica se a entidade está registrada.
Leia mais em [Entity Metadata](./entity-metadata.md).

```typescript
if (connection.hasMetadata(User))
    const userMetadata = connection.getMetadata(User);
```

* `getMetadata` - Pega a `EntityMetadata` de uma entidade.
Você também pode passar o nome da tabela e se uma entidade existe para ela será retornada
Leia mais em [Entity Metadata](./entity-metadata.md).

```typescript
const userMetadata = connection.getMetadata(User);
// now you can get any information about User entity
```

* `getRepository` - Pega `Repository` de uma entidade.
Você tambpem pode passar o nome da tabela e se o repositório for encontrado será retornado.
Leia mais em [Repositórios](working-with-repository.md).

```typescript
const repository = connection.getRepository(User);
// agora você pode chamar os métodos do repositório, por exemplo find:
const users = await repository.findOne(1);
```

* `getTreeRepository` - Pega `TreeRepository` de uma entidade.
Você pode passar o nome da tabela e se um repositório for encontrado será retornado.
Leia mais em [Repositórios](working-with-repository.md).

```typescript
const repository = connection.getTreeRepository(Category);
// agora você pode chamar os métodos de tree-repositories, por exemplo findTrees:
const categories = await repository.findTrees();
```

* `getMongoRepository` - Pega `MongoRepository` de uma entidade.
Este método é usado em conexões com MongoDB.
Leia mais em [MongoDB](./mongodb.md).

```typescript
const repository = connection.getMongoRepository(User);
// agora você pode chamar método específico de repositórios dos mongo, como por exemplo createEntityCursor:
const categoryCursor = repository.createEntityCursor();
const category1 = await categoryCursor.next();
const category2 = await categoryCursor.next();
```

* `getCustomRepository` - Pega um repositório personalizados.
Leia mais em [repositórios personalizados](custom-repository.md).

```typescript
const userRepository = connection.getCustomRepository(UserRepository);
// agora você pode chamar método de seus repositórios personalizados - classe UserRepository
const crazyUsers = await userRepository.findCrazyUsers();
```

* `transaction` - Fornece uma `transaction` onde várias operações podem ser executados no banco de dados usando uma única transação.
Leia mais em [Transactions](./transactions.md).

```typescript
await connection.transaction(async manager => {
    // NOTA: vocÊ precisa usar a nova instância do manager em todas as suas operações com o banco de dados
    // esta é uma instância especial de EntityManager que usa esta transaction
    // e não esqueça de usar await aqui
});
```

* `query` - Executa um script SQL.

```typescript
const rawData = await connection.query(`SELECT * FROM USERS`);
```

* `createQueryBuilder` - Cria um query builder, que pode ser usado para contruir queries.
Leia mais em [QueryBuilder](select-query-builder.md).

```typescript
const users = await connection.createQueryBuilder()
    .select()
    .from(User, "user")
    .where("user.name = :name", { name: "John" })
    .getMany();
```

* `createQueryRunner` - Cria um query runner, usado para gerenciar o trabalho com uma única instância da conexão real com o banco de dados.
Leia mais em [QueryRunner](./query-runner.md). 

```typescript
const queryRunner = connection.createQueryRunner();

// você pode usar este método apenas depois de chamar o connect
// que executa a conexão real com o banco de dados
await queryRunner.connect();

// .. agora você pode trabalhar com o query runner e seus métodos

// muito importante - não esqueça de usar o método release do query runner uma vez que você terminou de trabalhar com ele
await queryRunner.release();
```

## `ConnectionManager` API

* `create` - Cria uma nova conexão e registra o no connection manager.

```typescript
const connection = connectionManager.create({
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "test",
    password: "test",
    database: "test"
});
```

* `get` - Pega uma conexão já criada pelo seu nome.

```typescript
const defaultConnection = connectionManager.get("default");
const secondaryConnection = connectionManager.get("secondary");
```

* `has` - Verifica se uma conexão está registrada no connection manager.

```typescript
if (connectionManager.has("default")) {
    // ...
}
```
