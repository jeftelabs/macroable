# Planejamento

Os itens abaixo são oportunidades de avaliação; não são compromissos de entrega.

- Definir e documentar a faixa mínima de Node.js, adicionando metadado `engines` e matriz de CI quando a política for decidida.
- Criar teste para valor de objeto mutável passado a `instanceProperty`, deixando explícito se referências compartilhadas são contrato ou devem virar fábrica por instância.
- Avaliar tipos de module augmentation exemplificados por uma classe consumidora real e considerar publicar um guia TypeScript dedicado.
- Avaliar se a cópia/binding de propriedades próprias de função deve incluir chaves de símbolo e descriptors; qualquer mudança deve manter compatibilidade documentada.
- Considerar execução de lint, typecheck, testes e build em pull requests; o workflow de checks atual declara somente push para main.
- Corrigir inconsistências menores em metadados/scripts somente em mudança separada, atualizando documentação e testes conforme impacto.
