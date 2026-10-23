# Referência da API pública

## Export

O módulo raiz exporta a classe abstrata `Macroable` como default. Para criar uma classe utilizável, estenda-a e implemente a lógica específica da classe consumidora.

## `Macroable.macro(name, value)`

Atribui `value` diretamente em `this.prototype[name]`. É compartilhado pelo protótipo e não cria propriedade própria nas instâncias. `name` e `value` são limitados pelos tipos aos membros já declarados no tipo da classe.

- Entradas: chave de membro e valor compatível.
- Efeito: atribuição comum ao protótipo do construtor que recebeu a chamada.
- Contexto: quando value é função regular chamada como membro, o JavaScript define `this` para a instância; quando extraída, esse contexto não é preservado automaticamente. Arrow functions mantêm contexto léxico.
- Resultado: `void`.

## `Macroable.instanceProperty(name, value)`

Registra chave/valor na coleção estática `instanceMacros` da classe. No construtor base, cada registro é aplicado ao objeto em construção.

- Se a função é chamada numa subclasse que ainda herda `instanceMacros`, primeiro é criada uma coleção própria copiando o conjunto herdado.
- Funções são ligadas ao objeto. As propriedades próprias enumeráveis da função são copiadas para a função ligada; propriedades que sejam funções também são ligadas ao objeto.
- Valores não funcionais são atribuídos sem clonagem.
- Atribuição ocorre durante `super()`, antes de inicializadores de campo da classe derivada.
- Resultado: `void`.

A declaração TypeScript precisa incluir a chave como membro de `InstanceType` para passar pela checagem estática.

## `Macroable.getter(name, accumulator, singleton = false)`

Define no protótipo uma propriedade com descriptor getter. O accumulator é chamado com `this` igual ao objeto que lê o getter.

Para getter não singleton: `configurable: true`, `enumerable: false`, sem setter; cada leitura calcula novamente.

Para getter singleton: a primeira leitura define no objeto uma propriedade própria com o valor retornado, `configurable: false`, `enumerable: false`, `writable: false`. Leituras seguintes acessam essa propriedade própria e não chamam o accumulator novamente. O getter do protótipo permanece configurável; registrar novamente pode afetar instâncias que ainda não tenham cache, enquanto caches próprios existentes permanecem.

- Entradas: chave, função síncrona de acumulação e opção booleana.
- Resultado: `void`.

## Classe abstrata e construtores

`Macroable` não foi projetada para instanciar diretamente. O construtor chama o `instanceMacros` do construtor mais derivado (`this.constructor`). Classes derivadas devem chamar `super()` para aplicar registros de instância.

## Tipos dinâmicos

Os genéricos restringem o nome e o valor em relação ao construtor, mas o registro runtime não faz module augmentation. Consumidores precisam declarar os membros adicionados no tipo de sua classe.
