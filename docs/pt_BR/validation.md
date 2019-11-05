# Usando validações

Para usar validações use [class-validator](https://github.com/pleerock/class-validator). 
Exemplo de como usar class-validator com TypeORM:

```typescript
import {Entity, PrimaryGeneratedColumn, Column} from "typeorm";
import {Contains, IsInt, Length, IsEmail, IsFQDN, IsDate, Min, Max} from "class-validator";

@Entity()
export class Post {

    @PrimaryGeneratedColumn()
    id: number;
    
    @Column()
    @Length(10, 20)
    title: string;

    @Column()
    @Contains("hello")
    text: string;

    @Column()
    @IsInt()
    @Min(0)
    @Max(10)
    rating: number;

    @Column()
    @IsEmail()
    email: string;

    @Column()
    @IsFQDN()
    site: string;

    @Column()
    @IsDate()
    createDate: Date;

}
```

Validação:

```typescript
import {getManager} from "typeorm";
import {validate} from "class-validator";

let post = new Post();
post.title = "Hello"; // não deve passar
post.text = "this is a great post about hello world"; // não deve passar
post.rating = 11; // não deve passar
post.email = "google.com"; // não deve passar
post.site = "googlecom"; // não deve passar

const errors = await validate(post);
if (errors.length > 0) {
    throw new Error(`Não passou na validação!`); 
} else {
    await getManager().save(post);
}
```