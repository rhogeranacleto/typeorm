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

* `isConnected` - Indicates if a real connection to the database is established.

```typescript
const isConnected: boolean = connection.isConnected;
```

* `driver` - Underlying database driver used in this connection.

```typescript
const driver: Driver = connection.driver;
// you can cast connectionOptions to MysqlDriver
// or any other xxxDriver depending on the database driver you use
```

* `manager` - `EntityManager` used to work with connection entities.
Learn more about [Entity Manager and Repository](working-with-entity-manager.md).

```typescript
const manager: EntityManager = connection.manager;
// you can call manager methods, for example find:
const user = await manager.findOne(1);
```

* `mongoManager` - `MongoEntityManager` used to work with connection entities in mongodb connections.
For more information about MongoEntityManager see [MongoDB](./mongodb.md) documentation.

```typescript
const manager: MongoEntityManager = connection.mongoManager;
// you can call manager or mongodb-manager specific methods, for example find:
const user = await manager.findOne(1);
```

* `connect` - Performs connection to the database. 
When you use `createConnection` it automatically calls `connect` and you don't need to call it yourself.

```typescript
await connection.connect();
```

* `close` - Closes connection with the database. 
Usually, you call this method when your application is shutting down.

```typescript
await connection.close();
```

* `synchronize` - Synchronizes database schema. When `synchronize: true` is set in connection options it calls this method. 
Usually, you call this method when your application is shutting down.

```typescript
await connection.synchronize();
```

* `dropDatabase` - Drops the database and all its data.
Be careful with this method on production since this method will erase all your database tables and their data.
Can be used only after connection to the database is established.

```typescript
await connection.dropDatabase();
```

* `runMigrations` - Runs all pending migrations.

```typescript
await connection.runMigrations();
```

* `undoLastMigration` - Reverts last executed migration.

```typescript
await connection.undoLastMigration();
```

* `hasMetadata` - Checks if metadata for a given entity is registered.
Learn more about [Entity Metadata](./entity-metadata.md).

```typescript
if (connection.hasMetadata(User))
    const userMetadata = connection.getMetadata(User);
```

* `getMetadata` - Gets `EntityMetadata` of the given entity.
You can also specify a table name and if entity metadata with such table name is found it will be returned.
Learn more about [Entity Metadata](./entity-metadata.md).

```typescript
const userMetadata = connection.getMetadata(User);
// now you can get any information about User entity
```

* `getRepository` - Gets `Repository` of the given entity.
You can also specify a table name and if repository for given table is found it will be returned.
Learn more about [Repositories](working-with-repository.md).

```typescript
const repository = connection.getRepository(User);
// now you can call repository methods, for example find:
const users = await repository.findOne(1);
```

* `getTreeRepository` - Gets `TreeRepository` of the given entity.
You can also specify a table name and if repository for given table is found it will be returned.
Learn more about [Repositories](working-with-repository.md).

```typescript
const repository = connection.getTreeRepository(Category);
// now you can call tree repository methods, for example findTrees:
const categories = await repository.findTrees();
```

* `getMongoRepository` - Gets `MongoRepository` of the given entity.
This repository is used for entities in MongoDB connection.
Learn more about [MongoDB support](./mongodb.md).

```typescript
const repository = connection.getMongoRepository(User);
// now you can call mongodb-specific repository methods, for example createEntityCursor:
const categoryCursor = repository.createEntityCursor();
const category1 = await categoryCursor.next();
const category2 = await categoryCursor.next();
```

* `getCustomRepository` - Gets custom defined repository.
Learn more about [custom repositories](custom-repository.md).

```typescript
const userRepository = connection.getCustomRepository(UserRepository);
// now you can call methods inside your custom repository - UserRepository class
const crazyUsers = await userRepository.findCrazyUsers();
```

* `transaction` - Provides a single transaction where multiple database requests will be executed in a single database transaction.
Learn more about [Transactions](./transactions.md).

```typescript
await connection.transaction(async manager => {
    // NOTE: you must perform all database operations using given manager instance
    // its a special instance of EntityManager working with this transaction
    // and don't forget to await things here
});
```

* `query` - Executes a raw SQL query.

```typescript
const rawData = await connection.query(`SELECT * FROM USERS`);
```

* `createQueryBuilder` - Creates a query builder, which can be used to build queries.
Learn more about [QueryBuilder](select-query-builder.md).

```typescript
const users = await connection.createQueryBuilder()
    .select()
    .from(User, "user")
    .where("user.name = :name", { name: "John" })
    .getMany();
```

* `createQueryRunner` - Creates a query runner used manage and work with a single real database connection.
Learn more about [QueryRunner](./query-runner.md). 

```typescript
const queryRunner = connection.createQueryRunner();

// you can use its methods only after you call connect
// which performs real database connection
await queryRunner.connect();

// .. now you can work with query runner and call its methods

// very important - don't forget to release query runner once you finished working with it
await queryRunner.release();
```

## `ConnectionManager` API

* `create` - Creates a new connection and register it in the manager.

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

* `get` - Gets already created connection stored in the manager by its name.

```typescript
const defaultConnection = connectionManager.get("default");
const secondaryConnection = connectionManager.get("secondary");
```

* `has` - Checks if a connection is registered in the given connection manager.

```typescript
if (connectionManager.has("default")) {
    // ...
}
```
