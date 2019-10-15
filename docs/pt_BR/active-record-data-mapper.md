# Active Record vs Data Mapper

* [O que é o padrão Active Record?](#what-is-the-active-record-pattern)
* [O que é o padrão Data Mapper?](#what-is-the-data-mapper-pattern)
* [Qual eu deveria escolher?](#which-one-should-i-choose)

## O que é o padrão Active Record?

No TypeORM você pode usar dois padrões: o Active Record e o Data Mapper.

Usando a abordagem Active Record, você define todos os seus metodos de `select` no model em si, e você salva, remove e carrega objetos usando os métodos do model.

Em resumo, o padrão Active Record é uma abordagem para acessar seu banco de dados com seus models.
Você pode ler mais sobre o padrão Active Record no [Wikipedia](https://en.wikipedia.org/wiki/Active_record_pattern).

Exemplo:

```typescript
import {BaseEntity, Entity, PrimaryGeneratedColumn, Column} from "typeorm";

@Entity()
export class User extends BaseEntity {
       
    @PrimaryGeneratedColumn()
    id: number;
    
    @Column()
    firstName: string;
    
    @Column()
    lastName: string;
    
    @Column()
    isActive: boolean;

}
```
Toda entidade active-record precisa de um `extends` da classe `BaseEntity`, que provê métodos para trabalhar com a entidade.
Exemplo de como isso funciona:

```typescript

// example how to save AR entity
const user = new User();
user.firstName = "Timber";
user.lastName = "Saw";
user.isActive = true;
await user.save();

// example how to remove AR entity
await user.remove();

// example how to load AR entities
const users = await User.find({ skip: 2, take: 5 });
const newUsers = await User.find({ isActive: true });
const timber = await User.findOne({ firstName: "Timber", lastName: "Saw" });
```

`BaseEntity` tem a maioria dos métodos do `Repository` padrão.
A maior parte do tempo você não precisa usar `Repository` ou `EntityManager` com entidades active-records.

Agora vamos dizer que nós queremos criar a função que retorna os os usuários por nome e sobrenome.
Nos podemos criar muitos métodos estáticos na classe `User`:

```typescript
import {BaseEntity, Entity, PrimaryGeneratedColumn, Column} from "typeorm";

@Entity()
export class User extends BaseEntity {
       
    @PrimaryGeneratedColumn()
    id: number;
    
    @Column()
    firstName: string;
    
    @Column()
    lastName: string;
    
    @Column()
    isActive: boolean;
    
    static findByName(firstName: string, lastName: string) {
        return this.createQueryBuilder("user")
            .where("user.firstName = :firstName", { firstName })
            .andWhere("user.lastName = :lastName", { lastName })
            .getMany();
    }

}
```

E usá-los como outros métodos:

```typescript
const timber = await User.findByName("Timber", "Saw");
```

## O que é o padrão Data Mapper?

No TypeORM você pode usar ambos padrões Active Record e Data Mapper.

Usando a abordagem Data Mapper, você define todos os seus método de `select` separados das classes chamadas de "repositórios", você pode salvar, remover e carregar objetos usando repositorios.
No data-mapper sua entidade é bastante simples - elas apenas definem propriedade e podem ter alguns métodos simples.

Em resumo, data-mapper é uma abordagem para acessar seu banco de dados com repositórios ao invés de models.
Você pode ler mais sobre data-mapper em [Wikipedia](https://en.wikipedia.org/wiki/Data_mapper_pattern).

Exemplo:

```typescript
import {Entity, PrimaryGeneratedColumn, Column} from "typeorm";

@Entity()
export class User {
       
    @PrimaryGeneratedColumn()
    id: number;
    
    @Column()
    firstName: string;
    
    @Column()
    lastName: string;
    
    @Column()
    isActive: boolean;

}
```
Exemplo de como funciona essa entidade:

```typescript
const userRepository = connection.getRepository(User);

// example how to save DM entity
const user = new User();
user.firstName = "Timber";
user.lastName = "Saw";
user.isActive = true;
await userRepository.save(user);

// example how to remove DM entity
await userRepository.remove(user);

// example how to load DM entities
const users = await userRepository.find({ skip: 2, take: 5 });
const newUsers = await userRepository.find({ isActive: true });
const timber = await userRepository.findOne({ firstName: "Timber", lastName: "Saw" });
```

Agora digamos que queremos criar uma função que retorna usuários por nome e sobrenome.
Nós podemos criar várias funcões em um repositório customizado ("custom repository").
Nós podemos criar várias funções em um repositório customizado.

```typescript
import {EntityRepository, Repository} from "typeorm";
import {User} from "../entity/User";

@EntityRepository()
export class UserRepository extends Repository<User> {
       
    findByName(firstName: string, lastName: string) {
        return this.createQueryBuilder("user")
            .where("user.firstName = :firstName", { firstName })
            .andWhere("user.lastName = :lastName", { lastName })
            .getMany();
    }

}
```

E usá-lo assim:

```typescript
const userRepository = connection.getCustomRepository(UserRepository);
const timber = await userRepository.findByName("Timber", "Saw");
```

Veja mais em [custom repositories](custom-repository.md).

## Qual eu deveria escolher?

Essa decisão é sua.
Ambas as estratégias tem seus prós e contras.

Algo que sempre temos que ter em mente no desenvolvimento de software é como nós vamos manter nossas aplicações.
A abordagem `Data Mapper` ajuda na manutenabilidade, que é mais efetiva em aplicações maiores.
A abordagem `Active record` ajuda a manter as coisas simples e funciona bem em aplicações menores.
E simplicidade é sempre a chave para uma melhor manutenabilidade.
