# Usando Connection

* [O que é o `Connection`](#o-que-é-o-connection)
* [Criando uma nova conexão](#criando-uma-nova-conexão)
* [Usando `ConnectionManager`](#usando-connectionmanager)
* [Trabalhando com conexão](#trabalhando-com-conexão)
    
## O que é o `Connection`

A interação com o banco de dados só é possível uma vez que você configurou a conxeção.
O `Connection` do TypeORM não uma conexão com o banco de dados em si, apesar do que parece, é apenas uma interface de configuração que espelha o pool de conexão.
Se você está interessado na configuração real com o banco de dados, então de uma olhada na documentação de `QueryRunner`.
Cada instancia de um `QueryRunner` é uma conexão separada e isolada com o banco de dados.
O pool de conexão é estabelecido uma vez que o método `connect` do `Connection` é chamado.
O método `connect` é chamado automaticamente se ao configurar suas configurações você usar a função `createConnection`.
A desconexão com o banco de dados (fechando todos os pools de conexão) ocorre quando `close` é chamado.
Geralmente, você precisa criar sua conexão apenas uma vez quando sua aplicação é iniciada,
e fecha-la depois que foi finalizado todas as operações no banco de dados.
Na prática, se você está criando uma aplicação backend (como um site), eles sempre estará rodando, assim como sua conexão, então você não precisa fecha-la.

## Criando uma nova Connection

Existem várias maneiras de uma conexão ser criada.
A maneira mais simples e comum é usar as funções `createConnection` e `createConnections`.

`createConnection` cria uma conexão:

```typescript
import {createConnection, Connection} from "typeorm";

const connection = await createConnection({
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "test",
    password: "test",
    database: "test"
});
```
Um único atributo `url`, mais o atributo `type`, irá funcionar também.

```js
createConnection({
    type: 'postgres',
    url: 'postgres://test:test@localhost/test'
})
```

`createConnections` cria várias conexões:

```typescript
import {createConnections, Connection} from "typeorm";

const connections = await createConnections([{
    name: "default",
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "test",
    password: "test",
    database: "test"
}, {
    name: "test2-connection",
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "test",
    password: "test",
    database: "test2"
}]);
```

Ambas essas funções criam um `Connection` baseado ans opções passadas ao chamar o método `connect`.
Você pode criar um arquivo [ormconfig.json](./using-ormconfig.md) na raiz do seu projeto
a as opções de conexão serão automaticamente lidas do arquivo.
A raiz do projeto é o mesmo lugar onde está o `node_modules`.

```typescript
import {createConnection, createConnections, Connection} from "typeorm";

// aqui o createConnection irá carregar as opções de as arquivos
// ormconfig.json / ormconfig.js / ormconfig.yml / ormconfig.env / ormconfig.xml
// ou das variáveis de ambiente
const connection: Connection = await createConnection();

// você pode especificar o nome da conexão que será criada
// (se você omitir o nome, irá ser criado uma conexão sem nome)
const secondConnection: Connection = await createConnection("test2-connection");

// se o método createConnections for chamado ao invés de createConnection então
// irá inicializar e retornar todas as conexões definidas no arquivo ormconfig
const connections: Connection[] = await createConnections();
```

Diferentes conexões precisam ter nomes diferentes.
Por padrao se uma conexão nao tiver nome, ele será definido para `default`.
Geralmente, você pode usar várias conexões quando você usa vários banco de dados ou várias configurações de conexão.

Uma vez criada a conexão, você pode obtÊ-la de qualquer lugar em sua aplicação usando a função `getConnection`:

```typescript
import {getConnection} from "typeorm";

// pode ser usado uma vêz que createConnection é chamado e resolvido
const connection = getConnection();

// se você tem várias conexões você pode passar o nome de alguma delas
const secondConnection = getConnection("test2-connection");
```

Evite criar classes / serviços extras para guardar e gerenciar suas conexões.
Esta função já é disponibilizada pelo TypeORM -
você não precisa criar overengineerings e abstrações desnecessárias.

## Usando `ConnectionManager`

Você pdoe criar conexões usando a classe `ConnectionManager`. Por exemplo:

```typescript
import {getConnectionManager, ConnectionManager, Connection} from "typeorm";

const connectionManager = getConnectionManager();
const connection = connectionManager.create({
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "test",
    password: "test",
    database: "test",
});
await connection.connect(); // executa a conexão
```

Isto não é a maneira mais comun de criar uma conexão, mas pode ser útil em algums casos.
Por exemplo, usuários que querem criar a conexão e guardar sua instância,
mas tem que controlar quando sua conexão real é estabelecida com o banco.
Você também pode criar e manutenir seu prório `ConnectionManager`:

```typescript
import {getConnectionManager, ConnectionManager, Connection} from "typeorm";

const connectionManager = new ConnectionManager();
const connection = connectionManager.create({
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "test",
    password: "test",
    database: "test",
});
await connection.connect(); // executa a conexão
```

Mas note, desta maneira não é possível usar `getConnection()` -
você precisará guardar sua conexão e usar `connectionManager.get` para pegá-la sempre que preciso.

Geralmenente, evite está maneira e evite complicações desnecessárias em sua aplicação,
use `ConnectionManager` apenas se você realmente sabe o que está fazendo.

## Trabalhando com conexão

Uma vez que você configurou a sua conexão, você pode usá-la onde quiser em seu app, chamando a função `getConnection`:

```typescript
import {getConnection} from "typeorm";
import {User} from "../entity/User";

export class UserController {

    @Get("/users")
    getAll() {
        return getConnection().manager.find(User);
    }

}
```

Você também pode usar `ConnectionManager#get` para pegar a conexão,
mas usando  `getConnection()` é o suficiente na maioria dos casos.

Usando Connection você pode executar operações no banco dedados com suas entidades,
mas pode usar mais particularmente com `EntityManager` e `Repository`.
Para mais informações sobre veja a documentação em [Entity Manager e Repository](working-with-entity-manager.md).

Mas geralmente, você não precisa usar diretamente `Connection`.
A maioria das vezes você apenas precisará criar a conexão e usar `getRepository()` e `getManager()`
para acessar o gerenciador e repostórios de sua conexão sem acessar diretamente o objeto de conexão:

```typescript
import {getManager, getRepository} from "typeorm";
import {User} from "../entity/User";

export class UserController {

    @Get("/users")
    getAll() {
        return getManager().find(User);
    }

    @Get("/users/:id")
    getAll(@Param("id") userId: number) {
        return getRepository(User).findOne(userId);
    }

}
```
