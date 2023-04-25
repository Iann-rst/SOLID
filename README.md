# O QUE É SOLID: OS 5 PRINCÍPIOS DA POO

SOLID são cinco princípios da programação orientada a objeto (POO) que facilitam no desenvolvimento de softwares.

## S.O.L.I.D: Os 5 princípios da POO

- **S - Single Responsibility Principle** (Princípio da responsabilidade única);
- **O - Open-Closed Principle** (Princípio Aberto-Fechado);
- **L - Liskov Substitution Principle** (Princípio da substituição de Liskov);
- **I - Interface Segregation Principle** (Princípio da Segregação da Interface);
- **D - Dependency Inversion Principle** (Princípio da inversão da dependência);

### SRP - Single Responsibility Principle

- Uma classe deve ter um, e somente um, motivo para mudar;
- Uma classe deve fazer apenas uma coisa e, portanto, deve ter apenas uma razão para ser modificada;

##### Exemplo:

- Considere um serviço (use-case) de **CRUD** de usuários, por exemplo:

```ts
export class UsersUseCase {
  create() {
    // Lógica para criar usuário
  }

  read() {
    // Lógica para buscar usuário
  }

  update() {
    // Lógica para atualizar o usuário
  }

  delete() {
    // Lógica para deletar um usuário
  }
}
```

- Aplicando o SRP, você terá um serviço (use-case) responsável apenas pelo **Create (criar)**, outro pelo **Read (ler)**, outro pelo **Update (atualizar)** e outro para o **Delete (excluir)**;

```ts
 /* ../../use-cases*/

   export class CreateUsersUseCase(){
    // Lógica para criar usuário
   }

   export class GetUsersUseCase(){
    // Lógica para buscar usuário
   }

   export class UpdateUsersUseCase(){
    // Lógica para atualizar o usuário
   }

   export class DeleteUsersUseCase(){
    // Lógica para deletar um usuário
   }
```

- Dessa forma, cada classe tem apenas uma única responsabilidade e é mais fácil de entender, manter e testar. Além disso, se houver uma mudança em uma responsabilidade específica, isso não afetará as outras responsabilidades.

### OCP - Open-Closed Principle

- As classes devem estar abertas para extensão, mas fechadas para modificação;
- Modificação significa alterar o código de uma classe existente, enquanto extensão significa adicionar novas funcionalidades;
- Isso significa que o comportamento de uma entidade deve poder ser estendido sem que seja necessário modificar seu código fonte;

### LSP - Liskov Substitution Principle

- Os objetos de uma classe derivada devem ser substituíveis por objetos de sua classe base sem quebrar o comportamento do programa. Isso significa que uma classe derivada deve ser capaz de substituir sua classe base sem que haja efeitos colaterais indesejados;

#### Exemplo:

- Vamos considerar um use-case de persistir um usuário:

```ts
export class CreateUsersUseCase {
  constructor(private usersRepository: IUsersRepository) {}

  async execute(data: ICreateUserRequestDTO) {
    // Logica para buscar se já existe esse usuário cadastrado
    const userAlreadyExists = await this.usersRepository.findByEmail(
      data.email
    );

    // Se usuário já existe, lança um erro de usuário já cadastrado
    if (userAlreadyExists) {
      throw new Error("User already exists");
    }

    // Cria o novo usuário
    const user = new User(data);

    // Persiste o usuário
    await this.usersRepository.create(user);
  }
}
```

- Estamos aplicando o LSP, a partir do momento que nosso constructor da classe **CreateUsersUseCase** recebe nosso `usersRepository` do tipo `IUsersRepository`, que é uma interface (contrato) para definir quais métodos devem existir dentro do repositório.

- Não importa qual repositório eu passe para o constructor dessa classe, seja um repositório do Mongo, PostgreSQL, SQL ou até mesmo um repositório em memória.
- Desde que esse repositório tenha e implemente os métodos da interface `IUsersRepository`, posso substituir o repositório que ele irá funcionar sem quebrar o código.

### ISP - Interface Segregation Principle

- Consiste em segregar as interfaces que você tem no seu sistema em interfaces menores, deixando elas mais modulares.

##### Exemplo:

- Considere uma interface **IPessoa** com métodos para obter informações pessoais e métodos para obter informações profissionais. Se um cliente precisar apenas das informações pessoais, ele não deveria ser forçado a implementar os métodos de informações profissionais. Em vez disso, a interface **IPessoa** deve ser dividida em duas interfaces separadas, uma para informações pessoais e outra para informações profissionais;

### DIP - Dependency Inversion Principle

- As dependências devem ser abstraídas em vez de serem concretas. Isso significa que as classes de alto nível não devem depender de classes de baixo nível, mas sim de abstrações que definem a interface entre as classes;

##### Exemplo:

- Suponha que temos uma classe **CreateUsersUseCase** que depende de um **Database** para persistir seus dados:

```ts
class CreateUsersUseCase {
  private database: Database;

  constructor() {
    this.database = new Database();
  }

  async execute(user) {
    // código de exemplo para inserir os dados do usuário em um banco de dados SQL
    "INSERT INTO USER (name, email, password) VALUES (user.name, user.email, user.password)";
    await this.database.insert(user);
  }
}
```

- Nesse exemplo, a classe **CreateUsersUseCase** é responsável por criar uma instância do **Database** e chamar seu método **insert()** para persistir o usuário no banco de dados. Isso cria uma dependência direta entre **CreateUsersUseCase** e um determinado **Database**, o que torna difícil testar e reutilizar o código;

- Para aplicar o DIP, podemos introduzir uma interface **IUsersRepository** que define o contrato para persistir dados:

```ts
interface IUsersRepository {
  create(user: User): Promise<void>;
  // ... outros métodos
  findByEmail(email: string): Promise<User | null>;
}
```

- Em seguida, podemos fazer com que a classe **CreateUsersUseCase** dependa da interface **IUsersRepository**, em vez do **Database** diretamente:

```ts
class CreateUsersUseCase {
  constructor(private usersRepository: IUsersRepository) {}

  async execute(user) {
    await this.usersRepository.create(user);
  }
}
```

- Agora, podemos criar uma nova classe **DatabaseRepository** que implementa a interface **IUsersRepository**:

```ts
class DatabaseRepository implements IUsersRepository {
  create(user: User) {
    // código de exemplo para inserir os dados do usuário em um banco de dados SQL
    "INSERT INTO USER (name, email, password) VALUES (user.name, user.email, user.password)";
  }
}
```

- E, finalmente, podemos passar uma instância da classe **DatabaseRepository** para o constructor da classe **CreateUsersUseCase**:

```ts
const databaseRepository = new DatabaseRepository();
const createUsersUseCase = new CreateUsersUseCase(databaseRepository);
createUsersUseCase.execute(user);
```

- Com essa abordagem, a classe **CreateUsersUseCase** não depende mais diretamente de um **Database**. Em vez disso, ela depende da interface **IUsersRepository** _(abstração da persistência)_, que pode ser implementada por várias outras classes diferentes (banco de dados diferentes: Mongo, PostgreSQL, SQLite, MySQL).
