# Desenvolvimento

## Estrutura

- `index.ts`: implementação e export default da classe `Macroable`.
- `tests/macroable.spec.ts`: testes de macro, propriedade de instância, getters e tipagem.
- `bin/test.ts`: configuração do `@t8ngs/runner` e descoberta `tests/**/*.spec.ts`.
- `package.json`: entradas de build, exports, scripts e dependências.
- `tsconfig.json`: base `@arapucajs/tsconfig` e opção do loader.
- `.github/workflows/`: validação de commit, release, publicação e manutenção do repositório.

## Fluxo local

```sh
npm install
npm test
npm run typecheck
npm run build
```

`npm test` executa `pretest` (lint) e depois `quick:test`. `npm run build` passa pelo lint, executa `tsup-node` e emite declarações com TypeScript. `npm run format` altera arquivos; revise o diff depois.

## Documentação obrigatória

Para toda mudança, atualize os documentos em `docs/` na mesma tarefa. Mudanças de comportamento ou tipos devem atualizar referências da API e guias. Mudanças de configuração, scripts ou workflow devem atualizar referências de ferramentas/build. Registre páginas novas no índice `docs/README.md`. Altere o README raiz quando instalação, compatibilidade ou uso público mudar.

## Ao mudar a implementação

- Verifique os três mecanismos separadamente: prototype assignment, static instance registry, e property descriptor.
- Teste `this` em função regular e comportamento diferente de arrow function.
- Para propriedades de instância, considere herança multilayer, função com propriedades próprias e campos inicializados depois de `super()`.
- Para getter singleton, confira cache em cada instância, descriptor e tentativas de reatribuição.
- Não afirme que runtime extensível atualiza os tipos automaticamente.
