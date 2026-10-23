# @jeftelabs/macroable

Uma classe base TypeScript para ampliar classes existentes de fora para dentro: registre métodos no protótipo, propriedades criadas por instância ou getters calculados sem editar a classe original.

## Instalação

```sh
npm install @jeftelabs/macroable
```

O pacote é ESM e exporta `Macroable` como export default. Estenda a classe para habilitar os métodos estáticos:

```ts
import Macroable from '@jeftelabs/macroable'

class Router extends Macroable {
  declare head: (path: string) => string

  route(methods: string[], path: string) {
    return methods.join(',') + ': ' + path
  }
}

Router.macro('head', function (this: Router, path: string) {
  return this.route(['HEAD'], path)
})

new Router().head('/') // "HEAD: /"
```

O pacote fornece runtime e tipos dos métodos de registro. TypeScript não infere automaticamente os membros adicionados dinamicamente: declare-os na classe consumidora ou ajuste a declaração de tipos da classe que está sendo estendida.

## Macros de protótipo

`macro(name, value)` atribui um membro ao protótipo da classe. Todos os objetos da classe veem o mesmo membro. Use uma função regular quando precisar do contexto `this` da instância; uma arrow function mantém o contexto léxico:

```ts
class User extends Macroable {
  declare name: string
  declare greet: () => string
  constructor(name: string) {
    super()
    this.name = name
  }
}

User.macro('greet', function (this: User) {
  return `Olá, ${this.name}`
})

new User('Ada').greet() // "Olá, Ada"
```

Como macros vivem no protótipo, extrair a função e chamar sem receptor perde o `this` implícito. Para métodos que precisam continuar funcionando após desestruturação, use `instanceProperty`.

## Propriedades por instância

`instanceProperty(name, value)` registra uma propriedade que é criada para cada instância no construtor de `Macroable`. Valores funcionais são vinculados à instância, inclusive funções próprias anexadas ao valor funcional:

```ts
class Context extends Macroable {
  declare getUser: () => string
  user = 'Ada'
}

Context.instanceProperty('getUser', function (this: Context) {
  return this.user
})

const { getUser } = new Context()
getUser() // "Ada"
```

A propriedade só é atribuída quando a instância passa pelo construtor de `Macroable`. Inicializadores de campos da classe derivada executam depois de `super()` e podem sobrescrever uma propriedade com o mesmo nome.

As subclasses recebem uma cópia do conjunto herdado de registros quando registram sua primeira propriedade própria, de forma que os novos registros da subclasse não alterem a coleção da classe base. Objetos usados como valores não funcionais são atribuídos como a mesma referência em cada instância; crie o valor dentro da classe se precisar de estado independente.

## Getters

`getter(name, accumulator, singleton?)` define getter no protótipo. O callback é chamado com `this` apontando para a instância. Por padrão, ele é recalculado a cada leitura:

```ts
class User extends Macroable {
  declare upperName: string
  name = 'Ada'
}

User.getter('upperName', function (this: User) {
  return this.name.toUpperCase()
})
```

Com `singleton: true`, a primeira leitura grava o resultado como propriedade própria não gravável e não configurável; as próximas leituras retornam o valor em cache. O getter original não é executado novamente naquela instância. Veja [a referência da API](docs/references/api.md) para detalhes do descritor.

## Tipos

Os genéricos restringem a chave e o valor registrados aos membros já conhecidos no tipo da classe. Para um membro adicionado dinamicamente, declare sua forma na classe consumidora, por exemplo com `declare member: Type`; Macroable não altera automaticamente a interface de instâncias. Para classes de terceiros, ajuste ou augmente a declaração da própria classe conforme a forma como ela exporta os tipos. O export default de Macroable não fornece uma interface nomeada própria para augmentation. Veja [o guia de uso](docs/guides/using-macroable.md).

## Desenvolvimento

```sh
npm install
npm test
npm run typecheck
npm run build
```

O índice da documentação para pessoas e agentes de IA está em [docs/README.md](docs/README.md).
