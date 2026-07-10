---
name: formatacao-final
description: "Conversão do markdown canônico para o formato de entrega (docx, PDF) via Pandoc + CSL. Usar na etapa final de qualquer projeto de escrita, após aprovação do texto pelo autor."
status: ativa
version: 1.0
date: 2026-07-10
---

# Formatação Final

## Propósito

Converter o texto canônico em markdown para o formato exigido pela editora ou periódico, aplicando template, fontes, espaçamento e estilo de citação — eliminando formatação manual. Ler antes o SKILL.md do projeto: normas de formato e estilo de citação vêm de lá.

## Caminho canônico: Pandoc + CSL

Pandoc é a ferramenta central. Verificar instalação com `pandoc --version` antes de começar.

### Markdown → DOCX (caso mais comum: editoras)

```bash
cd ~/PKM/Escrita/<Projeto>/
pandoc capitulo.md -o entrega/capitulo.docx \
  --reference-doc=template-editora.docx \
  --citeproc --bibliography=pipeline/refs.bib --csl=vancouver.csl \
  --metadata title="Título do capítulo" --metadata author="Raphael Augusto Teixeira de Aguiar"
```

- `--reference-doc` aplica os estilos do docx de referência (fonte, espaçamento, margens, cabeçalhos). **Sem ele, o Pandoc usa o default (Calibri), que nenhuma editora aceita.**
- Criar o template uma vez por editora: `pandoc -o template-editora.docx --print-default-data-file reference.docx`, abrir no LibreOffice/Word e ajustar os estilos (Normal, Heading 1-4) conforme as normas. Guardar na pasta do projeto ou reutilizar entre projetos da mesma editora.

### Markdown → PDF

```bash
pandoc capitulo.md -o entrega/capitulo.pdf \
  --citeproc --bibliography=pipeline/refs.bib --csl=vancouver.csl \
  --pdf-engine=xelatex -V mainfont="Arial" -V fontsize=12pt \
  -V geometry:a4paper -V linestretch=1.5
```

Requer LaTeX instalado (`xelatex`). PDF é para conferência visual e circulação entre coautores; a entrega à editora é quase sempre docx.

### Citações: pipeline `.bib` + CSL

- O `.bib` vem do pipeline do projeto (`pipeline/refs.bib`, gerado por `python -m tools`). Nunca montar `.bib` à mão a partir de memória — referências vêm da base verificada.
- No markdown, citar com sintaxe Pandoc (`[@chave]` ou `[@chave1; @chave2]`); o `--citeproc` gera chamadas e lista final no estilo do `.csl`.
- Texto com numeração Vancouver manual já consolidada (caso Atheneu): não converter retroativamente para `[@chave]` sem pedido — formatar o docx preservando a numeração existente.

### Onde obter arquivos CSL

- Repositório oficial: **zotero.org/styles** (buscar "Vancouver", "ABNT" — ex.: `vancouver.csl`, `associacao-brasileira-de-normas-tecnicas.csl`).
- Baixar para a pasta do projeto ou para uma biblioteca compartilhada em `~/bin/escrita-tooling/csl/` (criar sob demanda). Registrar no SKILL.md do projeto qual CSL foi usado.

## Lições do caso Atheneu

- Requisitos da editora: **Arial 12, espaçamento 1,5, papel A4** — codificados no `--reference-doc` (docx) ou nas variáveis `-V` (PDF).
- O `markdown_replace` do Google Docs (MCP) serve para **rascunho intermediário** — circular texto com coautores, revisão comentada. A **formatação final exige Pandoc com template docx**: a API do Docs não controla com precisão estilos de parágrafo, numeração de referências sobrescritas e hierarquia de headings.
- Tabelas e quadros: manter no mesmo arquivo do capítulo, na ordem de citação, com título acima e fonte abaixo (conferir norma específica no SKILL.md do projeto).

## Checklist pré-entrega

Executar item a item sobre o arquivo final gerado (abrir o docx/PDF, não só o markdown):

1. **Referências sem lacunas**: numeração contínua (1, 2, 3... sem saltos nem duplicatas); toda chamada no texto tem entrada na lista e vice-versa.
2. **Figuras e tabelas**: todas numeradas conforme a norma da editora (ex.: Tabela 16.1, Fig. 16.2), todas citadas no texto, com título e fonte.
3. **Metadados do docx**: título, autor e filiação corretos (verificar em propriedades do documento; o Pandoc grava via `--metadata`).
4. **Extensão**: contagem de palavras/páginas do arquivo final contra o limite da editora (`pandoc capitulo.md -t plain | wc -w` para palavras; páginas conferidas no docx gerado, pois dependem do template).
5. **DOIs e URLs íntegros**: nenhum DOI ou URL truncado ou quebrado por hifenização na lista de referências — conferir visualmente no documento final e com `grep -oE 'https?://[^ ]+' capitulo.md` contra o que aparece renderizado.
6. **Formatação visual**: fonte, corpo, entrelinha e margens conferem com a norma (amostragem em 3 páginas: início, meio, referências).
7. **Sequência estrutural** exigida pela editora presente (ex.: Atheneu — número do capítulo → título → autor → texto).

Reportar o resultado do checklist ao autor junto com o arquivo, citando dado literal (contagem, número da última referência), não "tudo OK".

## Saída

- Arquivos gerados vão em subpasta `entrega/` dentro da pasta do projeto em `~/PKM/Escrita/<Projeto>/` — nunca fora dela.
- O markdown permanece o canônico: correções pós-conversão são feitas no `.md` e o documento é regenerado, nunca editado só no docx.
