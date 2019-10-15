# View Entities

* [O que é uma View Entity?](#what-is-view-entity)                    
* [View Entity columns](#view-entity-columns)
* [Exemplo completo](#complete-example)

## O que é uma View Entity?

View entity é uma classe que mapeia uma view do banco de dados.
Você pode cirar a view entity definindo uma nova classe e marcando-a com `@ViewEntity()`:

`@ViewEntity()` aceita as seguintes opções:

* `name` - nome da view. Se não especificado, então o nome da view será generada de acordo com o nome da classe.
* `database` - nome do banco de dados na seleção do DB server.
* `schema` - nome do schema.
* `expression` - definição da view. **Obrigatório**.

`expression` pode ser uma string com as colunas e as tabelas, dependendo do banco de dados usado(exemplo em postgres):

```typescript
@ViewEntity({ 
    expression: `
        SELECT "post"."id" "id", "post"."name" AS "name", "category"."name" AS "categoryName"
        FROM "post" "post"
        LEFT JOIN "category" "category" ON "post"."categoryId" = "category"."id"
    `
})
```

ou uma instancia de QueryBuilder

```typescript
@ViewEntity({ 
    expression: (connection: Connection) => connection.createQueryBuilder()
        .select("post.id", "id")
        .addSelect("post.name", "name")
        .addSelect("category.name", "categoryName")
        .from(Post, "post")
        .leftJoin(Category, "category", "category.id = post.categoryId")
})
```

**Nota:** a opção "parâmetro" não é suportada devido a limitações. Use parâmetros literais ao invés disso.

```typescript
@ViewEntity({ 
    expression: (connection: Connection) => connection.createQueryBuilder()
        .select("post.id", "id")
        .addSelect("post.name", "name")
        .addSelect("category.name", "categoryName")
        .from(Post, "post")
        .leftJoin(Category, "category", "category.id = post.categoryId")
        .where("category.name = :name", { name: "Cars" })  // <-- errado
        .where("category.name = 'Cars'")                   // <-- certo
})
```

Cada view entity deve ser registrado nas suas opções da conexão:

```typescript
import {createConnection, Connection} from "typeorm";
import {UserView} from "./entity/UserView";

const connection: Connection = await createConnection({
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "test",
    password: "test",
    database: "test",
    entities: [UserView]
});
```

Ou você pode especificar o diretório completo com todas as entidades - e todas serão carregadas:

```typescript
import {createConnection, Connection} from "typeorm";

const connection: Connection = await createConnection({
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "test",
    password: "test",
    database: "test",
    entities: ["entity/*.js"]
});
```

## View Entity columns

Para mapear os dados vindo da view nas colunas corretas você deve marcar as colunas com decorador `@ViewColumn()` e especificar essas colunas como alias da query.

exemplo com query string:

```typescript
import {ViewEntity, ViewColumn} from "typeorm";

@ViewEntity({ 
    expression: `
        SELECT "post"."id" AS "id", "post"."name" AS "name", "category"."name" AS "categoryName"
        FROM "post" "post"
        LEFT JOIN "category" "category" ON "post"."categoryId" = "category"."id"
    `
})
export class PostCategory {

    @ViewColumn()
    id: number;

    @ViewColumn()
    name: string;

    @ViewColumn()
    categoryName: string;

}
```

exemplo usando QueryBuilder:

```typescript
import {ViewEntity, ViewColumn} from "typeorm";

@ViewEntity({ 
    expression: (connection: Connection) => connection.createQueryBuilder()
        .select("post.id", "id")
        .addSelect("post.name", "name")
        .addSelect("category.name", "categoryName")
        .from(Post, "post")
        .leftJoin(Category, "category", "category.id = post.categoryId")
})
export class PostCategory {

    @ViewColumn()
    id: number;

    @ViewColumn()
    name: string;

    @ViewColumn()
    categoryName: string;

}
```

## Complete example

Let create two entities and a view containing aggregated data from these entities:

```typescript
import {Entity, PrimaryGeneratedColumn, Column} from "typeorm";

@Entity()
export class Category {

    @PrimaryGeneratedColumn()
    id: number;

    @Column()
    name: string;

}
```

```typescript
import {Entity, PrimaryGeneratedColumn, Column, ManyToOne, JoinColumn} from "typeorm";
import {Category} from "./Category";

@Entity()
export class Post {

    @PrimaryGeneratedColumn()
    id: number;

    @Column()
    name: string;

    @Column()
    categoryId: number;

    @ManyToOne(() => Category)
    @JoinColumn({ name: "categoryId" })
    category: Category;

}
```

```typescript
import {ViewEntity, ViewColumn} from "typeorm";

@ViewEntity({ 
    expression: (connection: Connection) => connection.createQueryBuilder()
        .select("post.id", "id")
        .addSelect("post.name", "name")
        .addSelect("category.name", "categoryName")
        .from(Post, "post")
        .leftJoin(Category, "category", "category.id = post.categoryId")
})
export class PostCategory {

    @ViewColumn()
    id: number;

    @ViewColumn()
    name: string;

    @ViewColumn()
    categoryName: string;

}
```

then fill these tables with data and request all data from PostCategory view:

```typescript
import {getManager} from "typeorm";
import {Category} from "./entity/Category";
import {Post} from "./entity/Post";
import {PostCategory} from "./entity/PostCategory";

const entityManager = getManager();

const category1 = new Category();
category1.name = "Cars";
await entityManager.save(category1);

const category2 = new Category();
category2.name = "Airplanes";
await entityManager.save(category2);

const post1 = new Post();
post1.name = "About BMW";
post1.categoryId = category1.id;
await entityManager.save(post1);

const post2 = new Post();
post2.name = "About Boeing";
post2.categoryId = category2.id;
await entityManager.save(post2);

const postCategories = await entityManager.find(PostCategory);
const postCategory = await entityManager.findOne(PostCategory, { id: 1 });
```

the result in `postCategories` will be:

```
[ PostCategory { id: 1, name: 'About BMW', categoryName: 'Cars' },
  PostCategory { id: 2, name: 'About Boeing', categoryName: 'Airplanes' } ]
```

and in `postCategory`:

```
PostCategory { id: 1, name: 'About BMW', categoryName: 'Cars' }
```


