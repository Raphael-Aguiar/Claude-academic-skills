---
name: formatacao-final
description: Conversão de markdown para formato de entrega (docx, PDF, Google Docs, LaTeX). Usar na etapa final de qualquer projeto de escrita.
status: placeholder — a desenvolver no projeto Octopus
---

# Formatação Final

**Status:** 🔲 A desenvolver

## Propósito

Automatizar a conversão do texto em markdown para o formato exigido pela editora/periódico, aplicando template, estilos de fonte, espaçamento, numeração de páginas, cabeçalhos etc. — eliminando o trabalho manual de formatação.

## O que esta skill deverá cobrir (rascunho)

- Conversão md → docx via Pandoc com templates customizados
- Conversão md → Google Docs via API (markdown_replace ou markdown_upload)
- Conversão md → PDF (via Pandoc + LaTeX ou WeasyPrint)
- Conversão md → LaTeX (para periódicos que exigem)
- Aplicação de templates por editora/periódico (fonte, espaçamento, margens, cabeçalhos)
- Numeração de figuras, tabelas e referências cruzadas
- Geração automática de sumário
- Inserção de metadados (título, autor, filiação, resumo, palavras-chave)

## Questões em aberto

- Pandoc como ferramenta central, ou alternativas?
- Como gerenciar uma biblioteca de templates por editora/periódico?
- Limitações do Google Docs API para formatação fina — quando vale e quando não vale?
- Figuras e tabelas: como referenciar no markdown de forma que a conversão funcione bem?

## Experiência acumulada (caso Atheneu)

Requisitos: Arial 12, espaçamento 1,5, A4. O markdown_replace do Google Docs funciona para escrita intermediária, mas a formatação final precisa de ajuste manual ou de um pipeline Pandoc com template docx customizado. Testar ambos caminhos.
