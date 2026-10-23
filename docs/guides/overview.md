# Visão geral e arquitetura

## Propósito

`Macroable` ajuda bibliotecas e aplicações a estender classes de terceiros ou classes centrais de um framework sem editar suas declarações originais. A classe base oferece três mecanismos distintos:

- `macro`: instala um valor compartilhado no protótipo.
- `instanceProperty`: registra um valor que o construtor instala em cada objeto.
- `getter`: instala um accessor de protótipo, recalculado ou memorizado por instância.

A implementação é um único módulo, `index.ts`, que exporta a classe abstrata como default. Não há estado global entre classes: os registros de propriedades são mantidos em uma propriedade estática herdável da classe.

## Fluxo de instanciação

1. Uma classe consumidora estende `Macroable`.
2. Chamadas estáticas registram macros/getters no protótipo ou propriedades na coleção estática da classe.
3. O construtor da instância determina o construtor mais derivado (`this.constructor`) e percorre a coleção `instanceMacros` visível nessa classe.
4. Cada registro é atribuído à instância. Funções são vinculadas ao objeto, e funções anexadas como propriedades próprias da função também são vinculadas.
5. A inicialização dos campos da classe derivada ocorre depois de `super()`; portanto um campo com mesmo nome pode sobrescrever uma propriedade registrada.

## Herança

Uma classe derivada herda inicialmente a coleção estática da classe base. Na primeira chamada a `instanceProperty` feita diretamente na derivada, a implementação cria uma coleção própria copiando os registros herdados; isso isola os registros novos de pai e filha. Cada instância consulta a coleção da sua classe mais derivada. Os testes exercitam herança em mais de um nível.

Macros e getters são adicionados diretamente ao protótipo do receptor da chamada estática. Uma atribuição em um membro herdado pode sobrescrever o comportamento observado por descendentes, conforme a resolução normal de protótipos.

## Tipagem

Os métodos genéricos limitam `name` e `value` a `keyof InstanceType<T>`. Isso valida uma declaração de membro já presente no tipo da classe. A implementação em runtime aceita membros dinâmicos, mas o compilador não os adiciona automaticamente às instâncias. O consumidor precisa declarar/augmentar os tipos da própria classe.

## Limites conhecidos

- A classe base não define classes consumidoras, nomes de macros ou validações próprias de API; é o código que chama o registro que escolhe esses contratos.
- Macros são membros do protótipo, portanto não são próprios da instância e métodos extraídos podem perder contexto.
- Propriedades de instância ligam funções ao objeto, mas copiam valores de propriedades próprias das funções por enumeração de chaves próprias. Símbolos e propriedades não enumeráveis da função não são copiadas.
- Valores não funcionais são atribuídos diretamente; referências mutáveis não são clonadas.
- Getters singleton guardam valor próprio e não gravável no objeto que foi lido. Cada objeto tem seu próprio cache.
- A suíte cobre comportamento síncrono. Não há suporte especial a valores assíncronos; uma Promise retornada por getter é simplesmente o valor calculado/cacheado.
