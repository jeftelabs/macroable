# Referência da implementação e semântica

## Estado estático e herança

`instanceMacros` é uma propriedade estática protegida inicializada com `Set`. Uma classe derivada a herda via cadeia de construtores. `instanceProperty` verifica `hasOwnProperty`; quando o conjunto ainda é herdado, cria um novo Set a partir dele, com descriptor configurável, enumerável e gravável. Isso mantém os registros herdados e separa futuras adições ao descendente.

O construtor usa `this.constructor` para ler o conjunto da classe mais específica, então os registros de base e descendentes são aplicados ao instanciar a classe derivada.

## Ligação de funções

Para uma função registrada como propriedade de instância, o construtor cria `value.bind(this)`. Ele percorre `Object.keys(value)` e copia cada chave própria enumerável para a função vinculada. A implementação tenta executar `.bind(this)` em cada valor encontrado; portanto valores não funcionais anexados à função causam erro na construção. Símbolos e propriedades não enumeráveis ficam fora desse processo. A cobertura atual verifica propriedades anexadas cujo valor também é função.

## Propriedades de protótipo

`macro` usa atribuição direta. `getter` usa `Object.defineProperty` e declara os accessors não enumeráveis. O descriptor de singleton criado na instância é imutável em relação à reatribuição e não configurável.

## Testes existentes

A suíte em `tests/macroable.spec.ts` cobre:

- macro como valor e função normal, além do comportamento lexical da arrow;
- chamada e desestruturação de propriedades de instância;
- cópia e binding de propriedades funcionais próprias;
- registro de instância através de herança em vários níveis;
- getter recalculado, getter singleton, contexto `this`, desestruturação, novo registro e erros de escrita.

A suíte testa runtime local. Não é um teste de compatibilidade entre versões diferentes de Node, nem valida execução do pacote empacotado instalado fora do workspace.

## Cuidados para evoluções

Mudanças em `instanceMacros` podem afetar classes derivadas que já compartilham uma coleção. Mudanças no descriptor podem ser incompatíveis para usuários que redefinem getters. Mudanças na vinculação de funções devem preservar propriedades próprias da função conforme os testes atuais.
