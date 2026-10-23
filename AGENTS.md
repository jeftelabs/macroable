# Instruções para agentes — @jeftelabs/macroable

Leia este arquivo antes de alterar o projeto. Código, testes, package.json, configuração TypeScript e documentação são fontes de verdade.

## Escopo

@jeftelabs/macroable exporta uma classe abstrata default que permite adicionar macros ao protótipo, propriedades aplicadas por instância e getters calculados ou singleton. A implementação fica em index.ts, e a cobertura atual fica em tests/macroable.spec.ts.

## Documentação obrigatória

É obrigatório criar ou atualizar documentação detalhada sempre que algo novo for criado ou qualquer parte do projeto for modificada, incluindo código, API, testes, configuração, dependências, scripts, CI, exemplos e os próprios documentos. Conclua a atualização na mesma tarefa.

- Explique propósito, fluxo, entradas, saídas, efeitos, limites e manutenção para humanos e agentes de IA.
- Organize documentos em docs/ e registre cada página no índice docs/README.md.
- Atualize o README quando instalação, API pública, compatibilidade ou exemplos mudarem.
- Valide afirmações e exemplos no código e nos testes.

## Antes de alterar

1. Leia a referência pertinente em docs/README.md.
2. Examine index.ts, testes, exports/scripts/dependências no package.json, tsconfig e workflows.
3. Preserve a diferença entre macros de protótipo, propriedades de instância e getters.
4. Verifique herança, contexto this, símbolos e cache de getters nos casos afetados.

## Comandos

- npm run lint: ESLint.
- npm test: lint pelo lifecycle pretest e testes pelo runner do projeto.
- npm run quick:test: inicia testes sem cobertura.
- npm run typecheck: TypeScript sem emissão.
- npm run build: bundle e declarações TypeScript.
- npm run format: aplica Prettier aos arquivos.

Confirme os scripts reais em package.json. Formatação pode modificar arquivos; revise o diff.

## Convenções

- Mantenha ESM e o export default definidos pelo pacote.
- Mantenha tsconfig.json e imports .ts compatíveis com @jeftelabs/exec.
- Não edite artefatos gerados em build/ como fonte.
- Use Conventional Commits; faça commit, push e PR apenas quando solicitados.
- Preserve alterações preexistentes e não exponha credenciais.
