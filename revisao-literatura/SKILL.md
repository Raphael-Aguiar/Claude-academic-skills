---
name: revisao-literatura
description: "Modalidades de revisão de literatura como produto final: integrativa, sistemática e de escopo. A revisão É o texto sendo produzido. Usa o pipeline automatizado em ~/bin/escrita-tooling/tools/."
status: ativa
version: 2.0
criada: 2026-02-18
atualizada: 2026-02-18
baseda-em: ~/PKM/Escrita/Livro Editora Atheneu/Deep Researches (Antigas)/relatorio-audit-deep-research.md
---

# Revisão de Literatura — Pipeline Validado

## Propósito

Conduzir revisão de literatura como **produto final** (o artigo de revisão É o texto sendo produzido). Três subtipos:

| Subtipo | Uso | Framework |
|---|---|---|
| `revisao-integrativa` | Sintetizar pesquisa sobre um tópico | Whittemore & Knafl (2005) |
| `revisao-sistematica` | Síntese exaustiva com protocolo rigoroso | Cochrane + PRISMA 2020 |
| `revisao-escopo` | Mapear extensão e natureza da evidência | Arksey & O'Malley (2005); JBI (Peters et al. 2020) |

**Princípio central:** ferramentas de IA são úteis para mapear o terreno, nunca para gerar a lista final de referências.

**Quando usar esta skill:** publicar artigo de revisão, TCC/dissertação/tese com revisão de literatura.

**Quando usar pesquisa-academica:** quando a revisão é intermediária (capítulo, artigo original, material didático).

**Fundamentação metodológica:** `~/PKM/Escrita/Pesquisas sobre Pipeline/revisao-metodologica-tipos-de-revisao.md`


## Pipeline automatizado

A partir da v2.0, esta skill é orquestrada pelo pipeline em `~/bin/escrita-tooling/tools/`.

```bash
# Revisão integrativa
python -m tools scope "Artigo Revisão" --init --modality revisao-integrativa
python -m tools run "Artigo Revisão"

# Revisão sistemática
python -m tools scope "Projeto" --init --modality revisao-sistematica
python -m tools run "Projeto" --modality revisao-sistematica

# Revisão de escopo
python -m tools scope "Projeto" --init --modality revisao-escopo
python -m tools run "Projeto" --modality revisao-escopo
```

**Documentação completa:** `~/bin/escrita-tooling/tools/README.md`

O pipeline automatiza busca, deduplicação, verificação, classificação, triagem e geração de .bib/relatório. As diferenças entre subtipos (profundidade, documentação, avaliação de qualidade) são configuradas automaticamente pela modalidade.


## 1. Pipeline de 6 etapas (instruções humanas)

### Etapa 1 — Mapeamento conceitual

**Objetivo:** entender os tópicos, subtemas e terminologia do campo — NÃO gerar referências.

**Ferramentas:** ChatGPT ou Gemini (qualquer modelo competente)

**O que pedir:**
- "Quais são os subtemas principais de [X]?"
- "Quais são os termos de busca em inglês e português para [X]?"
- "Quais são as instituições de referência (nacionais e internacionais) que produzem dados sobre [X]?"
- "Quais são os autores seminais sobre [X]?"

**Produto desta etapa:** lista de palavras-chave, subáreas e atores — não uma lista de referências.

**Regra de ouro:** nenhuma referência desta etapa vai diretamente para o texto.

---

### Etapa 2 — Busca primária em bases indexadas

**Objetivo:** encontrar artigos científicos verificáveis com DOI.

**Ferramentas e como usar:**

| Base | Melhor para | Como acessar |
|---|---|---|
| PubMed/PMC | Artigos internacionais de saúde | pubmed.ncbi.nlm.nih.gov |
| SciELO Brasil | Contexto nacional | scielo.br |
| Google Scholar | Gestão, administração, interdisciplinar | scholar.google.com |
| Portal CAPES | Acesso a paywalled | periodicos.capes.gov.br |
| Semantic Scholar | Exploração de citações | semanticscholar.org |

**Estratégia de busca:**
- Combinar termos em inglês (mais artigos) + português (contexto nacional)
- Filtros temporais: priorizar 2020–2025
- Filtros de tipo: "review", "systematic review", "meta-analysis" para embasamento conceitual; "original research" para casos e dados
- Verificar listas de referências dos melhores artigos (snowball)

**Produto desta etapa:** lista de DOIs e PMIDs com título confirmado.

---

### Etapa 3 — Fontes institucionais e de mercado

**Objetivo:** complementar com dados de mercado e fontes de referência do setor que não estão em periódicos.

**Fontes de alta autoridade para saúde no Brasil:**

| Categoria | Fonte | Tipo |
|---|---|---|
| Adoção de TI/IA | CETIC.br — TIC Saúde | Relatório de pesquisa anual |
| Regulação suplementar | ANS (ans.gov.br) | Órgão regulador |
| Dados públicos de saúde | DATASUS | Base de dados governamental |
| Interoperabilidade | RNDS/Ministério da Saúde | Documentação técnica oficial |
| Hospitais privados | ANAHP (anahp.com.br) | Relatório setorial |
| Medicina | CFM (cfm.org.br) | Conselho federal |
| Informática em saúde | SBIS | Sociedade científica |
| Global | OMS/PAHO, HIMSS | Organizações internacionais |
| Mercado/tendências | PwC, Deloitte, McKinsey (relatórios públicos) | Relatório de consultoria¹ |

¹ *Relatórios de consultoria: citar como "relatório de mercado" com data de acesso, nunca como evidência científica.*

**Para encontrar essas fontes:** Gemini ou Perplexity são úteis para localizar o URL exato de um relatório específico. Acesse sempre o site oficial diretamente após.

---

### Etapa 4 — Verificação de citações com scite.ai

**Objetivo:** para afirmações científicas importantes, verificar se a citação é apoiada ou contestada pela literatura.

**Quando usar:**
- Afirmação central do argumento (não tangencial)
- Afirmação quantitativa com número específico
- Afirmação potencialmente controversa

**Como usar:** inserir título do artigo ou DOI no scite.ai → verificar contexto de citação (supporting/contrasting/mentioning).

**Quando NÃO usar:** como buscador de tema (não é para isso — produz refs off-topic).

---

### Etapa 5 — Gestão e rastreabilidade

**Ferramentas:** Zotero (gratuito, indispensável)

**Para cada referência, registrar em `notas_pesquisa.md`:**
```
- Autores: [...]
- Título: [...]
- Periódico: [...]
- Ano: [...]
- DOI: [doi.org/...]
- URL: [...]
- Data de acesso: [YYYY-MM-DD]
- Status verificação: [acessível / verificado / bloqueado / 404]
- Dado utilizado: [...]
- Verificação do dado: [sim/não — o número/afirmação está no texto da fonte?]
```

**Hierarquia de estabilidade de link:**
1. DOI (raramente quebra)
2. URL canônica do artigo no periódico
3. URL de download/PDF (muda frequentemente)
4. URL de site governamental (alta taxa de link rot)

---

### Etapa 6 — Checklist de inserção

Antes de inserir cada referência no texto canônico, executar:

| # | Verificação | Como |
|---|---|---|
| 1 | URL/DOI acessível? | WebFetch ou navegador |
| 2 | Conteúdo corrobora a afirmação? | Ler a fonte e confirmar o dado |
| 3 | Formato correto? | Conferir com SKILL.md do projeto |
| 4 | Procedência documentada? | Registrado em notas_pesquisa.md |
| 5 | Autores verificados? | Confirmar no site do periódico |
| 6 | Metadados corretos? | Volume, issue, páginas, ano |

---

## 2. Critérios de qualidade de fontes

### Classificação por tier

| Tier | Tipo | Aceitável para livro-texto? |
|---|---|---|
| 1 | Periódico revisado por pares indexado (PubMed, SciELO, IEEE, Scopus, WoS) | Sim — preferencial |
| 2 | Fonte institucional de referência (Ministério da Saúde, DATASUS, IBGE, OMS) | Sim — com ressalva de tipo de fonte |
| 3 | Relatório técnico especializado (CETIC.br, HIMSS, consultorias de referência) | Sim — citar como relatório técnico |
| 4 | Notícia / blog / site corporativo | Não — apenas como ilustração, nunca como sustentação de dado |
| 5 | Inacessível / inexistente | Não |

### Domínios confiáveis por tier (lista consolidada desta auditoria)

**Tier 1 (periódicos acadêmicos):**
- pmc.ncbi.nlm.nih.gov, pubmed.ncbi.nlm.nih.gov
- scielo.br, scielo.org
- ieeexplore.ieee.org, bmj.com, biomedcentral.com, mdpi.com
- jmir.org, tandfonline.com, semanticscholar.org
- journals.library.columbia.edu, jmphc.com.br, abccardiol.org
- researchgate.net, arxiv.org (pré-print), fi-admin.bvsalud.org

**Tier 2 (fontes institucionais):**
- saude.gov.br, datasus.gov.br, ans.gov.br, anvisa.gov.br
- ibge.gov.br, fiocruz.br, ipea.gov.br
- who.int, paho.org, himss.org
- cetic.br, iess.org.br, cfm.org.br
- rnds-guia.saude.gov.br, ces.ibge.gov.br
- pwc.com.br (relatórios públicos de pesquisa)

**Tier 3 (técnicos/especializados):**
- medicinasa.com.br, anahp.com.br, sindihospa.com.br
- grandviewresearch.com, straitsresearch.com, statista.com

**Tier 4 (evitar como referência):**
- Blogs corporativos, sites de consultores individuais, YouTube, LinkedIn
- Sites de startups/healthtechs (carefy.com.br, sensorweb.com.br etc.)

---

## 3. Avaliação de relevância

### Score por escopo

Use este critério ao triagear referências geradas por IA:

| Score | Critério |
|---|---|
| 2 | Diretamente relevante ao escopo declarado do texto |
| 1 | Tangencial — saúde digital, regulação, mas não o foco específico |
| 0 | Off-topic — descartar |

### Keywords para gestão de saúde digital (EXEMPLO histórico — Capítulo 16 Atheneu)

> **Nota:** as listas abaixo são o exemplo concreto que calibrou a skill (Cap. 16). Para qualquer outro projeto, as keywords vivem no `scope.yaml` do projeto (`relevance_keywords` + `exclusion_keywords`) — não reutilizar estas.

**Score 2 (incluir):** big data, machine learning, gestão hospitalar, hospital management, saúde suplementar, operadora, leito, bed management, patient flow, fraude, fraud detection, auditoria médica, supply chain, cadeia de suprimentos, people analytics, no-show, value-based, custo, eficiência, DATASUS, SUS, ANS, RNDS, FHIR, TISS, prontuário eletrônico, interoperabilidade, LGPD, saúde digital, transformação digital, analytics, data lake, maturidade digital.

**Score 0 (descartar):** resíduo hospitalar, tuberculose, geriatria, cuidados paliativos, resistência antimicrobiana, AMR, nutrição, wearable (salvo gestão), diagnóstico por imagem (salvo menção gerencial).

---

## 4. Sobre as ferramentas de IA — resumo empírico

Baseado na auditoria de 288 referências (2026-02-18). Relatório completo: `~/PKM/Escrita/Livro Editora Atheneu/Deep Researches (Antigas)/relatorio-audit-deep-research.md`.

| Ferramenta | Melhor uso | Pior uso | Risco |
|---|---|---|---|
| **Perplexity** | Triagem de URLs em volume; 76% T1; 24 Gold refs | Gerar lista de refs para uso direto | Baixo risco de alucinação (URLs verificáveis), 59% off-topic |
| **scite.ai** | Verificar se citação é apoiada/contestada | Busca de tema / geração de refs | Refs off-topic graves (46%); sem URLs |
| **Google Gemini** | Mapear fontes institucionais brasileiras (T2=24%) | Referências acadêmicas diretas | Mistura blogs e periódicos; 36% T4 |
| **ChatGPT** | Identificar fontes-chave com contexto editorial | Qualquer uso sem verificação | Alto risco de alucinação em dados numéricos; zero URLs |
| **Claude** | Base bibliográfica estruturada (Vancouver completo; 8% off-topic) | Referências acadêmicas diretas | 51% T4 (blogs); verificar fonte primária para dados numéricos |

**Sequência recomendada:** ChatGPT/Gemini (mapeamento) → PubMed/SciELO (busca primária) → CETIC.br/ANS/DATASUS (fontes nacionais) → scite.ai (verificação pontual) → Zotero (gestão).

---

## 5. Erros comuns e como evitar

| Erro | Como evitar |
|---|---|
| Inserir referência de IA sem verificar URL | Executar checklist etapa 6 para toda ref |
| Dados numéricos sem fonte primária | Exigir URL ou DOI que contenha o número |
| Citar blog como referência | Substituir por artigo ou relatório técnico |
| Usar scite.ai como buscador | Usar apenas para verificação de afirmação específica |
| Não registrar data de acesso | Registrar em notas_pesquisa.md sempre |
| Confiar em título HTML como título do artigo | Verificar título no site do periódico |
| Link rot antes da submissão | Re-verificar URLs 2 semanas antes de submeter |

---

## Referências desta skill

- Relatório de auditoria: `~/PKM/Escrita/Livro Editora Atheneu/Deep Researches (Antigas)/relatorio-audit-deep-research.md`
- Skill complementar: `~/bin/escrita-tooling/skills/referencias-e-citacoes/SKILL.md`
- Verificação de referências: `python -m tools verify` / `verify-refs` (o antigo `check_refs.py` foi absorvido pelo pipeline — ver `tools/DECISOES.md` T1/T8)
