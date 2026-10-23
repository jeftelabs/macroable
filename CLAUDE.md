# Orientações para Claude e agentes de IA

As instruções canônicas estão em AGENTS.md. Consulte também docs/README.md e as referências ligadas pelo índice. Este repositório contém @jeftelabs/macroable, uma classe base para adicionar macros de protótipo, propriedades por instância e getters.

## Documentação obrigatória

É obrigatório criar ou atualizar documentação detalhada sempre que algo novo for criado ou qualquer parte do projeto for modificada. Isso inclui código, API, tipos, testes, configuração, dependências, scripts, workflows, exemplos e documentação. Faça a atualização na mesma tarefa.

- Explique propósito, fluxo, entradas, saídas, efeitos, limites e manutenção para humanos e IAs.
- Organize páginas em docs/ e inclua-as no índice docs/README.md.
- Atualize o README quando instalação, API, compatibilidade ou exemplos mudarem.
- Valide cada afirmação no código e nos testes antes de documentá-la.

## Mapa do projeto

- index.ts: classe Macroable e seus métodos estáticos.
- tests/macroable.spec.ts: testes de macros, propriedades de instância, getters e herança.
- eslint.config.js: configuração de lint baseada em @arapucajs/eslint-config.
- tsconfig.json: configuração baseada em @arapucajs/tsconfig.
- docs/references/testing-and-tooling.md: dependências, ferramentas, loader e npm test.

Leia o código e a documentação antes de alterar. Preserve o export default, a compatibilidade ESM, o contexto this dos valores funcionais e o isolamento das propriedades entre subclasses e instâncias. Faça commit, push ou PR apenas quando solicitado.
