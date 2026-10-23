# Usar Macroable

## Instalação e classe consumidora

Instale `@jeftelabs/macroable` e importe o default export. A classe que receberá extensões deve estender `Macroable`:

```ts
import Macroable from '@jeftelabs/macroable'

class Router extends Macroable {
  declare route: (methods: string[], path: string) => string
  route(methods: string[], path: string) {
    return `${methods.join(',')}: ${path}`
  }
}
```

## Registrar método compartilhado

Use `macro` para compartilhar um valor via protótipo. Métodos devem ser funções normais para receber `this` no objeto chamador:

```ts
Router.macro('head', function (this: Router, path: string) {
  return this.route(['HEAD'], path)
})

new Router().head('/')
```

Uma arrow function captura `this` léxico e não recebe o contexto da instância. A implementação não liga funções adicionadas ao protótipo.

## Registrar função por instância

Use `instanceProperty` quando a função precisar continuar funcionando após ser extraída:

```ts
class User extends Macroable {
  declare displayName: () => string
  name = 'Ada'
}

User.instanceProperty('displayName', function (this: User) {
  return this.name
})

const { displayName } = new User()
displayName() // "Ada"
```

Como o valor é instalado em `super()`, campos inicializados depois podem substituir o membro. Para funcionar, declare o membro sem inicializador (por exemplo `declare`) ou cuide da ordem explicitamente.

Valores de função são ligados por instância. Evite compartilhar valores de objeto mutáveis como propriedade se cada instância deve ter cópia própria.

## Adicionar getter

```ts
class User extends Macroable {
  declare label: string
  name = 'Ada'
}

User.getter('label', function (this: User) {
  return this.name.toUpperCase()
})
```

Sem o terceiro argumento, cada leitura chama o accumulator. Com `true`, a primeira leitura define uma propriedade própria, congelada para substituição e sem enumerabilidade; alterações posteriores de `name` não mudam esse valor em cache. Leia a referência para detalhes do descritor.

## Tipos TypeScript

Declare os membros dinâmicos na classe por `declare` ou use module augmentation da classe/interface consumidora. Os genéricos de `macro`, `instanceProperty` e `getter` validam os nomes e tipos que a classe já declara; eles não alteram automaticamente a interface estática da instância.

## Quando escolher cada mecanismo

| Necessidade | Mecanismo |
| --- | --- |
| Valor compartilhado no protótipo; método chamado com receptor | `macro` |
| Função com `this` que deve sobreviver à desestruturação | `instanceProperty` |
| Valor calculado sob demanda | `getter` |
| Valor calculado uma vez por instância | `getter(name, fn, true)` |
