---
name: iniciar-projeto
description: Skill geradora — cria a skill de projeto específica a partir de um questionário sobre editora, estilo de citação, escopo, público, extensão e deadline.
status: placeholder — a desenvolver no projeto Octopus
---

# Iniciar Projeto de Escrita

**Status:** 🔲 A desenvolver

## Propósito

Automatizar a configuração de um novo projeto de escrita. Em vez de montar manualmente o SKILL.md de cada projeto, esta skill conduz um questionário e gera automaticamente:

1. A skill de projeto (`SKILL.md`) com todas as regras específicas
2. A estrutura de pastas do projeto
3. Os arquivos iniciais (texto limpo, notas de pesquisa)
4. O registro do projeto no Index (tabela `projetos`)

## Questionário proposto (rascunho)

1. **Tipo:** capítulo de livro / artigo de periódico / texto para aula / relatório técnico / outro
2. **Editora/periódico:** nome, link para normas de submissão
3. **Estilo de citação:** Vancouver / ABNT / APA / outro
4. **Título (provisório):**
5. **Escopo:** o que incluir e o que excluir
6. **Público-alvo:** gestores / clínicos / pesquisadores / estudantes / público geral
7. **Extensão:** número de páginas ou palavras
8. **Deadline:**
9. **Coautores:** nomes e filiações
10. **Fontes prioritárias:** bases, instituições, autores-chave
11. **Formatação:** fonte, espaçamento, margens (ou link para template)

## Saída esperada

```
~/escrita/[Nome do Projeto]/
├── SKILL.md              ← gerada pelo questionário
├── [texto].md            ← arquivo canônico do texto (vazio, com cabeçalho)
├── notas_pesquisa.md     ← arquivo para dados brutos
└── PROMPT_RETOMADA.md    ← prompt padrão para retomar em nova conversa
```

## Questões em aberto

- Essa skill justifica o esforço de construção, ou é mais rápido montar manualmente (como fizemos com a Atheneu)?
- O questionário deve ser interativo (pergunta por pergunta) ou aceitar um briefing livre?
- Como lidar com projetos que não seguem um template claro (ex: textos opinativos, ensaios)?
- Integrar com a tabela `projetos` do Index automaticamente?

## Experiência acumulada (caso Atheneu)

O SKILL.md do capítulo 16 foi construído manualmente ao longo de uma conversa. O processo envolveu: ler o Manual do Autor, definir escopo com base no sumário do livro, decidir estilo de citação, listar fontes prioritárias, e codificar o workflow de edição. Esse processo concreto é a base para o questionário desta skill.
