# Criando cache de queries

Você pode criar cache dos resultados usando métodos de `QueryBuilder`: `getMany`, `getOne`, `getRawMany`, `getRawOne` and `getCount`.

Você também pode criar cache para os resultados selecionados por esses métodos do `Repository`: `find`, `findAndCount`, `findByIds`, and `count`.

Para habilitar o cache você precisa explicitamente ativar nas opções da sua conexão:

```typescript
{
    type: "mysql",
    host: "localhost",
    username: "test",
    ...
    cache: true
}
```

Quando você habilita o cache pela primeira vez, você precisa sincronizar seus esquema de banco de dados (usando CLI, migrações ou a opção `synchronize` nas configurações da conexão).

Então em um `QueryBuilder` você pode habilitar o "cacheamento" para qualquer query:

```typescript
const users = await connection
    .createQueryBuilder(User, "user")
    .where("user.isAdmin = :isAdmin", { isAdmin: true })
    .cache(true)
    .getMany();
```

Equivalente usando `Repository`:
```typescript
const users = await connection
    .getRepository(User)
    .find({
        where: { isAdmin: true },
        cache: true
    });
```

Isto irá executar a query para buscar todos os usuários admin e cachear os resultados.
A próxima vez que você executar o mesmo código, irá pegar todos os usuários admin do cache.
O tempo padrão que o cache é guardado é de `1000 ms` ou 1 segundo.
Isso significa que o cache não será mais válido 1 segundo após o cache ter sido criado.
Na prática, isso significa que se os usuários abrirem a mesma página 150 vezes em 3 segundos, apenas três queries serão executadas neste período.
Qualquer usuário inserido durante este 1 segundo de validade do cache não será retornado.

Você pode alterar o tempo de cache manualmente via `QueryBuilder`:

```typescript
const users = await connection
    .createQueryBuilder(User, "user")
    .where("user.isAdmin = :isAdmin", { isAdmin: true })
    .cache(60000) // 1 minute
    .getMany();
```

Ou via `Repository`:

```typescript
const users = await connection
    .getRepository(User)
    .find({
        where: { isAdmin: true },
        cache: 60000
    });
```

Ou globalmente nas configurações da conexão:

```typescript
{
    type: "mysql",
    host: "localhost",
    username: "test",
    ...
    cache: {
        duration: 30000 // 30 seconds
    }
}
```

Também é possivel usar um id para o cache via `QueryBuilder`:

```typescript
const users = await connection
    .createQueryBuilder(User, "user")
    .where("user.isAdmin = :isAdmin", { isAdmin: true })
    .cache("users_admins", 25000)
    .getMany();
```

Ou com `Repository`:
```typescript
const users = await connection
    .getRepository(User)
    .find({
        where: { isAdmin: true },
        cache: {
            id: "users_admins",
            milliseconds: 25000
        }
    });
```

Isto dá um controle melhor para seu cache, por exemplo, limpar os resultados guardados quando inserir um novo usuário:

```typescript
await connection.queryResultCache.remove(["users_admins"]);
```

Por padrão, o TypeORM usa uma tabela separada chamada `query-result-cache` onde guarda todas a queries e resultados.
O nome da tabela é configurável, então você pode alterá-lo usando a propriedade `tableName`.
Exemplo:

```typescript
{
    type: "mysql",
    host: "localhost",
    username: "test",
    ...
    cache: {
        type: "database",
        tableName: "configurable-table-query-result-cache"
    }
}
```

Se usar uma única tabela do banco de dados não é eficiente para você,
você pode alterar o tipo de cache usado para "redis" ou "ioredis" e o TypeORM irá guardar todos os registros cacheados na instância do redis.
Exemplo:

```typescript
{
    type: "mysql",
    host: "localhost",
    username: "test",
    ...
    cache: {
        type: "redis",
        options: {
            host: "localhost",
            port: 6379
        }
    }
}
```

"options" podem ser [node_redis specific options](https://github.com/NodeRedis/node_redis#options-object-properties) ou [ioredis specific options](https://github.com/luin/ioredis/blob/master/API.md#new-redisport-host-options) , dependendo de que tipo você está usando.


No caso de você querer conectar em um redis-cluster usando a funcionalidade cluster do IORedis, você pode fazê-lo da seguinte maneira:

```typescript
{
    type: "mysql",
    host: "localhost",
    username: "test",
    cache: {
        type: "ioredis/cluster",
        options: {
            startupNodes: [
                {
                    host: 'localhost',
                    port: 7000,
                },
                {
                    host: 'localhost',
                    port: 7001,
                },
                {
                    host: 'localhost',
                    port: 7002,
                }
            ],
            options: {
                scaleReads: 'all',
                clusterRetryStrategy: function (times) { return null },
                redisOptions: {
                    maxRetriesPerRequest: 1
                }
            }
        }
    }
}
```

Veja, você ainda pode usar "options" como primeiro argumento do construtor de cluster do IORedis.
```typescript
{
    ...
    cache: {
        type: "ioredis/cluster",
        options: [
            {
                host: 'localhost',
                port: 7000,
            },
            {
                host: 'localhost',
                port: 7001,
            },
            {
                host: 'localhost',
                port: 7002,
            }
        ]
    },
    ...
}
```

Você pode usar `typeorm cache:clear` para limpar tudo guardado em cache.
