---
name: formatacao-final
description: "Conversão do markdown canônico para o formato de entrega (docx, PDF) via Pandoc + CSL. Usar na etapa final de qualquer projeto de escrita, após aprovação do texto pelo autor."
status: ativa
version: 1.0
date: 2026-07-10
---

# Formatação Final

## Propósito

Converter o texto canônico em markdown para o formato exigido pela editora/periódico, aplicando template, fontes, espaçamento e estilo de citação. Ler antes o SKILL.md do projeto — normas de formato e estilo de citação vêm de lá.

## Caminho canônico: Pandoc + CSL

Pandoc é a ferramenta central (`pandoc --version` antes de começar).

### Markdown → DOCX (caso mais comum: editoras)

```bash
cd ~/PKM/Escrita/<Projeto>/
pandoc capitulo.md -o entrega/capitulo.docx \
  --reference-doc=template-editora.docx \
  --citeproc --bibliography=pipeline/refs.bib --csl=vancouver.csl \
  --metadata title="Título do capítulo" --metadata author="Raphael Augusto Teixeira de Aguiar"
```

- `--reference-doc` aplica os estilos do docx de referência (fonte, espaçamento, margens, headings). **Sem ele, o Pandoc usa o default Calibri, que nenhuma editora aceita.** Criar o template uma vez por editora (a partir de `pandoc -o ref.docx --print-default-data-file reference.docx`, ajustando os estilos no LibreOffice/Word) e reutilizar entre projetos da mesma editora.

### Markdown → PDF

```bash
pandoc capitulo.md -o entrega/capitulo.pdf \
  --citeproc --bibliography=pipeline/refs.bib --csl=vancouver.csl \
  --pdf-engine=xelatex -V mainfont="Arial" -V fontsize=12pt \
  -V geometry:a4paper -V linestretch=1.5
```

Requer `xelatex`. PDF é para conferência visual e circulação entre coautores; a entrega à editora é quase sempre docx.

### Citações: `.bib` do pipeline + CSL

- O `.bib` vem do pipeline (`pipeline/refs.bib`, gerado por `python -m tools`). **Nunca montar `.bib` à mão de memória** — referências vêm da base verificada.
- No markdown, citar com sintaxe Pandoc (`[@chave]` ou `[@chave1; @chave2]`); `--citeproc` gera chamadas e lista final no estilo do `.csl`.
- Texto com numeração Vancouver manual já consolidada (caso Atheneu): **não converter retroativamente para `[@chave]` sem pedido** — formatar o docx preservando a numeração existente.
- CSL oficiais em **zotero.org/styles** (`vancouver.csl`, `associacao-brasileira-de-normas-tecnicas.csl`); guardar na pasta do projeto ou em `~/bin/escrita-tooling/csl/`. Registrar no SKILL.md do projeto qual CSL foi usado.

## Lições do caso Atheneu

- Requisitos da editora: **Arial 12, espaçamento 1,5, papel A4** — codificados no `--reference-doc` (docx) ou nas variáveis `-V` (PDF).
- O `markdown_replace` do Google Docs (MCP) serve só para **rascunho intermediário** (circular com coautores, revisão comentada). A **formatação final exige Pandoc com template docx**: a API do Docs não controla com precisão estilos de parágrafo, numeração de referências sobrescritas nem hierarquia de headings.
- Tabelas e quadros: mesmo arquivo do capítulo, na ordem de citação, título acima e fonte abaixo (norma específica no SKILL.md do projeto).

## Checklist pré-entrega

Sobre o arquivo **gerado** (abrir o docx/PDF, não só o markdown), item a item, reportando **dado literal** (contagem, nº da última referência) — nunca "tudo OK":

1. **Referências**: numeração contínua (sem saltos nem duplicatas); toda chamada no texto tem entrada na lista e vice-versa.
2. **Figuras e tabelas**: numeradas conforme a norma (ex.: Tabela 16.1), citadas no texto, com título e fonte.
3. **Metadados do docx**: título, autor, filiação (gravados via `--metadata`; conferir em propriedades do documento).
4. **Extensão** vs limite da editora: `pandoc capitulo.md -t plain | wc -w` (palavras); páginas no docx gerado (dependem do template).
5. **DOIs/URLs íntegros**: nenhum truncado/quebrado por hifenização — conferir visualmente + `grep -oE 'https?://[^ ]+' capitulo.md` contra o renderizado.
6. **Formatação visual**: fonte, corpo, entrelinha, margens (amostrar 3 páginas: início, meio, referências).
7. **Sequência estrutural** da editora (Atheneu: nº do capítulo → título → autor → texto).

## Saída

- Arquivos gerados vão na subpasta `entrega/` dentro de `~/PKM/Escrita/<Projeto>/` — nunca fora dela.
- O markdown permanece o canônico: correções pós-conversão são feitas no `.md` e o documento é regenerado, nunca editado só no docx.
