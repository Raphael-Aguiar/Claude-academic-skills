---
name: iniciar-projeto
description: "Skill geradora — cria um novo projeto de escrita em ~/PKM/Escrita/ (SKILL.md do projeto, scope.yaml, notas_pesquisa.md) a partir de um questionário conduzido com Raphael, uma pergunta por vez."
status: ativa
version: 1.0
date: 2026-07-10
---

# Iniciar Projeto de Escrita

## Propósito

Configurar um novo projeto de escrita sem montagem manual. Conduz um questionário com Raphael e gera a estrutura mínima do projeto, com o SKILL.md específico modelado no do caso Atheneu (`~/PKM/Escrita/Livro Editora Atheneu/SKILL.md` — ler como referência antes de gerar).

## Questionário — UMA pergunta por vez

Aplicar via `AskUserQuestion`, **estritamente uma pergunta por turno** (regra §2 do CLAUDE.md global — Raphael tem TDAH; nunca despejar várias perguntas de uma vez). Aguardar cada resposta antes da próxima. Se Raphael já forneceu a informação no contexto da conversa, pular a pergunta e registrar o valor (não re-perguntar o decidido).

| # | Pergunta | Opções típicas |
|---|---|---|
| 1 | Tipo de texto | capítulo de livro / artigo original / revisão de literatura (integrativa, sistemática, de escopo, meta-revisão) / relatório técnico |
| 2 | Editora ou periódico alvo | texto livre + link para normas de submissão, se houver |
| 3 | Estilo de citação | Vancouver / ABNT / APA |
| 4 | Escopo | o que incluir e o que **excluir** explicitamente |
| 5 | Público-alvo | gestores / clínicos / pesquisadores / estudantes / público geral |
| 6 | Extensão alvo | páginas ou palavras (e a definição de lauda, se a editora tiver) |
| 7 | Prazo | data de entrega |
| 8 | Coautores | nomes e filiações (ou nenhum) |
| 9 | Fontes prioritárias | bases, instituições, autores-chave, materiais já existentes |
| 10 | Acesso institucional | CAPES / ProBE / assinaturas diretas — condiciona a lista `to-obtain` do pipeline |

Se houver manual do autor ou normas em PDF, pedir o arquivo e extrair as normas de formatação para o SKILL.md do projeto (como feito com o `Manual do Autor Atheneu.pdf`).

## Mapeamento tipo de texto → modalidade do pipeline

| Tipo de texto (pergunta 1) | `--modality` do scope.yaml |
|---|---|
| Capítulo de livro | `pesquisa-base` |
| Artigo original | `pesquisa-base` |
| Revisão integrativa | `revisao-integrativa` |
| Revisão sistemática | `revisao-sistematica` |
| Revisão de escopo | `revisao-escopo` |
| Meta-revisão | `meta-revisao` |
| Relatório técnico | `pesquisa-base` |

Revisões usam a skill `revisao-literatura` (a revisão É o produto); os demais usam `pesquisa-academica` (base de refs é produto intermediário).

## Saída gerada

Criar a pasta e três arquivos:

```
~/PKM/Escrita/<Nome Natural do Projeto>/
├── SKILL.md            ← regras do projeto (modelo abaixo)
├── scope.yaml          ← via pipeline (comando abaixo)
└── notas_pesquisa.md   ← vazio, só com cabeçalho
```

1. **`SKILL.md`** — modelado no do Atheneu. Seções obrigatórias: frontmatter (`name` kebab-case, `description`); Contexto do projeto (tipo, alvo, autor, coautores, prazo); Escopo (Incluir / Excluir); Normas da editora/periódico (formato de entrega, estrutura, estilo de citação com exemplos, tabelas/figuras, abreviaturas); Diretrizes de estilo e tom (público-alvo; incluir referência à skill `redacao-academica`); Fontes prioritárias; Estado atual do projeto (inicial: "projeto criado em <data>, nenhuma seção escrita"); Arquivos do projeto; Skills genéricas relacionadas (tabela apontando para `~/bin/escrita-tooling/skills/` — nunca duplicar conteúdo delas).
2. **`scope.yaml`** — gerar pelo pipeline, com a modalidade mapeada acima:
   ```bash
   cd ~/bin/escrita-tooling && source .venv/bin/activate
   python -m tools scope "Nome Natural do Projeto" --init --modality <modalidade>
   ```
   Depois, preencher no scope.yaml as keywords, research_axes e exclusion_keywords derivados das respostas 4 e 9, e o acesso institucional (resposta 10).
3. **`notas_pesquisa.md`** — apenas cabeçalho: `# Notas de Pesquisa — <Nome do Projeto>`, uma linha explicando a função (dados brutos e fontes verificadas; nunca no texto canônico) e a tabela de controle de referências vazia (formato da skill `pesquisa-academica` §7.2).

O arquivo de texto canônico (ex.: `capitulo.md`) NÃO é criado aqui — nasce na primeira sessão de escrita, quando a estrutura de seções estiver aprovada.

## Regras invioláveis

- **Nome da pasta human-readable, com espaços** (ex.: `Artigo Telessaúde APS`) — nunca kebab-case nem snake_case (regra do vault para arquivos visíveis a Raphael).
- **Não criar nada fora de `~/PKM/Escrita/`** — sem repo Git próprio (o vault já versiona), sem pastas em outros pontos do vault.
- **Antes de criar, verificar se já existe** projeto com nome ou tema semelhante em `~/PKM/Escrita/` — se houver sobreposição, perguntar a Raphael em vez de criar duplicata.
- **Registrar a criação no `~/PKM/wiki/log.md`**: `[YYYY-MM-DD HH:MM] CRIAR | projeto de escrita <Nome> (tipo, alvo, prazo) | ~/PKM/Escrita/<Nome>/`.
- Nenhuma propriedade de frontmatter, enum ou tag nova — usar apenas o padrão descrito aqui (autoria de schema é de Raphael).

## Após a criação

Apresentar a Raphael, no chat: caminho da pasta, resumo de 1 linha por arquivo gerado e o próximo passo sugerido (editar keywords do scope.yaml ou rodar `python -m tools run "Projeto"` para a pesquisa inicial).
