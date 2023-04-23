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

### OCP - Open-Closed Principle
- As classes devem estar abertas para extensão, mas fechadas para modificação;
- Modificação significa alterar o código de uma classe existente, enquanto extensão significa adicionar novas funcionalidades;

> Como podemos adicionar novas funcionalidades sem tocar na classe? Geralmente, isso é feito com o auxílio de interfaces e classes abstratas. Caso precisamos adicionar uma nova funcionalidade, adicionamos na interface e as classes implementam essa interface;

```js
export interface UserRepository{
  salvar(user: User): Promise<void>;
  buscar(user_id: string): Promise<User | null>;
}

export class PersistenciaEmBD implements UserRepository{
  async salvar(user: User){
    // Salvar usuário no BD
  }

  async buscar(user_id: string){
    // Buscar o usuário no banco de dados
  }
}

export class PersistenciaEmArquivo implements UserRepository{
  async salvar(user: User){
    // Salvar usuário no arquivo
  }

  async buscar(user_id: string){
    // Buscar o usuário no arquivo
  }
}
```

### LSP - Liskov Substitution Principle

### ISP - Interface Segregation Principle

### DIP - Dependency Inversion Principle