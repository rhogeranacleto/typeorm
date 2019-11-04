# Connection Options

* [O que é o `ConnectionOptions`](#what-is-connectionoptions)
* [Configurações comum da conexão](#common-connection-options)
* [Configurações de `mysql` / `mariadb`](#mysql--mariadb-connection-options)
* [Configurações de `postgres` / `cockroachdb`](#postgres--cockroachdb-connection-options)
* [Configurações de `sqlite`](#sqlite-connection-options)
* [Configurações de `cordova`](#cordova-connection-options)
* [Configurações de `react-native`](#react-native-connection-options)
* [Configurações de `nativescript`](#nativescript-connection-options)
* [Configurações de `mssql`](#mssql-connection-options)
* [Configurações de `mongodb`](#mongodb-connection-options)
* [Configurações de `sql.js`](#sqljs-connection-options)
* [Configurações de `expo`](#expo-connection-options)
* [Exemplo de configurações](#connection-options-example)
    
## O que é o `ConnectionOptions`

Connection options são as configurações de conexão que você pode passa para o `createConnection`
 ou definir no arquivo de `ormconfig`. Diferentes banco de dados tem suas configureções específicas.

## Configurações comum da conexão

* `type` - Tipo do banco de dados. Você precisa especificar qual banco de dados será usado.
 As opções posíveis são "mysql", "postgres", "cockroachdb", "mariadb", "sqlite", "cordova", "nativescript",
 "oracle", "mssql", "mongodb", "sqljs", "react-native".
 Está configurações é **obrigatória**.

* `name` - O nome da conexão. Você irá usá-la para pegar sua conexão usando `getConnection(name: string)` ou `ConnectionManager.get(name: string)`. 
Conexões diferentes devem ter nomes diferentes - todas devem ser únicas.
Se o nome da conexão não for passada, ela será chamada de "default".

* `extra` - A opção Extra será redirecionada para o driver escolhido. 
Use se você quer passar configurações adicionais ao driver usado na conexão.

* `entities` - Entidades a serem carregadas e usadas nessa conexão.
Aceita tanto classes como caminhos para o diretório de onde carregar as entidades.
Caminhos de diretórios suporta o padrão glob.
Exemplo: `entities: [Post, Category, "entity/*.js", "modules/**/entity/*.js"]`.
Leia mais em [Entities](./entities.md).

* `subscribers` - Subscribers a serem carregados e usados nessa conexão.
Aceita tanto classes como caminhos para o diretório de onde carregar.
Caminhos de diretórios suporta o padrão glob.
Exemplo: `subscribers: [PostSubscriber, AppSubscriber, "subscriber/*.js", "modules/**/subscriber/*.js"]`.
Leia mais em [Subscribers](listeners-and-subscribers.md).

* `entitySchemas` - Entity schemas a serem carregados e usados nessa conexão.
Aceita tanto classes como caminhos para o diretório de onde carregar.
Caminhos de diretórios suporta o padrão glob.
Exemplo: `entitySchemas: [PostSchema, CategorySchema, "entity-schema/*.json", "modules/**/entity-schema/*.json"]`.
Leia mais em [Entity Schemas](separating-entity-definition.md).

* `migrations` - Migrations a serem carregados e usados nessa conexão.
Aceita tanto classes como caminhos para o diretório de onde carregar.
Caminhos de diretórios suporta o padrão glob.
Exemplo: `migrations: [FirstMigration, SecondMigration, "migration/*.js", "modules/**/migration/*.js"]`.
Leia mais em [Migrations](./migrations.md).

* `logging` - Indica se os logs estão habilidatos ou não.
Se configurado como `true` então queries e erros serão macadas como habilitadas.
Você também pode especificar diferentes tipos de logs, por exemplo `["query", "error", "schema"]`.
Leia mais em [Logging](./logging.md).

* `logger` - Logger que será usado para logs. Possíveis valores são "advanced-console", "simple-console" and "file". 
O padrão é "advanced-console". Você também pode passar uma classe que implementa a interface `Logger`.
Leia mais em [Logging](./logging.md).

* `maxQueryExecutionTime` - Se uma query exceder o tempo máximo passado (em milissegundos) o logger irá mostrar essa query.

* `namingStrategy` - Estratégia de nomeação usada para nomear as tabelas e colunas do banco de dados.
Leia mais em [Naming strategies](./naming-strategy.md).

* `entityPrefix` - Prefixo para todas as tabelas (ou coleção) neste banco de dados.

* `dropSchema` - Remove o schema toda vez que a conexão é estabelecida.
Cuidado com esta opção e não a use em produção - você corre o risco de perder todos os dados.
Esta opção é útil durante debugs e desenvolvimento.

* `synchronize` - Indica se as tabelas e colunas do banco de dados devem se auto criadas de acordo com as entidades a cada vez que a aplicação for inicializada.
 Cuidado com esta opção e não a use em produção - ou você pode perder dados.
 Esta opção é útil durante debugs e desenvolvimento.
 Como uma alternativa, você pode usar CLI e executar o comando schema:sync.
 Note que o MongoDB não cria schema, já que ele não é um banco de dados estrutural.
 Ao invés disso, ele sincroniza automaticamente sempre que uma operação de insert é realizada.

* `migrationsRun` - Indica se as migrações devem ser executadas automaticamente quando a conexão for feita.
Como alternativa, você pode usar CLI e executar o comando migration:run.

* `migrationsTableName` - Nome da tabela neste banco de dados que conterá todas as informações referentes as migrações executadas.
Por padrão ela é chamada de "migrations".

* `cache` - Habilita que os resultados sejam guardados em cache. Você tambem pode configurar o tipo de cache e outras opções de cache aqui.
Leia mais sobre cache [aqui](./caching.md).

* `cli.entitiesDir` - Diretório onde as entities devem ser criadas por default pelo CLI.

* `cli.migrationsDir` - Diretório onde as migrations devem ser criadas por default pelo CLI.

* `cli.subscribersDir` - Diretório onde as subscribers devem ser criadas por default pelo CLI.

## Configurações de `mysql` / `mariadb`

* `url` - Connection url where perform connection to. Please note that other connection options will override parameters set from url.

* `host` - Database host.

* `port` - Database host port. Default mysql port is `3306`.

* `username` - Database username.

* `password` - Database password.

* `database` - Database name.

* `charset` - The charset for the connection. This is called "collation" in the SQL-level of MySQL 
(like utf8_general_ci). If a SQL-level charset is specified (like utf8mb4) then the default collation for that charset is used. (Default: `UTF8_GENERAL_CI`).

* `timezone` - the timezone configured on the MySQL server. This is used to typecast server date/time 
values to JavaScript Date object and vice versa. This can be `local`, `Z`, or an offset in the form 
`+HH:MM` or `-HH:MM`. (Default: `local`)

* `connectTimeout` - The milliseconds before a timeout occurs during the initial connection to the MySQL server.
 (Default: `10000`)

* `acquireTimeout` - The milliseconds before a timeout occurs during the initial connection to the MySql server. It differs from `connectTimeout` as it governs the TCP connection timeout where as connectTimeout does not. (default: `10000`)
 
* `insecureAuth` - Allow connecting to MySQL instances that ask for the old (insecure) authentication method. 
(Default: `false`)
 
* `supportBigNumbers` - When dealing with big numbers (`BIGINT` and `DECIMAL` columns) in the database, 
you should enable this option (Default: `true`)
 
* `bigNumberStrings` - Enabling both `supportBigNumbers` and `bigNumberStrings` forces big numbers 
(`BIGINT` and `DECIMAL` columns) to be always returned as JavaScript String objects (Default: `true`). 
Enabling `supportBigNumbers` but leaving `bigNumberStrings` disabled will return big numbers as String 
objects only when they cannot be accurately represented with 
[JavaScript Number objects](http://ecma262-5.com/ELS5_HTML.htm#Section_8.5) 
(which happens when they exceed the `[-2^53, +2^53]` range), otherwise they will be returned as 
Number objects. This option is ignored if `supportBigNumbers` is disabled.

* `dateStrings` - Force date types (`TIMESTAMP`, `DATETIME`, `DATE`) to be returned as strings rather than 
inflated into JavaScript Date objects. Can be true/false or an array of type names to keep as strings. 
(Default: `false`)

* `debug` - Prints protocol details to stdout. Can be true/false or an array of packet type names that 
should be printed. (Default: `false`)

* `trace` - Generates stack traces on Error to include call site of library entrance ("long stack traces"). 
Slight performance penalty for most calls. (Default: `true`)

* `multipleStatements` - Allow multiple mysql statements per query. Be careful with this, it could increase the scope 
of SQL injection attacks. (Default: `false`)

* `flags` - List of connection flags to use other than the default ones. It is also possible to blacklist default ones.
 For more information, check [Connection Flags](https://github.com/mysqljs/mysql#connection-flags).
 
* `ssl` -  object with ssl parameters or a string containing the name of ssl profile. 
See [SSL options](https://github.com/mysqljs/mysql#ssl-options).

## Configurações de `postgres` / `cockroachdb`

* `url` - Connection url where perform connection to. Please note that other connection options will override parameters set from url.

* `host` - Database host.

* `port` - Database host port. Default postgres port is `5432`.

* `username` - Database username.

* `password` - Database password.

* `database` - Database name.

* `schema` - Schema name. Default is "public".

* `ssl` - Object with ssl parameters. See [TLS/SSL](https://node-postgres.com/features/ssl).

* `uuidExtension` - The Postgres extension to use when generating UUIDs. Defaults to `uuid-ossp`. Can be changed to `pgcrypto` if the `uuid-ossp` extension is unavailable.

* `poolErrorHandler` - A function that get's called when underlying pool emits `'error'` event. Takes single parameter (error instance) and defaults to logging with `warn` level.

## Configurações de `sqlite`

* `database` - Database path. For example "./mydb.sql"

## Configurações de `cordova`

* `database` - Database name

* `location` - Where to save the database. See [cordova-sqlite-storage](https://github.com/litehelpers/Cordova-sqlite-storage#opening-a-database) for options.

## Configurações de `react-native`
* `database` - Database name

* `location` - Where to save the database. See [react-native-sqlite-storage](https://github.com/andpor/react-native-sqlite-storage#opening-a-database) for options.

## Configurações de `nativescript`
* `database` - Database name

## Configurações de `mssql`

* `url` - Connection url where perform connection to. Please note that other connection options will override parameters set from url.

* `host` - Database host.

* `port` - Database host port. Default mssql port is `1433`.

* `username` - Database username.

* `password` - Database password.

* `database` - Database name.

* `schema` - Schema name. Default is "public".

* `domain` - Once you set domain, the driver will connect to SQL Server using domain login.

* `connectionTimeout` - Connection timeout in ms (default: `15000`).

* `requestTimeout` - Request timeout in ms (default: `15000`). NOTE: msnodesqlv8 driver doesn't support
 timeouts < 1 second.

* `stream` - Stream recordsets/rows instead of returning them all at once as an argument of callback (default: `false`).
 You can also enable streaming for each request independently (`request.stream = true`). Always set to `true` if you plan to
 work with a large amount of rows.
 
* `pool.max` - The maximum number of connections there can be in the pool (default: `10`).

* `pool.min` - The minimum of connections there can be in the pool (default: `0`).

* `pool.maxWaitingClients` - maximum number of queued requests allowed, additional acquire calls will be callback with
 an err in a future cycle of the event loop.
 
* `pool.testOnBorrow` -  should the pool validate resources before giving them to clients. Requires that either
  `factory.validate` or `factory.validateAsync` to be specified.
  
* `pool.acquireTimeoutMillis` - max milliseconds an `acquire` call will wait for a resource before timing out.
 (default no limit), if supplied should non-zero positive integer.
 
* `pool.fifo` - if true the oldest resources will be first to be allocated. If false the most recently released resources
 will be the first to be allocated. This in effect turns the pool's behaviour from a queue into a stack. boolean,
 (default `true`).
 
* `pool.priorityRange` - int between 1 and x - if set, borrowers can specify their relative priority in the queue if no
 resources are available. see example. (default `1`).
 
* `pool.autostart` - boolean, should the pool start creating resources etc once the constructor is called, (default `true`).

* `pool.evictionRunIntervalMillis` - How often to run eviction checks. Default: `0` (does not run).

* `pool.numTestsPerRun` - Number of resources to check each eviction run. Default: `3`.

* `pool.softIdleTimeoutMillis` - amount of time an object may sit idle in the pool before it is eligible for eviction by
 the idle object evictor (if any), with the extra condition that at least "min idle" object instances remain in the pool.
 Default `-1` (nothing can get evicted).
 
* `pool.idleTimeoutMillis` -  the minimum amount of time that an object may sit idle in the pool before it is eligible for
 eviction due to idle time. Supersedes `softIdleTimeoutMillis`. Default: `30000`.
 
 * `pool.errorHandler` - A function that get's called when underlying pool emits `'error'` event. Takes single parameter (error instance) and defaults to logging with `warn` level.
 
* `options.fallbackToDefaultDb` - By default, if the database requestion by `options.database` cannot be accessed, the connection
 will fail with an error. However, if `options.fallbackToDefaultDb` is set to `true`, then the user's default database will
  be used instead (Default: `false`).
  
* `options.enableAnsiNullDefault` - If true, SET ANSI_NULL_DFLT_ON ON will be set in the initial sql. This means new
 columns will be nullable by default. See the [T-SQL documentation](https://msdn.microsoft.com/en-us/library/ms187375.aspx)
 for more details. (Default: `true`).
 
* `options.cancelTimeout` - The number of milliseconds before the cancel (abort) of a request is considered failed (default: `5000`).

* `options.packetSize` - The size of TDS packets (subject to negotiation with the server). Should be a power of 2. (default: `4096`).

* `options.useUTC` - A boolean determining whether to pass time values in UTC or local time. (default: `true`).

* `options.abortTransactionOnError` - A boolean determining whether to rollback a transaction automatically if any
 error is encountered during the given transaction's execution. This sets the value for `SET XACT_ABORT` during the
 initial SQL phase of a connection ([documentation](http://msdn.microsoft.com/en-us/library/ms188792.aspx)).

* `options.localAddress` - A string indicating which network interface (ip address) to use when connecting to SQL Server.

* `options.useColumnNames` - A boolean determining whether to return rows as arrays or key-value collections. (default: `false`).

* `options.camelCaseColumns` - A boolean, controlling whether the column names returned will have the first letter
 converted to lower case (`true`) or not. This value is ignored if you provide a `columnNameReplacer`. (default: `false`).

* `options.isolationLevel` - The default isolation level that transactions will be run with. The isolation levels are
 available from `require('tedious').ISOLATION_LEVEL`.
   * `READ_UNCOMMITTED`
   * `READ_COMMITTED`
   * `REPEATABLE_READ`
   * `SERIALIZABLE`
   * `SNAPSHOT`
   
   (default: `READ_COMMITTED`)
 
* `options.connectionIsolationLevel` - The default isolation level for new connections. All out-of-transaction queries
 are executed with this setting. The isolation levels are available from `require('tedious').ISOLATION_LEVEL`.
   * `READ_UNCOMMITTED`
   * `READ_COMMITTED`
   * `REPEATABLE_READ`
   * `SERIALIZABLE`
   * `SNAPSHOT`
   
   (default: `READ_COMMITTED`)
 
* `options.readOnlyIntent` - A boolean, determining whether the connection will request read-only access from a
 SQL Server Availability Group. For more information, see here. (default: `false`).

* `options.encrypt` - A boolean determining whether or not the connection will be encrypted. Set to true if you're
 on Windows Azure. (default: `false`).
 
* `options.cryptoCredentialsDetails` - When encryption is used, an object may be supplied that will be used for the
 first argument when calling [tls.createSecurePair](http://nodejs.org/docs/latest/api/tls.html#tls_tls_createsecurepair_credentials_isserver_requestcert_rejectunauthorized)
  (default: `{}`).
  
* `options.rowCollectionOnDone` - A boolean, that when true will expose received rows in Requests' `done*` events.
 See done, [doneInProc](http://tediousjs.github.io/tedious/api-request.html#event_doneInProc)
 and [doneProc](http://tediousjs.github.io/tedious/api-request.html#event_doneProc). (default: `false`)
   
   Caution: If many rows are received, enabling this option could result in excessive memory usage.

* `options.rowCollectionOnRequestCompletion` - A boolean, that when true will expose received rows
 in Requests' completion callback. See [new Request](http://tediousjs.github.io/tedious/api-request.html#function_newRequest). (default: `false`)

   Caution: If many rows are received, enabling this option could result in excessive memory usage.

* `options.tdsVersion` - The version of TDS to use. If the server doesn't support the specified version, a negotiated version
 is used instead. The versions are available from `require('tedious').TDS_VERSION`.
   * `7_1`
   * `7_2`
   * `7_3_A`
   * `7_3_B`
   * `7_4`
   
  (default: `7_4`)

* `options.debug.packet` - A boolean, controlling whether `debug` events will be emitted with text describing packet
 details (default: `false`).
 
* `options.debug.data` - A boolean, controlling whether `debug` events will be emitted with text describing packet data
 details (default: `false`).
 
* `options.debug.payload` - A boolean, controlling whether `debug` events will be emitted with text describing packet
 payload details (default: `false`).
 
* `options.debug.token` - A boolean, controlling whether `debug` events will be emitted with text describing token stream
 tokens (default: `false`).

## Configurações de `mongodb`

* `url` - Connection url where perform connection to. Please note that other connection options will override parameters set from url.

* `host` - Database host.

* `port` - Database host port. Default mongodb port is `27017`.

* `database` - Database name.

* `poolSize` - Set the maximum pool size for each individual server or proxy connection.

* `ssl` - Use ssl connection (needs to have a mongod server with ssl support). Default: `false`.

* `sslValidate` - Validate mongod server certificate against ca (needs to have a mongod server with ssl support,
 2.4 or higher). Default: `true`.
 
* `sslCA` - Array of valid certificates either as Buffers or Strings (needs to have a mongod server with ssl support,
 2.4 or higher).
 
* `sslCert` - String or buffer containing the certificate we wish to present (needs to have a mongod server with ssl
 support, 2.4 or higher)
 
* `sslKey` - String or buffer containing the certificate private key we wish to present (needs to have a mongod server
 with ssl support, 2.4 or higher).
 
* `sslPass` - String or buffer containing the certificate password (needs to have a mongod server with ssl support,
 2.4 or higher).
 
* `autoReconnect` - Reconnect on error. Default: `true`.

* `noDelay` - TCP Socket NoDelay option. Default: `true`.

* `keepAlive` - The number of milliseconds to wait before initiating keepAlive on the TCP socket. Default: `30000`.

* `connectTimeoutMS` - TCP Connection timeout setting. Default: `30000`.

* `socketTimeoutMS` - TCP Socket timeout setting. Default: `360000`.

* `reconnectTries` - Server attempt to reconnect #times. Default: `30`.

* `reconnectInterval` - Server will wait #milliseconds between retries. Default: `1000`.

* `ha` - Turn on high availability monitoring. Default: `true`.

* `haInterval` - Time between each replicaset status check. Default: `10000,5000`.

* `replicaSet` - The name of the replicaset to connect to.
 
* `acceptableLatencyMS` - Sets the range of servers to pick when using NEAREST (lowest ping ms + the latency fence,
 ex: range of 1 to (1 + 15) ms). Default: `15`.

* `secondaryAcceptableLatencyMS` - Sets the range of servers to pick when using NEAREST (lowest ping ms + the latency
 fence, ex: range of 1 to (1 + 15) ms). Default: `15`.
 
* `connectWithNoPrimary` - Sets if the driver should connect even if no primary is available. Default: `false`.

* `authSource` - If the database authentication is dependent on another databaseName.

* `w` - The write concern.

* `wtimeout` - The write concern timeout value.

* `j` - Specify a journal write concern. Default: `false`.

* `forceServerObjectId` - Force server to assign _id values instead of driver. Default: `false`.

* `serializeFunctions` - Serialize functions on any object. Default: `false`.

* `ignoreUndefined` - Specify if the BSON serializer should ignore undefined fields. Default: `false`.

* `raw` - Return document results as raw BSON buffers. Default: `false`.

* `promoteLongs` - Promotes Long values to number if they fit inside the 53 bits resolution. Default: `true`.

* `promoteBuffers` - Promotes Binary BSON values to native Node Buffers. Default: `false`.

* `promoteValues` - Promotes BSON values to native types where possible, set to false to only receive wrapper types.
 Default: `true`.
 
* `domainsEnabled` - Enable the wrapping of the callback in the current domain, disabled by default to avoid perf hit.
 Default: `false`.
 
* `bufferMaxEntries` - Sets a cap on how many operations the driver will buffer up before giving up on getting a
 working connection, default is -1 which is unlimited.
 
* `readPreference` - The preferred read preference.
  * `ReadPreference.PRIMARY`
  * `ReadPreference.PRIMARY_PREFERRED`
  * `ReadPreference.SECONDARY`
  * `ReadPreference.SECONDARY_PREFERRED`
  * `ReadPreference.NEAREST`
  
* `pkFactory` - A primary key factory object for generation of custom _id keys.

* `promiseLibrary` - A Promise library class the application wishes to use such as Bluebird, must be ES6 compatible.

* `readConcern` - Specify a read concern for the collection. (only MongoDB 3.2 or higher supported).

* `maxStalenessSeconds` - Specify a maxStalenessSeconds value for secondary reads, minimum is 90 seconds.

* `appname` - The name of the application that created this MongoClient instance. MongoDB 3.4 and newer will print this
 value in the server log upon establishing each connection. It is also recorded in the slow query log and profile
 collections
 
* `loggerLevel` - Specify the log level used by the driver logger (`error/warn/info/debug`).

* `logger` - Specify a customer logger mechanism, can be used to log using your app level logger.

* `authMechanism` - Sets the authentication mechanism that MongoDB will use to authenticate the connection.

## Configurações de `sql.js`

* `database`: The raw UInt8Array database that should be imported.

* `sqlJsConfig`: Optional initialize config for sql.js.

* `autoSave`: Whether or not autoSave should be disabled. If set to true the database will be saved to the given file location (Node.js) or LocalStorage element (browser) when a change happens and `location` is specified. Otherwise `autoSaveCallback` can be used.

* `autoSaveCallback`: A function that get's called when changes to the database are made and `autoSave` is enabled. The function gets a `UInt8Array` that represents the database.

* `location`: The file location to load and save the database to.

* `useLocalForage`: Enables the usage of the localforage library (https://github.com/localForage/localForage) to save & load the database asynchronously from the indexedDB instead of using the synchron local storage methods in a browser environment. The localforage node module needs to be added to your project and the localforage.js should be imported in your page.

## Configurações de `expo`

* `database` - Name of the database. For example "mydb".

## Connection options example

Here is a small example of connection options for mysql:

```typescript
{
    host: "localhost",
    port: 3306,
    username: "test",
    password: "test",
    database: "test",
    logging: true,
    synchronize: true,
    entities: [
        "entity/*.js"
    ],
    subscribers: [
        "subscriber/*.js"
    ],
    entitySchemas: [
        "schema/*.json"
    ],
    migrations: [
        "migration/*.js"
    ],
    cli: {
        entitiesDir: "entity",
        migrationsDir: "migration",
        subscribersDir: "subscriber"
    }
}
```
