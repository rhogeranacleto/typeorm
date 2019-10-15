# Que é um EntityManager

Usando `EntityManager` você pode gerir (inserir, atualizar, deletar, carregar e etc.) qualquer entidade.
EntityManager é como uma coleção de todos os repositórios das entidades em um único lugar.

Você pode acessar a entity manager via `getManager()` ou usando `Connection`.
Exemplo de como usar:
 
```typescript
import {getManager} from "typeorm";
import {User} from "./entity/User";

const entityManager = getManager(); // você também pode usar via getConnection().manager
const user = await entityManager.findOne(User, 1);
user.name = "Umed";
await entityManager.save(user);
```
