---
name: redacao-academica
description: "Princípios de redação acadêmica didática. Ler antes de redigir qualquer seção de texto, em qualquer projeto de ~/PKM/Escrita/. Complementa (nunca substitui) o SKILL.md do projeto."
status: ativa
version: 1.0
date: 2026-07-10
---

# Redação Acadêmica

## Propósito

Princípios de escrita acadêmica didática independentes de editora, periódico ou formato. Aplicar em conjunto com o SKILL.md do projeto — **em conflito, o SKILL.md do projeto vence** (normas da editora são autoritativas).

## Regras de casa (não são o default do modelo)

- **Pobreza intencional de adjetivos** com abundância de dados, números e referências (regra do vault + Manual Atheneu).
- Escrever para o **público-alvo do projeto** (gestor, clínico, estudante — ver SKILL.md do projeto), que não é especialista no tema.
- Exemplos: preferir **casos reais e brasileiros** (instituições, sistemas, políticas nomeadas) a hipotéticos genéricos.

## Anti-padrões de texto gerado por IA — evitar ativamente

Correções contra os "tells" que o modelo produz por default:

| Anti-padrão | Correção |
|---|---|
| Listas com marcadores onde caberia prosa | Bullet points só para material genuinamente enumerável; argumento vai em parágrafo. |
| Muletas: "além disso", "vale ressaltar", "é importante notar", "nesse sentido", "cabe destacar" | Cortar ou substituir por conexão lógica real entre as ideias. |
| Paralelismo monótono (três frases seguidas com a mesma estrutura sintática) | Variar comprimento e construção das frases. |
| Resumo redundante ao fim de cada seção ("Em suma, como visto...") | Seção termina no último ponto substantivo. Síntese só onde o gênero exige (conclusão do texto). |
| Negrito em excesso no corpo do texto | Negrito quase nunca em prosa acadêmica; ênfase vem da construção da frase. |
| Travessões (em-dash) em excesso como conector universal | Preferir vírgula, dois-pontos ou frase nova. |
| Tríades adjetivas ("eficiente, escalável e sustentável") | Escolher o termo que importa; cortar os outros. |
| Parágrafo-sanduíche (anuncia, diz, repete o que disse) | Dizer uma vez, bem. |

## Integração com o Facts Registry

- **Todo número citado no texto deve existir no `.facts-registry.json` do projeto** (percentuais, valores monetários, contagens, anos de dados, tamanhos de mercado).
- Antes de escrever uma seção: `python -m tools facts-status "Projeto" --section X` para obter o brief de fatos disponíveis.
- Dado necessário mas ausente do registry → **não escrever de memória**: extrair primeiro com `extract-facts` (skill `~/bin/escrita-tooling/skills/pesquisa-academica/extract-facts.md`) ou omitir a afirmação quantitativa.
- Nunca derivar números novos (somas, médias, "quase o dobro") sem que a derivação seja trivial e os operandos estejam no registry.

## Citação

- Respeitar o estilo definido no SKILL.md do projeto (Vancouver, ABNT, APA) — formato de chamada no texto E da lista de referências.
- **Nunca inserir referência não-verificada.** Toda referência passa antes pela skill `referencias-e-citacoes` (checklist 3.1) ou já consta verificada em `notas_pesquisa.md` / `pipeline/`.
- Referências são acumulativas no projeto: a numeração continua de onde parou; não renumerar sem operação explícita.
- Afirmação que precisa de fonte e não tem → marcar `[REF PENDENTE: descrição]` e listar na entrega, nunca inventar.

## Checklist de saída (antes de entregar ao autor) — responder item a item

1. Todos os números citados existem no `.facts-registry.json`? (conferir um a um)
2. Todas as citações estão no estilo do projeto e apontam para referências verificadas?
3. Grep pelos anti-padrões da tabela acima — em especial muletas, resumos redundantes, negrito e em-dash?
4. A extensão bate com a estimativa do SKILL.md do projeto para a seção?
5. Há `[REF PENDENTE]` ou lacunas? Se sim, listar explicitamente na mensagem de entrega.

## Skills relacionadas

| Skill | Quando |
|---|---|
| `pesquisa-academica` | Antes de redigir — montar a base de referências. |
| `referencias-e-citacoes` | Ao inserir/formatar/verificar qualquer referência. |
| `formatacao-final` | Depois da aprovação — conversão para o formato de entrega. |
