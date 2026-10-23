# Referência: build, pacote e publicação

## Metadados e artefatos

`package.json` publica ESM e aponta `main` e export raiz para `build/index.js`. O tsup tem entrada `index.ts`, limpa/gera `build/`, usa formato ESM e não gera declarations; o script `compile` executa também `tsc --emitDeclarationOnly --declaration`. O campo `files` publica `build` e exclui `bin` e `tests`.

Não há campo `engines` em `package.json`; portanto não declare uma faixa oficial de versões Node baseada somente no repositório. O alvo de build `esnext` reflete a configuração do pacote, não uma matriz de compatibilidade testada.

## Scripts de publicação

`prepublishOnly` chama `npm run build`. O workflow deploy, em release publicada, constrói e publica em GitHub Packages e npm. O workflow release-please executa em push para `main`, gerencia a proposta/tag de release e anexa um artefato empacotado quando uma release é criada.

## Workflows de repositório

- `checks.yml` declara apenas evento `push` para `main` e valida o último commit com commitlint; contém também passo condicional para pull_request, mas esse evento não está declarado no gatilho atual.
- `release.yml` executa Release Please em push para `main` e faz upload de artefato de pacote após release criada.
- `deploy.yml` publica pacotes após evento `release` publicado.
- `labels.yml` sincroniza labels sob acionamento manual.
- `stale.yml` agenda marcação/fechamento de issues e PRs inativos.

Revise os YAMLs antes de assumir gatilhos, permissões ou requisitos de publicação; workflows e secrets podem mudar independentemente deste documento.
