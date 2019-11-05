# Usando configuração

  - [Criando uma nova conexão a partir de um arquivo de configuração](#criando-uma-nova-conexão-a-partir-de-um-arquivo-de-configuração)
  - [Usando `ormconfig.json`](#usando-ormconfigjson)
  - [Usando `ormconfig.js`](#usando-ormconfigjs)
  - [Usando variáveis de ambientes](#usando-variáveis-de-ambientes)
  - [Usando `ormconfig.yml`](#usando-ormconfigyml)
  - [Usando `ormconfig.xml`](#usando-ormconfigxml)
  - [Sobrescrevendo as opções do ormconfig](#sobrescrevendo-as-opções-do-ormconfig)

## Criando uma nova conexão a partir de um arquivo de configuração

A maioria das vezes você quer guardar as configurações de conexão em um arquivo separado.
Isto é conveniente e fácil de gerenciar.
TypeORM suporta varios arquivos de configurações diferentes.
Voccê apenas precisa criar um arquivo na raiz do seu projeto chamado `ormconfig.[format]` (próximo a `package.json`),
coloque as configurações de banco de dados no arquivo e em sua aplicação chame `createConnection()` sem passar qualquer configuração:

```typescript
import {createConnection} from "typeorm";

// o método createConnection irá automaticamente ler as opções
// se seu arquivo ormconfig ou variáveis de ambiente
const connection = await createConnection();
```

Os formatos suportados para ormconfig são: `.json`, `.js`, `.env`, `.yml` e `.xml`.

## Usando `ormconfig.json`

Crie `ormconfig.json` na raiz de seu projeto (perto `package.json`). Deve ter conter o seguinte json:

```json
{
   "type": "mysql",
   "host": "localhost",
   "port": 3306,
   "username": "test",
   "password": "test",
   "database": "test"
}
```

Você pode especificar qualquer outra opção disponível nas [Opções de conexão](./connection-options.md).

Se você quer criar múltiplas conexões então simplesmente crie um array com várias configurações de conexão:

```json
[{
   "name": "default",
   "type": "mysql",
   "host": "localhost",
   "port": 3306,
   "username": "test",
   "password": "test",
   "database": "test"
}, {
   "name": "second-connection",
   "type": "mysql",
   "host": "localhost",
   "port": 3306,
   "username": "test",
   "password": "test",
   "database": "test"
}]
```

## Usando `ormconfig.js`

Crie um arquivo `ormconfig.js` na raiz do projeto (próximo a `package.json`). Com o seguinte conteúdo:

```javascript
module.exports = {
   "type": "mysql",
   "host": "localhost",
   "port": 3306,
   "username": "test",
   "password": "test",
   "database": "test"
}
```

Você pode especificar qualquer outra opção disponível nas [Opções de conexão](./connection-options.md).
Se você quer criar vários conexões você pode simplesmente criar um array com mais opções e retorná-lo.

## Usando variáveis de ambientes

Crie um arquivlo `.env` ou `ormconfig.env` na raiz do projeto (próximo a `package.json`). Contendo o seguinte conteúdo:

```ini
TYPEORM_CONNECTION = mysql
TYPEORM_HOST = localhost
TYPEORM_USERNAME = root
TYPEORM_PASSWORD = admin
TYPEORM_DATABASE = test
TYPEORM_PORT = 3000
TYPEORM_SYNCHRONIZE = true
TYPEORM_LOGGING = true
TYPEORM_ENTITIES = entity/*.js,modules/**/entity/*.js
```

Lista com as opões disponíveis:

* TYPEORM_CONNECTION
* TYPEORM_HOST
* TYPEORM_USERNAME
* TYPEORM_PASSWORD
* TYPEORM_DATABASE
* TYPEORM_PORT
* TYPEORM_URL
* TYPEORM_SID
* TYPEORM_SCHEMA
* TYPEORM_SYNCHRONIZE
* TYPEORM_DROP_SCHEMA
* TYPEORM_MIGRATIONS_RUN
* TYPEORM_ENTITIES
* TYPEORM_MIGRATIONS
* TYPEORM_MIGRATIONS_TABLE_NAME
* TYPEORM_SUBSCRIBERS
* TYPEORM_ENTITY_SCHEMAS
* TYPEORM_LOGGING
* TYPEORM_LOGGER
* TYPEORM_ENTITY_PREFIX
* TYPEORM_MAX_QUERY_EXECUTION_TIME
* TYPEORM_ENTITIES_DIR
* TYPEORM_MIGRATIONS_DIR
* TYPEORM_SUBSCRIBERS_DIR
* TYPEORM_DRIVER_EXTRA
* TYPEORM_DEBUG
* TYPEORM_CACHE
* TYPEORM_CACHE_OPTIONS
* TYPEORM_CACHE_ALWAYS_ENABLED
* TYPEORM_CACHE_DURATION

`TYPEORM_CACHE` deve ser um booleano ou string (sendo o tipo de cache)

`ormconfig.env` Deve ser usando apenas durante desenvolvimento.
Em produção você pode usar todas as opções usando reais VARIÁVEIS DE AMBIENTE.

Você não pode definir multiplas conexões usando `env` em arquivo ou variáveis de ambiente.
Se seu aplicativo tem várias conexões então use alguns dos outros formatos disponíveis.

If you need to pass a driver-specific option, e.g. `charset` for MySQL, you could use the `TYPEORM_DRIVER_EXTRA` variable in JSON format, e.g.
```
TYPEORM_DRIVER_EXTRA='{"charset": "utf8mb4"}'`
```
## Usando `ormconfig.yml`

Create `ormconfig.yml` in the project root (near `package.json`). It should have the following content:

```yaml
default: # default connection
    host: "localhost"
    port: 3306
    username: "test"
    password: "test"
    database: "test"

second-connection: # other connection
    host: "localhost"
    port: 3306
    username: "test"
    password: "test"
    database: "test2"
```

You can use any connection options available.

## Usando `ormconfig.xml`

Create `ormconfig.xml` in the project root (near `package.json`). It should have the following content:

```xml
<connections>
    <connection type="mysql" name="default">
        <host>localhost</host>
        <username>root</username>
        <password>admin</password>
        <database>test</database>
        <port>3000</port>
        <logging>true</logging>
    </connection>
    <connection type="mysql" name="second-connection">
        <host>localhost</host>
        <username>root</username>
        <password>admin</password>
        <database>test2</database>
        <port>3000</port>
        <logging>true</logging>
    </connection>
</connections>
```

You can use any connection options available.

## Which configuration file is used by Typeorm

Sometimes, you may want to use multiple configurations using different formats. When calling `getConnectionOptions()`
or attempting to use `createConnection()` without the connection options, Typeorm will attempt to load the configurations,
in this order:

1. From the environment variables. Typeorm will attempt to load the `.env` file using dotEnv if it exists. If the environment
variables `TYPEORM_CONNECTION` or `TYPEORM_URL` are set, Typeorm will use this method.
2. From the `ormconfig.env`.
3. From the other `ormconfig.[format]` files, in this order: `[js, ts, json, yml, yaml, xml]`.

Note that Typeorm will use the first valid method found and will not load the others. For example, Typeorm will not load the
`ormconfig.[format]` files if the configuration was found in the environment.

## Sobrescrevendo as opções do ormconfig

Sometimes you want to override values defined in your ormconfig file,
or you might to append some TypeScript / JavaScript logic to your configuration.

In such cases you can load options from ormconfig and get `ConnectionOptions` built,
then you can do whatever you want with those options, before passing them to `createConnection` function:


```typescript
// read connection options from ormconfig file (or ENV variables)
const connectionOptions = await getConnectionOptions();

// do something with connectionOptions,
// for example append a custom naming strategy or a custom logger
Object.assign(connectionOptions, { namingStrategy: new MyNamingStrategy() });

// create a connection using modified connection options
const connection = await createConnection(connectionOptions);
```
