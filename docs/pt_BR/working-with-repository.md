# What is Repository
# O que é um Repository

`Repository` é como um `EntityManager` mas seus operações são limitadas a uma entidade.

Você pode acessar o repositório via `getRepository(Entity)`, `Connection#getRepository`, ou `EntityMangager#getRepository`.
Exemplo:

```typescript
import {getRepository} from "typeorm";
import {User} from "./entity/User";

const userRepository = getRepository(User); // você também pode usar via getConnection().getRepository() ou getManager().getRepository()
const user = await userRepository.findOne(1);
user.name = "Umed";
await userRepository.save(user);
```

Há 3 tipos de repositórios:
* `Repository` - Repositório normal para qualquer entidade.
* `TreeRepository` - Repositório extensão de `Repository` usado para tree-entities 
(igual entidades marcadas com decorador `@Tree`). Tem métodos especiais para funcionar com estruturas hierárquicas.
* `MongoRepository` - Repositório com funções especiais usadas apenas com MongoDB.
