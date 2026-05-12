# CLAUDE.md — Regras do Projeto

## REGRA PRINCIPAL: Proteção dos Dados no Firebase

**Os dados inseridos pelos usuários no Firebase são a parte mais importante deste projeto. Nenhuma alteração de código pode colocar esses dados em risco.**

### O que NUNCA fazer

- Nunca executar `deleteCollection`, `deleteDoc`, ou qualquer operação de deleção em massa no Firestore sem confirmação explícita do usuário.
- Nunca alterar regras de segurança do Firebase (`firestore.rules`, `storage.rules`) de forma que bloqueie acesso aos dados existentes.
- Nunca reescrever ou migrar estruturas de coleções sem antes garantir backup e validação dos dados.
- Nunca executar scripts de seed, reset ou wipe no banco de produção.
- Nunca usar `firebase use --add` ou trocar o projeto ativo sem confirmar com o usuário qual ambiente está sendo usado (desenvolvimento vs. produção).
- Nunca remover campos de documentos existentes sem avaliar o impacto em todos os lugares onde são lidos.
- Nunca alterar índices do Firestore de forma que torne dados existentes inacessíveis.

### O que SEMPRE fazer antes de qualquer alteração que envolva o banco

1. **Confirmar o ambiente**: verificar se o projeto Firebase ativo é desenvolvimento ou produção.
2. **Escopo mínimo**: toda operação de escrita deve afetar apenas os documentos estritamente necessários.
3. **Operações não-destrutivas primeiro**: prefira adicionar campos novos a renomear ou remover existentes.
4. **Avisar o usuário**: antes de qualquer operação que possa modificar ou apagar dados, descrever exatamente o que será feito e aguardar aprovação.
5. **Reversibilidade**: se uma migração for necessária, ela deve ser reversível ou ter backup explícito antes de executar.

### Hierarquia de prioridades

1. Integridade dos dados dos usuários no Firebase
2. Segurança e autenticação
3. Funcionalidade nova
4. Qualidade e refatoração de código

## ultrareview — Obrigatório após toda alteração

**Sempre rodar `/ultrareview` automaticamente ao final de qualquer sessão que envolva alterações de código**, independentemente do arquivo ou escopo.

### Por quê?
- O ultrareview executa múltiplos agentes especializados (estrutura, segurança, dados, qualidade)
- Detecta bugs críticos (como `removeID` destruir DOM permanentemente), inconsistências e vulnerabilidades XSS
- Garante que alterações em um lugar não quebram funcionalidades em outro

### Fluxo padrão:
1. Fazer as alterações necessárias
2. Rodar `graphify update .` para manter o grafo atualizado
3. Rodar `/ultrareview` obrigatoriamente
4. Corrigir quaisquer problemas apontados
5. Só então sugerir ao usuário que faça o commit

## graphify

This project has a graphify knowledge graph at graphify-out/.

Rules:
- Before answering architecture or codebase questions, read graphify-out/GRAPH_REPORT.md for god nodes and community structure
- If graphify-out/wiki/index.md exists, navigate it instead of reading raw files
- For cross-module "how does X relate to Y" questions, prefer `graphify query "<question>"`, `graphify path "<A>" "<B>"`, or `graphify explain "<concept>"` over grep — these traverse the graph's EXTRACTED + INFERRED edges instead of scanning files
- After modifying code files in this session, run `graphify update .` to keep the graph current (AST-only, no API cost)
