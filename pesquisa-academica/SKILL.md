---
name: pesquisa-academica
description: "Modalidade pesquisa-base: montar base de referências verificadas para subsidiar escrita (capítulo, artigo, texto didático). Produto intermediário, não final. Usa o pipeline automatizado em ~/bin/escrita-tooling/tools/."
status: ativa
version: 2.1
date: 2026-02-19
modality: pesquisa-base
evidence: Validado pela auditoria de 288 referências do Cap. 16 Atheneu. Pipeline v2 corrige triagem, queries e outputs.
---

# Pesquisa Acadêmica — Modalidade "pesquisa-base"

## Propósito

Montar uma **base de referências verificadas** como ponto de partida para o desenvolvimento de um texto acadêmico. O produto desta skill é intermediário — o texto final será outra coisa (capítulo, artigo, relatório).

Codificar um workflow padronizado de pesquisa de literatura que seja:
- **Sistemático** — bases definidas, palavras-chave documentadas, critérios explícitos
- **Auditável** — qualquer pessoa consegue reproduzir a busca
- **Eficiente** — automatizado via pipeline Python (`python -m tools`)
- **Resistente a alucinações** — toda referência verificada antes de uso (princípio "zero trust")

**Premissa fundamental (validada pela auditoria de 288 refs, Cap. 16):** ferramentas de IA generativa são úteis para *mapeamento conceitual* e *descoberta de fontes*, mas **nunca** devem ser a fonte final de referências.

**Quando usar esta skill:** capítulo de livro-texto, artigo original (introdução/discussão), material didático, relatório técnico.

**Quando usar revisao-literatura:** quando a revisão É o produto final (integrativa, sistemática, de escopo).


## Pipeline automatizado

A partir da v2.0, esta skill é orquestrada pelo pipeline em `~/bin/escrita-tooling/tools/`.

```bash
# 1. Criar scope.yaml para o projeto
python -m tools scope "Projeto" --init --modality pesquisa-base

# 2. Editar scope.yaml com keywords, research_axes, exclusion_keywords, etc.

# 3. (Opcional) Extrair seed refs de textos existentes
python -m tools seed "Projeto"

# 4. Executar pipeline completo (10 etapas + extração + brief)
python -m tools run "Projeto"

# 5. Verificar resultado
python -m tools status "Projeto"

# 6. Revisar validation-list.md (~5-10 min)
# 7. Obter artigos não-OA listados em to-obtain.md
# 8. Processar PDFs manuais
python -m tools extract-manual "Projeto"

# 9. Enriquecer refs com resumos Claude
# (via Claude Code interativo — /enrich-refs)
```

**Documentação completa:** `~/bin/escrita-tooling/tools/README.md`

O pipeline automatiza as etapas 2-6 abaixo. As etapas 1 (delimitação humana) e os prompts de IA assistida permanecem como instruções nesta skill.

### Perguntas estruturadas para o scope.yaml

Ao iniciar um novo projeto, o pipeline deve coletar:

1. **Pergunta de pesquisa** — qual o tema central?
2. **Eixos de pesquisa** — quais os subtemas/dimensões? (min. 3, max. 8)
3. **Keywords por eixo** — termos de busca + sinônimos
4. **Exclusões explícitas** — temas que NÃO devem aparecer (cobertos em outros capítulos, fora do escopo)
5. **Acesso institucional** — Tem acesso Capes, ProBE ou assinatura direta? Artigos indispensáveis não-OA serão listados para você providenciar.
6. **Meta de refs** — quantas referências no output final? (default: 40 para pesquisa-base)


---


## 1. Visão geral do pipeline

```
┌─────────────────────────────────────────────────────────────┐
│  ENTRADA: Tema / pergunta de pesquisa / seção a escrever    │
└──────────────────────────┬──────────────────────────────────┘
                           │
                    ┌──────▼──────┐
                    │  ETAPA 1    │  Delimitação
                    │  Escopo     │  Pergunta → Palavras-chave → Limites
                    └──────┬──────┘
                           │
              ┌────────────┼────────────────┐
              │            │                │
       ┌──────▼──────┐ ┌──▼──────────┐ ┌───▼─────────┐
       │  ETAPA 2a   │ │  ETAPA 2b   │ │  ETAPA 2c   │
       │  Bases      │ │  Fontes     │ │  IA assist.  │
       │  indexadas   │ │  instit.    │ │  (descoberta)│
       │  (primária)  │ │  e cinza    │ │  NÃO citação │
       └──────┬──────┘ └──────┬──────┘ └──────┬───────┘
              │               │               │
              └───────────────┼───────────────┘
                              │
                       ┌──────▼──────┐
                       │  ETAPA 3    │  Triagem
                       │  Avaliação  │  Tier + Relevância + Acessibilidade
                       │  de qualid. │  → Gold / Silver / Bronze / Descartar
                       └──────┬──────┘
                              │
                       ┌──────▼──────┐
                       │  ETAPA 4    │  Verificação
                       │  Checklist  │  Checklist 3.1 da skill referências
                       │  por ref    │  + verificação de dados quantitativos
                       └──────┬──────┘
                              │
                       ┌──────▼──────┐
                       │  ETAPA 5    │  Contestação
                       │  scite.ai   │  Para afirmações centrais:
                       │  (opcional)  │  supporting / contrasting / mentioning
                       └──────┬──────┘
                              │
                       ┌──────▼──────┐
                       │  ETAPA 6    │  Registro
                       │  Documentar │  notas_pesquisa.md + Zotero (se usar)
                       └─────────────┘
```


---


## 2. Etapa 1 — Delimitação do escopo

### 2.1 Prompt-template de delimitação

Usar este template para definir a pesquisa antes de ir às bases. Preencher para cada seção ou subtema:

```markdown
## Ficha de pesquisa — [seção/tema]

**Pergunta de pesquisa:**
[Ex: "Quais são as aplicações de Big Data na gestão hospitalar para otimização de leitos?"]

**Palavras-chave (PT):** [Ex: big data, gestão hospitalar, leitos, otimização, saúde]
**Palavras-chave (EN):** [Ex: big data, hospital management, bed management, optimization, healthcare]
**Termos MeSH (se aplicável):** [Ex: "Big Data"[MeSH], "Hospital Administration"[MeSH], "Bed Occupancy"[MeSH]]

**Escopo — incluir:**
- [Ex: aplicações administrativas, gestão de leitos, fluxo de pacientes, eficiência operacional]

**Escopo — excluir:**
- [Ex: diagnóstico por imagem, telemedicina, wearables, aplicações puramente clínicas]

**Período:** [Ex: 2020-2026]
**Idiomas:** [Ex: português, inglês, espanhol]
**Tipo de fontes prioritárias:** [Ex: revisão sistemática, estudo de caso, relatório institucional]

**Contexto geográfico prioritário:** [Ex: Brasil > América Latina > global]
```

### 2.2 Estratégia de palavras-chave

Montar 3 blocos combináveis com operadores booleanos:

| Bloco | Conceito | Termos (OR entre si) |
|---|---|---|
| A | Tecnologia | big data OR artificial intelligence OR machine learning OR analytics |
| B | Domínio | healthcare management OR hospital administration OR health system |
| C | Aplicação | bed management OR resource allocation OR fraud detection OR cost reduction |

**String de busca:** `(Bloco A) AND (Bloco B) AND (Bloco C)`

Adaptar para cada base (PubMed usa MeSH; SciELO aceita português; Google Scholar aceita linguagem natural).


---


## 3. Etapa 2 — Busca em três trilhas paralelas

### 3.1 Trilha A — Bases indexadas (PRIMÁRIA)

Esta é a trilha principal. Todas as demais são complementares.

#### Bases de consenso e como buscar

| Base | O que encontrar | Como buscar | Acesso |
|---|---|---|---|
| **PubMed / PMC** | Artigos biomédicos revisados por pares | String com MeSH terms + filtros de data e tipo | Gratuito |
| **SciELO Brasil** | Artigos brasileiros e latino-americanos | Busca em português + filtros por periódico e área | Gratuito |
| **Google Scholar** | Cobertura ampla (periódicos + teses + grey lit) | Linguagem natural + filtro temporal | Gratuito |
| **Scopus / Web of Science** | Métricas de impacto, artigos indexados | String booleana + filtros | Via CAPES (institucional) |
| **Portal CAPES Periódicos** | Acesso a paywalled (Elsevier, Springer, etc.) | Via proxy institucional | Institucional |
| **LILACS** | Literatura latino-americana em saúde | Descritores DeCS + filtros | Gratuito (BVS) |
| **Semantic Scholar** | IA-powered; API gratuita; citation context | Busca semântica + filtros | Gratuito + API |

#### Strings de busca pré-formatadas por base

**PubMed:**
```
("Big Data"[MeSH] OR "Artificial Intelligence"[MeSH] OR "Machine Learning"[MeSH])
AND ("Hospital Administration"[MeSH] OR "Health Services Administration"[MeSH])
AND ("2020"[PDAT] : "2026"[PDAT])
```

**SciELO:**
```
(big data OR inteligência artificial OR aprendizado de máquina)
AND (gestão hospitalar OR administração em saúde OR gestão em saúde)
```

**Google Scholar:**
```
"big data" OR "artificial intelligence" "healthcare management" OR "hospital management"
```
(Usar aspas para frases exatas; filtrar por ano na interface)

**Semantic Scholar API** (gratuita, até 100 resultados):
```
GET https://api.semanticscholar.org/graph/v1/paper/search
  ?query=big+data+healthcare+management
  &year=2020-2026
  &fieldsOfStudy=Medicine
  &fields=title,authors,year,citationCount,journal,externalIds,abstract
  &limit=50
```

#### Como usar o Claude para buscar

O Claude pode executar buscas via `web_search` e `web_fetch` nas bases de acesso aberto. Para cada base:

1. **PubMed:** buscar `site:pubmed.ncbi.nlm.nih.gov [termos]` ou construir URL direta da API E-utilities
2. **SciELO:** buscar `site:scielo.br [termos em português]`
3. **Google Scholar:** buscar `site:scholar.google.com [termos]` (limitado — Scholar bloqueia scraping)
4. **PMC:** buscar `site:ncbi.nlm.nih.gov/pmc [termos]`

**Preferência de busca do Claude:** PubMed > PMC > SciELO > Scholar > web geral

Para cada resultado encontrado, extrair imediatamente:
- DOI (prioritário — âncora mais estável)
- PMID (se PubMed/PMC)
- Título completo
- Autores
- Periódico, volume, issue, páginas, ano
- URL canônica (não URL de download/PDF)


### 3.2 Trilha B — Fontes institucionais e literatura cinza

| Tipo | Fontes | O que buscar |
|---|---|---|
| **Governo BR** | DATASUS, ANS, RNDS, Ministério da Saúde | Portarias, notas técnicas, relatórios, dados |
| **Legislação** | Planalto, Câmara, Senado, LexML | Decretos, leis, regulamentos |
| **Organismos internacionais** | OMS, OPAS, OECD, World Bank | Relatórios, posicionamentos, guidelines |
| **Associações setoriais** | ANAHP, CFM, SBIS, HIMSS | Ebooks, pesquisas, posicionamentos |
| **Dados de pesquisa BR** | CETIC.br (TIC Saúde), IBGE, IPEA | Surveys nacionais, indicadores |
| **Relatórios de mercado** | PwC, Deloitte, McKinsey, Straits, Grand View | Tamanho de mercado, tendências |

**Regra:** relatórios de consultorias (PwC, McKinsey) são Tier 3. Citá-los como "relatório de mercado" ou "relatório técnico", nunca como evidência científica primária.

**Atenção especial — URLs governamentais BR:** alta taxa de link rot. Sempre:
- Registrar URL completa + data de acesso
- Copiar trecho relevante no `notas_pesquisa.md`
- Buscar URL alternativa (LexML para legislação; BVS para Ministério da Saúde)


### 3.3 Trilha C — IA assistida (SOMENTE DESCOBERTA)

**REGRA FUNDAMENTAL:** Nenhuma referência da Trilha C entra no texto sem passar pela Trilha A ou B para verificação.

#### O que a IA faz bem (validado pela auditoria)

| Uso | Ferramenta recomendada | Produto esperado |
|---|---|---|
| Mapear o ecossistema de um tema | Gemini, ChatGPT | Lista de subtemas, atores, terminologia |
| Descobrir fontes institucionais BR | Gemini | Nomes de bases, relatórios, associações |
| Curadoria editorial (quais 10 fontes importam?) | ChatGPT | Lista priorizada com justificativa |
| Triagem em volume de URLs | Perplexity | Lista de URLs para inspecionar |
| Verificar se citação é apoiada/contestada | scite.ai | Supporting / contrasting / mentioning |

#### O que a IA faz MAL (validado pela auditoria)

| Anti-padrão | Evidência |
|---|---|
| Gerar lista de referências para citar diretamente | 49,8% off-topic; 13,5% inverificáveis |
| Fornecer metadados bibliográficos (autores, vol, pag) | Caso Aldoseri — fabricação completa |
| Fornecer dados quantitativos sem URL | "HC-SP reduziu 40%" — inverificável |
| Substituir busca em bases indexadas | scite.ai: 45,8% refs sobre resíduos hospitalares |

#### Prompt-template para IA assistida (descoberta, NÃO citação)

```markdown
## Prompt para mapeamento conceitual

Estou pesquisando sobre [TEMA] para um [TIPO DE DOCUMENTO: capítulo de livro / artigo / relatório].

**Escopo:** [incluir / excluir]

**Preciso que você:**
1. Liste os 5-8 subtemas principais que este tema abrange
2. Identifique as bases de dados e fontes institucionais mais relevantes para cada subtema
3. Sugira termos de busca (em português E inglês) para cada subtema
4. Liste 5-10 referências-chave que estruturam o campo — para CADA uma, forneça:
   - Autores completos
   - Título exato
   - Periódico/editora
   - Ano
   - DOI (obrigatório se existir)
   - URL (obrigatória)
5. Identifique lacunas: que subtemas têm pouca literatura recente?

**REGRAS:**
- NÃO invente referências. Se não tiver certeza de um DOI ou URL, diga explicitamente.
- Priorize fontes recentes (2020+) e brasileiras quando disponíveis.
- Para dados quantitativos, indique a fonte primária exata (não "estima-se que...").
- Separe claramente: (a) artigos de periódicos indexados, (b) fontes institucionais, (c) relatórios de mercado, (d) fontes informais.

**Contexto geográfico:** [Brasil / América Latina / Global]
```

**Pós-processamento obrigatório:** toda referência sugerida pela IA entra na Etapa 3 (avaliação) e Etapa 4 (verificação) antes de qualquer uso.


---


## 4. Etapa 3 — Avaliação de qualidade (triagem)

### 4.1 Critérios de avaliação

Cada referência candidata recebe três scores independentes:

#### Tier de qualidade (domínio/fonte)

| Tier | Definição | Exemplos | Peso |
|---|---|---|---|
| **T1** | Periódico revisado por pares, indexado | PubMed, SciELO, Scopus, IEEE | ★★★ |
| **T2** | Fonte institucional de referência | OMS, DATASUS, ANS, CETIC.br, HIMSS | ★★★ |
| **T3** | Relatório técnico / especializado | PwC, McKinsey, Deloitte, ANAHP | ★★☆ |
| **T4** | Notícia, blog, site corporativo | Carefy, TopSaúde, YouTube, sites pessoais | ★☆☆ |

**Regra para o texto acadêmico:** T1 e T2 são citáveis sem ressalvas. T3 é citável como "relatório técnico" com nota de contexto. T4 **não é citável** como referência bibliográfica — usar apenas como pista para encontrar a fonte primária.

#### Relevância para o tema

| Score | Definição | Critério |
|---|---|---|
| **2** | Diretamente relevante | O assunto principal do artigo/documento coincide com o escopo da pesquisa |
| **1** | Tangencial | O artigo toca no tema mas o foco é outro (ex: saúde digital em geral, regulação ampla) |
| **0** | Off-topic | Sem relação com o escopo definido na Etapa 1 |

#### Acessibilidade

| Status | Definição |
|---|---|
| **Acessível** | URL/DOI retorna conteúdo legível (HTTP 200 ou acesso via navegador) |
| **Restrito** | Paywall ou bloqueio de bot, mas artigo existe (acessível via CAPES ou navegador) |
| **Quebrado** | 404 ou timeout persistente — URL inválida |
| **Sem URL** | Referência sem link verificável — exige busca manual |

### 4.2 Classificação composta

| Classificação | Critério | Ação |
|---|---|---|
| **🥇 Gold** | T1 ou T2 + relevância 2 + acessível | Usar prioritariamente |
| **🥈 Silver** | T1/T2 + relevância 1 + acessível, OU T3 + relevância 2 + acessível | Usar se necessário; contextualizar |
| **🥉 Bronze** | T3 + relevância 1, OU qualquer tier + restrito | Reserva; buscar alternativa melhor |
| **❌ Descartar** | Relevância 0, OU T4, OU sem URL inverificável, OU quebrado | Não usar |

### 4.3 Meta de composição para um texto acadêmico

| Tier | % ideal do total de referências |
|---|---|
| T1 (periódicos) | ≥ 50% |
| T2 (institucional) | 15-25% |
| T3 (relatórios técnicos) | 10-20% |
| T4 (informal) | 0% |

Se a distribuição final tiver < 50% de T1, voltar à Trilha A e buscar mais artigos em bases indexadas.


---


## 5. Etapa 4 — Verificação individual

Executar o **Checklist 3.1** da skill `referencias-e-citacoes` (~/bin/escrita-tooling/skills/referencias-e-citacoes/SKILL.md) para cada referência que passou pela triagem.

### 5.1 Checklist resumido (referência cruzada)

| # | Verificação | Obrigatório? |
|---|---|---|
| 1 | URL/DOI acessível? | Sim |
| 2 | Conteúdo corrobora a afirmação? | Sim |
| 3 | Formato correto (estilo do projeto)? | Sim |
| 4 | Procedência documentada em notas_pesquisa.md? | Sim |
| 5 | Autores verificados na fonte primária? | Sim |
| 6 | Metadados conferidos (vol, issue, pag, ano)? | Sim |

### 5.2 Verificação reforçada para dados quantitativos

Para qualquer afirmação com número específico (ex: "redução de 40%", "mercado de US$ 50 bi"):

1. Acessar a URL/DOI da fonte
2. Localizar o número exato no texto da fonte (Ctrl+F)
3. Se o número **não estiver lá**: descartar a afirmação ou buscar fonte que a contenha
4. Registrar no `notas_pesquisa.md`: "Dado X confirmado em [fonte], parágrafo/página Y"

**Motivação (caso real):** "80% dos dados clínicos são não estruturados" foi atribuído a Dash et al. (2019), mas o artigo não contém esse número. O dado é amplamente repetido na literatura sem fonte primária clara.

### 5.3 Verificação reforçada para referências sugeridas por IA

Protocolo completo documentado em `~/bin/escrita-tooling/skills/referencias-e-citacoes/SKILL.md`, seção 3.2.

Resumo: verificar existência via DOI → confirmar autores no site do periódico → conferir metadados → localizar dados quantitativos na fonte.


---


## 6. Etapa 5 — Verificação de contestação (scite.ai)

### 6.1 Quando usar

- Para afirmações **centrais** do texto (as que sustentam argumentos-chave)
- Para dados quantitativos que parecem surpreendentes ou contraintuitivos
- Para verificar se uma referência é amplamente apoiada ou contestada pela literatura

### 6.2 Como usar corretamente

O scite.ai **NÃO é um buscador de referências** (auditoria: 45,8% off-topic quando usado assim). Seu uso correto é:

1. Inserir o DOI ou título exato de um artigo específico
2. Verificar o *citation context*: quantas citações são supporting, contrasting, mentioning
3. Se > 20% das citações forem *contrasting*: investigar as objeções antes de citar

### 6.3 Alternativas gratuitas para verificação de contestação

| Ferramenta | Como usar | Limitações |
|---|---|---|
| **Google Scholar — "Citado por"** | Ver quem citou o artigo; ler abstracts dos citantes | Não classifica supporting/contrasting |
| **Semantic Scholar — Citation Context** | API gratuita retorna citation intents (background, method, result) | Classificação menos granular que scite.ai |
| **Connected Papers** | Visualizar cluster de artigos relacionados | Não mostra contestação direta |
| **PubMed — Related Articles** | Artigos similares por algoritmo NLM | Não mostra contestação |


---


## 7. Etapa 6 — Registro e documentação

### 7.1 Formato de registro em notas_pesquisa.md

Para cada referência verificada, registrar:

```markdown
### Ref [N] — Sobrenome et al. (Ano)

- **Título:** [título completo]
- **Periódico:** [nome abreviado], v.X, n.Y, p.Z-W, ano
- **DOI:** [se existir]
- **PMID:** [se PubMed]
- **URL:** [URL canônica, não URL de download]
- **Tier:** T1 / T2 / T3
- **Relevância:** 2 / 1
- **Classificação:** Gold / Silver / Bronze
- **Data de verificação:** [data]
- **Dados sustentados:** [lista de afirmações que esta ref sustenta]
- **Verificação de dados quantitativos:** [número X confirmado no parágrafo Y / não encontrado]
- **Origem da descoberta:** [PubMed / SciELO / Gemini(descoberta) / etc.]
```

### 7.2 Tabela de controle de referências

Manter uma tabela resumo no topo do `notas_pesquisa.md` de cada seção:

```markdown
| # | Autor(es) | Ano | Tier | Relev. | Class. | DOI | Status |
|---|---|---|---|---|---|---|---|
| 1 | Straits Research | 2024 | T3 | 2 | Silver | — | Verificada |
| 2 | Dash et al. | 2019 | T1 | 2 | Gold | 10.1186/s40537-019-0217-0 | Verificada |
```


---


## 8. Prompt-template completo para pesquisa de uma seção

Este é o prompt reutilizável para iniciar a pesquisa de qualquer seção de qualquer projeto. Copiar, preencher os campos entre `[colchetes]` e executar:

```markdown
## Pesquisa para [SEÇÃO/TEMA]

**Projeto:** [nome do projeto]
**Seção:** [ex: 16.3 — Aplicações na gestão hospitalar]
**Arquivo canônico:** [ex: capitulo16.md]
**Notas de pesquisa:** [ex: notas_pesquisa.md]
**Próxima referência:** [N]
**Estilo de citação:** [ex: Vancouver — ver SKILL.md do projeto]

### Escopo da seção
[Descrever o que a seção deve cobrir e o que está FORA do escopo]

### Afirmações que precisam de referência
1. [Afirmação 1 — ex: "IA reduz tempo médio de permanência hospitalar"]
2. [Afirmação 2 — ex: "Operadoras usam ML para detecção de fraudes"]
3. [Afirmação N]

### Instruções para o Claude

**Fase 1 — Busca primária (Trilha A):**
Para cada afirmação acima, buscar em PubMed, SciELO e Google Scholar:
- Priorizar revisões sistemáticas e meta-análises
- Depois estudos observacionais e de caso
- Filtrar: 2020-2026, português e inglês
- Para cada resultado: extrair DOI, autores, título, periódico, ano

**Fase 2 — Fontes institucionais (Trilha B):**
Buscar fontes brasileiras primárias:
- CETIC.br, DATASUS, ANS, ANAHP, SBIS para dados nacionais
- OMS, OPAS para dados e diretrizes internacionais
- Relatórios de mercado se necessário (citar como T3)

**Fase 3 — Avaliação:**
Para cada referência encontrada, classificar:
- Tier (T1/T2/T3/T4)
- Relevância (0/1/2)
- Acessibilidade (acessível/restrito/quebrado)
- Classificação composta (Gold/Silver/Bronze/Descartar)

**Fase 4 — Verificação:**
Para as referências Gold e Silver:
- Acessar a URL/DOI e confirmar que o conteúdo sustenta a afirmação
- Para dados quantitativos: localizar o número exato na fonte
- Registrar tudo em notas_pesquisa.md

**Formato de saída:**
Apresentar as referências em tabela com: #, Autores, Ano, Título, Periódico, DOI, Tier, Relevância, Classificação, Afirmação(ões) sustentada(s).
Depois, bloco formatado no estilo [Vancouver/ABNT/outro] para inserção direta.
```


---


## 9. Modos de operação por tipo de projeto

O rigor do pipeline varia conforme o tipo de produção:

### 9.1 Capítulo de livro-texto (ex: Atheneu)

| Aspecto | Configuração |
|---|---|
| Trilha A (bases indexadas) | Obrigatória — ≥ 50% das refs |
| Trilha B (institucional) | Obrigatória — fontes BR e internacionais |
| Trilha C (IA assistida) | Opcional — para mapeamento inicial |
| Etapa 3 (triagem) | Gold + Silver apenas |
| Etapa 4 (verificação) | Checklist completo para todas |
| Etapa 5 (scite.ai) | Para afirmações centrais |
| Meta de refs | 15-30 refs por capítulo |
| % T1 mínimo | 50% |

### 9.2 Artigo científico / revisão

| Aspecto | Configuração |
|---|---|
| Trilha A | Obrigatória — protocolo PRISMA se revisão sistemática |
| Trilha B | Conforme tema |
| Trilha C | Para mapeamento; documentar no método |
| Etapa 3 | Gold apenas; Silver com justificativa |
| Etapa 4 | Checklist completo + verificação cruzada |
| Etapa 5 | Obrigatória para afirmações quantitativas |
| Meta de refs | Variável (30-100+ para revisão) |
| % T1 mínimo | 80% |

### 9.3 Relatório técnico / texto para aula

| Aspecto | Configuração |
|---|---|
| Trilha A | Recomendada — pelo menos refs-chave |
| Trilha B | Obrigatória (fontes primárias do tema) |
| Trilha C | Livre — com verificação básica |
| Etapa 3 | Gold + Silver + Bronze aceitáveis |
| Etapa 4 | Checklist simplificado (URL + autores + conteúdo) |
| Etapa 5 | Opcional |
| Meta de refs | 5-15 |
| % T1 mínimo | 30% |


---


## 10. Ferramentas recomendadas por etapa

| Etapa | Ferramenta gratuita | Ferramenta paga | Observação |
|---|---|---|---|
| 1. Delimitação | Claude (este prompt) | — | Template acima |
| 2a. Bases indexadas | PubMed, SciELO, Scholar, Semantic Scholar | Scopus, Web of Science (CAPES) | **Trilha principal** |
| 2b. Institucionais | CETIC.br, DATASUS, ANS, OMS | — | Gratuitas e de alta qualidade |
| 2c. IA assistida | Claude (web_search), Perplexity free | Perplexity Pro, ChatGPT Plus, Gemini Advanced | Só para descoberta |
| 3. Triagem | Planilha manual ou tabela em notas_pesquisa.md | — | Critérios seção 4 |
| 4. Verificação | web_fetch (Claude), navegador | — | Checklist seção 5 |
| 5. Contestação | Google Scholar "Citado por", Semantic Scholar | scite.ai (~$20/mês) | Para afirmações centrais |
| 6. Registro | notas_pesquisa.md | Zotero | DOI > URL permanente > URL temporária |
| Verificação de links | Script check_refs.py (Atheneu) | — | Reutilizável entre projetos |


---


## 11. Anti-padrões a evitar

Validados pela auditoria de 251 referências (Cap. 16 Atheneu, 2026-02-18):

| # | Anti-padrão | Consequência | Evidência |
|---|---|---|---|
| 1 | Usar deep research para **gerar lista final de referências** | Refs off-topic (49,8%), sem metadados, risco de alucinação | Auditoria completa |
| 2 | Inserir dados quantitativos sem URL/DOI verificável | Afirmações inverificáveis; possível alucinação | ChatGPT "HC-SP -40%", Gemini "R$ 2 mi" |
| 3 | Citar blogs/sites corporativos como referências acadêmicas | Baixo rigor; inaceitável em livro-texto | Gemini: 35,7% Tier 4 |
| 4 | Usar scite.ai como buscador de tema | Refs off-topic dominantes (resíduos hospitalares, geriatria) | scite.ai: 45,8% off-topic |
| 5 | Não verificar URL/DOI antes de inserir | Link rot, 404, URLs dinâmicas | Ref [5] ANS quebrada |
| 6 | Confiar em metadados bibliográficos fornecidos por IA | Autores fabricados, DOIs inventados | Ref [7] Aldoseri — fabricação completa |
| 7 | Atribuir dados operacionais a documentos normativos | Decreto não contém estatísticas de uso | Ref [4] Decreto 12.560 |
| 8 | Repetir estatísticas amplamente citadas sem rastrear a fonte primária | Dado sem fonte real; viés de repetição | "80% não estruturados" (Dash et al. não contém) |


---


## 11.5 Extract Facts — camada preventiva

Após coletar referências (Etapas 2-6) e antes de escrever, usar o `/extract-facts` para extrair fatos verificados das refs para o Facts Registry. Isso previne erros de dados fabricados, valores incorretos e derivações matemáticas erradas.

**Skill completa:** `~/bin/escrita-tooling/skills/pesquisa-academica/extract-facts.md`

**Uso rápido:**
```bash
python -m tools extract-facts "Projeto" --refs 11-17   # Extrair candidatos
# Claude revisa e escreve .facts-pending.jsonl
python -m tools facts-import "Projeto"                  # Validar e importar
python -m tools facts-status "Projeto" --section 16.3   # Brief para escrita
```


## 11.6 Triagem semântica e descritores (Fase 2, 2026-07-10)

- **Descritores MeSH/DeCS**: `python -m tools descriptors "Projeto"` sugere descritores oficiais (API MeSH da NLM) para os termos livres do scope.yaml → `pipeline/descriptors-suggested.md` com bloco YAML pronto. DeCS aceita o rótulo MeSH em inglês no campo `mh:` da BVS; traduções em decs.bvsalud.org.
- **Triagem semântica (dupla triagem)**: os critérios de inclusão/exclusão em prosa são aplicados por revisor LLM — `screen-export` → Claude julga cada ref → `screen-import` (kappa de Cohen keyword×LLM + divergências em `screening-report.md`) → `run --from-stage 5`. Divergência nunca descarta silenciosamente; decide o revisor humano.
- **PRISMA canônico**: `python -m tools prisma "Projeto"` (automático na etapa 9 para revisões).

## 12. Questões em aberto (roadmap)

- [x] ~~Script de busca automatizada em PubMed via E-utilities API~~ (implementado no pipeline)
- [x] ~~Fluxograma PRISMA~~ (exporter prisma_flow, 2026-07-10)
- [x] ~~Semantic Scholar API~~ (base de busca implementada; fields_of_study configurável)
- [ ] Integração com Index (acervo na VPS) para buscar materiais já no acervo pessoal
- [ ] Template de fichamento por artigo (para revisões mais extensas)
- [ ] Integração com Zotero via Better BibTeX
- [x] ~~Busca semântica vetorial~~ (Fase 3, 2026-07-10: re-ranqueamento + resgate semântico via Ollama/bge-m3 — `python -m tools semantic` ou `semantic_rerank: true` no scope)
