---
name: referencias-e-citacoes
description: Gestão, formatação e verificação de referências bibliográficas em múltiplos estilos. Usar ao inserir, organizar, formatar ou auditar referências em qualquer projeto de escrita.
status: ativa (seção de verificação operacional; demais seções em desenvolvimento)
---

# Referências e Citações

## Propósito

Automatizar ao máximo o trabalho de gestão de referências — a tarefa que o autor mais detesta fazer manualmente. Cobrir desde a inserção inline até a geração da lista final formatada, incluindo verificação de integridade.


## 1. Estilos de citação suportados

- **Vancouver (ICMJE):** Padrão para publicações médicas. Numeração sequencial [1], [2], etc.
- **ABNT:** Padrão brasileiro. Autor-data (Sobrenome, Ano).
- **APA:** Padrão em ciências sociais. Autor-data (Sobrenome, ano).
- Outros conforme demanda do projeto.

Cada projeto define seu estilo no respectivo `SKILL.md`. Sempre verificar antes de inserir referências.


## 2. Inserção e formatação

### Inserção inline
- Vancouver: numeração sequencial conforme ordem de aparição no texto
- ABNT/APA: formato autor-data

### Lista de referências
- Formatar conforme estilo definido no projeto
- Periódicos, livros, Internet e legislação têm formatos distintos (consultar Manual do projeto)

### Verificação de consistência
- Toda referência citada no texto deve constar da lista final
- Toda referência da lista deve estar citada no texto
- Numeração sequencial sem lacunas (Vancouver)


## 3. Verificação e monitoramento de referências

**Esta seção é operacional** — usar como checklist ao inserir referências e ao auditar referências existentes.

### 3.1 Checklist por referência (executar ao inserir cada nova referência)

Antes de inserir qualquer referência no texto canônico, verificar:

| # | Verificação | Como fazer | Obrigatório? |
|---|---|---|---|
| 1 | **URL/DOI acessível?** | WebFetch na URL ou `https://doi.org/[DOI]` | Sim (para fontes Internet) |
| 2 | **Conteúdo corrobora a afirmação?** | Ler a fonte e confirmar que os dados citados estão presentes | Sim |
| 3 | **Formato correto?** | Conferir com o estilo definido no SKILL.md do projeto | Sim |
| 4 | **Procedência documentada?** | Referência registrada em `notas_pesquisa.md` com URL, DOI e data de verificação | Sim |
| 5 | **Autores verificados?** | Confirmar que os autores listados são os autores reais do artigo/documento | Sim |
| 6 | **Metadados conferidos?** | Volume, issue, páginas, ano — conferir com a fonte primária | Sim |

### 3.2 Cuidados especiais com referências de IA

Referências sugeridas por modelos de linguagem (Perplexity, scite.ai, ChatGPT, Claude) **DEVEM ser verificadas individualmente** antes de inserção no texto. Riscos conhecidos e documentados:

- **Autores fabricados** — nomes plausíveis mas inexistentes no periódico citado (caso Aldoseri et al. [7], Cap. 16)
- **Metadados misturados** — título de um artigo combinado com volume/páginas de outro
- **DOIs inventados** — DOIs com formato correto mas que retornam 404
- **Periódicos errados** — artigo atribuído ao periódico errado
- **Dados quantitativos sem fonte** — números específicos sem URL (ChatGPT: "HC-SP reduziu 40% tempo de auditoria" — inverificável)
- **Refs off-topic** — scite.ai gerou 45,8% de refs off-topic sobre resíduos hospitalares e geriatria para um capítulo sobre Big Data/IA em gestão

**Protocolo obrigatório para referências de IA:**
1. Verificar existência do artigo via DOI ou busca no site do periódico
2. Conferir autores, título, volume, páginas e ano na fonte primária
3. Para dados quantitativos específicos: localizar o número na fonte — não confiar que está lá
4. Se qualquer dado não conferir, descartar e buscar a referência correta manualmente
5. Documentar no `notas_pesquisa.md` que a referência foi verificada

**Avaliação empírica das ferramentas** (auditoria de 288 refs / 5 ferramentas, 2026-02-18):

| Ferramenta | % off-topic | URLs fornecidas | Risco de alucinação |
|---|---|---|---|
| Perplexity | 59% | 100% (verificável) | Baixo |
| scite.ai | 46% | 0% (inverificável) | Médio |
| Google Gemini | 21% | 100% (verificável) | Médio |
| ChatGPT | 0% | 0% (inverificável) | Alto |
| Claude | 8% | 97% (verificável) | Médio-baixo |

→ Relatório completo: `~/escrita/Livro Editora Atheneu/relatorio-audit-deep-research.md`
→ Pipeline recomendado: `~/escrita/skills/revisao-literatura/SKILL.md`

### 3.3 Protocolo de prevenção (integrado ao workflow de escrita)

**Ao pesquisar (etapa 1 do workflow):**
- Registrar em `notas_pesquisa.md`: URL de acesso, DOI (quando aplicável), data de verificação
- Para periódicos: anotar DOI sempre que disponível (DOIs raramente quebram)
- Para sites institucionais: anotar URL completa + capturar texto relevante (sites governamentais mudam URLs frequentemente)

**Ao inserir referência no texto (etapa 3 do workflow):**
- Executar checklist 3.1 completo
- Incluir URL ou DOI mesmo para artigos de periódico (facilita verificação futura)
- Para legislação: separar referência legal (decreto/portaria) de dados quantitativos (relatórios operacionais)

**Ao finalizar seção (entre etapas 3 e 4 do workflow):**
- Verificar consistência: todas as referências citadas estão na lista?
- Verificar numeração sequencial (Vancouver)
- Rodar checklist 3.1 para referências que foram inseridas sem verificação completa

### 3.4 Monitoramento periódico

**Antes de submeter o capítulo/artigo final:**
- Re-verificar todos os links (URLs podem quebrar em semanas)
- Priorizar DOIs como âncora estável (DOIs > URLs de sites)
- Para URLs governamentais (DATASUS, ANS, Planalto): verificar com atenção redobrada

**Classificação de risco de link rot:**
| Risco | Tipo de fonte | Ação |
|---|---|---|
| Baixo | Periódicos via DOI | Manter DOI; URL é backup |
| Médio | Sites de consultorias e organizações (HIMSS, Straits Research) | Verificar antes de submeter |
| Alto | Sites governamentais brasileiros (.gov.br) | Verificar antes de submeter; manter cópia do conteúdo relevante no notas_pesquisa.md |
| Muito alto | URLs diretas de PDF (download links) em periódicos | Evitar; usar URL canônica do artigo |
| Muito alto | URLs dinâmicas, páginas de busca | Evitar; buscar URL permanente |

**Evidência empírica (auditoria Cap. 16, 2026-02-18):** dos 217 links verificados, 71,4% acessíveis, 8,8% bloqueados por bot (não implica inexistência), 0,9% 404, 3,7% timeout. URLs de download de PDF foram as únicas 404 reais (2 casos).


## 4. Verificação de claims (afirmações vs. referências)

**Status:** Operacional (Camada 1 automatizada + Camada 2 interativa)

### O que é

Sistema que verifica se as afirmações no texto são realmente suportadas pelas referências citadas. Motivado pela descoberta de que 9/30 referências do Cap. 16 tinham autores fabricados — se metadados estavam errados, dados citados podem estar igualmente fabricados.

### Tipos de verificação

| Tipo | Descrição | Método |
|------|-----------|--------|
| **A — Quantitativo** | Números, percentagens, valores monetários | Busca numérica com variantes PT↔EN |
| **B — Qualitativo** | Métodos, modelos, instituições | Entity matching + contexto |
| **C — Conceitual** | Frameworks, conceitos gerais | Sobreposição temática |

### Uso

```bash
# Camada 1 — verificação automatizada
source .venv/bin/activate
python -m tools verify-claims "Projeto/capitulo.md" --project "Projeto"

# Camada 2 — revisão interativa via skill
/verify-claims "Projeto/capitulo.md" --project "Projeto"
```

### Outputs

- `<arquivo>.claims-report.md` — relatório legível com claims agrupados por risco
- `<arquivo>.claims-data.json` — dados estruturados para atualização pela Camada 2

### Detalhes técnicos

- Hierarquia de resolução: pipeline/refs → PMC full text → PubMed abstract → CrossRef abstract
- Cache em `.refs-cache.json` (reutilizado entre execuções)
- Variantes numéricas PT↔EN (vírgula decimal, mil/billion, moeda)
- Detecção de erros matemáticos (valores absolutos + percentagem derivada)
- Skill interativa: `~/escrita/skills/pesquisa-academica/verify-claims.md`


## 4.5 Facts Registry (camada preventiva)

**Status:** Operacional

### Relação entre as camadas

| Camada | Ferramenta | Quando | O que faz |
|--------|-----------|--------|-----------|
| **Preventiva** | extract-facts + Facts Registry | ANTES de escrever | Extrai fatos verificados das refs → `.facts-registry.json` |
| **Corretiva** | verify-claims | DEPOIS de escrever | Verifica se afirmações do texto são suportadas pelas refs → `.claims-data.json` |

### facts-crosscheck: ponte entre as camadas

O `facts-crosscheck` conecta as duas camadas:
```bash
python -m tools facts-crosscheck "Projeto"
```

Cruza os dados quantitativos do `.claims-data.json` (output do verify-claims) com o `.facts-registry.json`. Identifica dados usados no texto que NÃO foram registrados previamente no Facts Registry.

### Quando usar cada ferramenta

| Situação | Ferramenta |
|----------|-----------|
| Vou começar a escrever uma seção | `extract-facts` → registrar dados antes |
| Terminei de escrever, quero verificar | `verify-claims` → verificar depois |
| Quero ver se todos os dados usados estão registrados | `facts-crosscheck` → cruzar |
| Vou citar um número no texto | Consultar `facts-status --section X` |

### Referência

Skill completa: `~/escrita/skills/pesquisa-academica/extract-facts.md`


## 5. Formato de armazenamento — .bib + Pandoc

### Decisão (2026-02-18)

O formato de armazenamento é **.bib (BibTeX)**, gerado automaticamente pelo pipeline como subproduto da verificação via CrossRef.

**Abordagem em camadas (não excludentes):**

| Camada | O que | Status |
|---|---|---|
| **Camada 1 — Output do pipeline** | `.bib` por projeto, gerado automaticamente pela etapa 9 do pipeline. Pandoc + CSL para ABNT/Vancouver. | **Implementado (MVP)** |
| **Camada 2 — Rastreamento no Index** | Extensão da tabela `contextos` do `acervo.db` para registrar DOI/PMID. | Planejado |
| **Camada 3 — Zotero + Better BibTeX** | Importar .bib dos projetos para Zotero se/quando necessário. | Futuro/opcional |

### Geração de .bib

O pipeline gera `refs.bib` automaticamente na etapa 9 (`s09_synthesize.py`). Cada referência verificada contém metadados completos (título, autores, ano, DOI, journal, volume, issue, páginas) obtidos do CrossRef.

```bash
# Gerar .bib do projeto
python -m tools export "Projeto" --format bib
# Output: ~/escrita/Projeto/pipeline/refs.bib
```

### Uso com Pandoc + CSL

Para converter Markdown com citações para Word/PDF com referências formatadas:

```bash
# Vancouver (padrão médico)
pandoc capitulo16.md --citeproc --bibliography pipeline/refs.bib \
  --csl vancouver.csl -o capitulo16.docx

# ABNT
pandoc artigo.md --citeproc --bibliography pipeline/refs.bib \
  --csl abnt.csl -o artigo.docx
```

**CSL files:** baixar de https://www.zotero.org/styles

### Verificação automatizada

O pipeline inclui verificação na etapa 10 (`s10_validate.py`):
- Re-verificar DOIs e URLs das referências finais
- Checar consistência das grades
- Relatório de problemas encontrados

```bash
python -m tools run "Projeto" --from-stage 10
```


## 6. Experiência acumulada

### Caso Atheneu (Capítulo 16, 2025-2026)

- Estilo: Vancouver adaptado ao Manual do Autor Atheneu
- Referências incluem: artigos de periódico, relatórios de consultorias, legislação brasileira, sites institucionais
- Formato registrado no `SKILL.md` do projeto
- **Auditoria realizada em 2026-02-18**: 10 referências verificadas. Achados principais:
  - 1 referência inválida (possível alucinação de IA) — removida/substituída
  - 1 URL quebrada (site governamental) — corrigida
  - 1 dado sem fonte adequada ("80% não estruturados") — fonte complementar adicionada
  - Relatório completo: `~/escrita/Livro Editora Atheneu/relatorio-auditoria-referencias.md`
- **Lição aprendida:** Referências de modelos de linguagem devem ser verificadas individualmente. O Deep Research do scite.ai produziu referências tangenciais de baixa utilidade.
