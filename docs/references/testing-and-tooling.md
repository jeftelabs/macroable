# Referência: testes e ferramentas

## Pipeline de testes

`npm test` executa o lifecycle `pretest` que chama `npm run lint`, e então o script `test` que chama `npm run quick:test`. O runner usa `node --import=@jeftelabs/exec --enable-source-maps bin/test.ts`. `bin/test.ts` configura `@t8ngs/runner`, carrega `tests/**/*.spec.ts` e instala plugins de assertion e verificação de tipos.

## Configuração ESLint

`eslint.config.js` importa `configPkg` do pacote `@arapucajs/eslint-config`. O nome deve coincidir exatamente com a dependência declarada no `package.json` e no lockfile. Uma grafia diferente falha durante o import da configuração antes de qualquer regra ser executada.

## Configuração TypeScript

`tsconfig.json` estende `@arapucajs/tsconfig/tsconfig.package.json`; a dependência precisa ser instalada. `rewriteRelativeImportExtensions: true` dá suporte a imports `.ts` usados pelo runner/loader. `rootDir` e `outDir` definem a organização da compilação.

## Scripts declarados

- `npm run lint`: executa ESLint.
- `npm test`: lint mais runner de teste.
- `npm run quick:test`: runner sem cobertura.
- `npm run typecheck`: `tsc --noEmit`.
- `npm run build`: lint pelo lifecycle `precompile`, bundle por `tsup-node` e declarações por `tsc`.
- `npm run format`: Prettier com escrita.

Confirme `package.json` caso scripts mudem.

## Diagnóstico de `npm test`

1. Erro ao importar `eslint.config.js`: compare o nome do módulo com `devDependencies` e o lockfile.
2. Erro Prettier/ESLint: corrija o arquivo indicado; então o lifecycle prossegue.
3. Erro `File ... tsconfig ... not found`: confira o `extends` e instalação do pacote base TypeScript.
4. Erro após o runner iniciar: examine arquivo/teste indicado; já não é falha de bootstrap.

No estado documentado desta correção, `eslint.config.js` e `tsconfig.json` usam `@arapucajs`, e o runner passa depois da formatação de `bin/test.ts`.
