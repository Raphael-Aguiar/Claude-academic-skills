# Prompt para Deep Research — Pipeline de Pesquisa Acadêmica com Referências de Alta Qualidade

**Uso:** Colar este prompt em ferramentas de deep research (Perplexity, Gemini, ChatGPT, Claude) para obter propostas independentes de solução. Depois, sintetizar as melhores ideias.

---

## O prompt

Preciso da sua ajuda para projetar um **pipeline de pesquisa de literatura acadêmica** que maximize a qualidade das referências bibliográficas que uso em textos acadêmicos (capítulos de livro, artigos científicos, relatórios técnicos).

O problema central é: **como garantir que toda referência inserida num texto acadêmico seja verificável, acessível, relevante e correta** — especialmente num cenário em que ferramentas de IA (inclusive deep research) são usadas como parte do processo de pesquisa.

### Contexto e evidência empírica

Realizei uma auditoria rigorosa de 251 referências produzidas por 4 ferramentas de IA (Perplexity, scite.ai, Google Gemini, ChatGPT) durante a pesquisa de um capítulo de livro acadêmico sobre Big Data e IA em gestão de saúde. Os resultados foram alarmantes:

**Acessibilidade (217 URLs verificadas):**
- 75% acessíveis (HTTP 200)
- 9% bloqueadas por bot (403)
- 1% não encontradas (404)
- 15% com erros diversos (timeout, 400, 500)

**Qualidade científica:**
- 42% de periódicos revisados por pares (Tier 1)
- 4% de fontes institucionais de referência (Tier 2)
- 12% de relatórios técnicos (Tier 3)
- 6% de blogs/fontes informais (Tier 4)
- 36% não classificáveis

**Relevância para o tema do capítulo:**
- 31% diretamente relevantes
- 19% tangenciais
- **50% completamente off-topic**

**Problemas por ferramenta:**

| Ferramenta | Refs | URLs | % Tier 1 | % Off-topic | Risco de alucinação |
|---|---|---|---|---|---|
| Perplexity | 175 | 100% | 58% | 60% | Baixo (URLs verificáveis) |
| scite.ai | 24 | 0% | n/a | 46% | Médio (sem URLs) |
| Gemini | 42 | 100% | 12% | 21% | Médio |
| ChatGPT | 10 | 0% | n/a | 0% | Alto (sem URLs, dados quantitativos inverificáveis) |

**Problemas graves documentados:**
1. Uma referência completamente fabricada (autores, DOI, volume e páginas inventados) passou despercebida até a auditoria
2. ChatGPT afirmou "Hospital das Clínicas SP reduziu 40% tempo de auditoria" — sem URL, inverificável, possivelmente alucinado
3. scite.ai retornou artigos sobre gestão de resíduos hospitalares e geriatria quando o tema era gestão digital em saúde (confundiu "gestão em saúde" com "gestão hospitalar" em sentido amplo)
4. Gemini misturou fontes de alta autoridade (CETIC.br, Ministério da Saúde) com blogs corporativos e sites pessoais, sem hierarquização
5. Perplexity forneceu 175 URLs sem metadados (autores, título, periódico, ano) — exigindo consulta individual a cada link

**Achado positivo:** Apenas 24 referências (9,6%) eram "Gold" — Tier 1, diretamente relevantes e acessíveis. Todas vieram do Perplexity, mas foram encontradas entre 175 resultados com 60% de lixo.

### O que eu preciso que você proponha

Quero um **esquema operacional completo** — não uma lista genérica de dicas, mas uma arquitetura de pipeline com etapas claras, critérios objetivos, ferramentas específicas e pontos de decisão. O pipeline deve resolver estes problemas:

**Problema 1 — Descoberta eficiente:** Como encontrar as referências certas para um tema acadêmico específico, priorizando bases de dados de consenso (PubMed, SciELO, Scopus, Web of Science, LILACS, Google Scholar, Semantic Scholar), sem depender de IA generativa como fonte primária?

**Problema 2 — Filtragem de qualidade:** Como classificar e triar referências de forma sistemática para separar o que é citável (periódico revisado por pares, fonte institucional de referência) do que não é (blog, site corporativo, fonte inverificável)?

**Problema 3 — Verificação anti-alucinação:** Como garantir que nenhuma referência fabricada, com metadados inventados ou dados quantitativos não existentes na fonte, entre no texto final?

**Problema 4 — Rastreabilidade:** Como documentar todo o processo de forma que seja auditável — qualquer pessoa consiga verificar de onde veio cada referência e por que foi incluída?

**Problema 5 — Uso correto de IA no processo:** Qual é o papel adequado de ferramentas de IA (Perplexity, ChatGPT, Gemini, Claude, scite.ai, Semantic Scholar) em cada etapa do pipeline, dado que são úteis para mapeamento conceitual e descoberta, mas perigosas como fonte final de referências?

**Problema 6 — Escalabilidade:** O pipeline deve funcionar tanto para um capítulo de livro (15-30 referências) quanto para um artigo de revisão (50-100+ referências) ou um relatório técnico (5-15 referências), com diferentes níveis de rigor conforme o contexto.

### Critérios de avaliação de referências (usar estes)

Cada referência deve ser avaliada em três dimensões independentes:

**Tier de qualidade (fonte):**
- T1: periódico revisado por pares, indexado (PubMed, SciELO, Scopus)
- T2: fonte institucional de referência (OMS, DATASUS, ANS, CETIC.br, HIMSS)
- T3: relatório técnico/especializado (PwC, McKinsey, Deloitte, ANAHP)
- T4: notícia, blog, site corporativo (inadmissível como referência acadêmica)

**Relevância:**
- 2: diretamente relevante (assunto principal coincide com escopo)
- 1: tangencial (toca no tema mas foco é outro)
- 0: off-topic

**Acessibilidade:**
- Acessível: URL/DOI retorna conteúdo
- Restrito: paywall, mas artigo existe (acessível via CAPES)
- Quebrado: 404 ou timeout persistente
- Sem URL: referência sem link verificável

**Classificação composta:**
- Gold: T1/T2 + relevância 2 + acessível → usar prioritariamente
- Silver: T1/T2 + relevância 1, ou T3 + relevância 2 → usar com contexto
- Bronze: T3 + relevância 1, ou qualquer + restrito → reserva
- Descartar: relevância 0, T4, sem URL, ou quebrado

### Restrições e contexto técnico

- Meu ambiente de trabalho é Linux Mint + Obsidian (vault de escrita com arquivos .md) + Git para versionamento
- Tenho acesso a: Claude (API + Desktop + web), ChatGPT Plus, Perplexity Pro, Gemini Advanced, scite.ai
- Tenho acesso institucional ao Portal CAPES para periódicos paywalled
- Uso Markdown como formato de trabalho; a conversão para Word/PDF é feita no final
- O texto canônico fica em `.md` e as notas de pesquisa em arquivo separado
- Preciso que o pipeline funcione com o Claude como executor principal (ele tem web_search, web_fetch, filesystem tools), complementado por buscas manuais quando necessário
- O estilo de citação varia por projeto (Vancouver, ABNT, APA) — o pipeline deve ser agnóstico ao estilo
- Meu contexto principal é saúde pública, gestão em saúde e saúde digital, mas o pipeline deve ser generalizável

### Questões em aberto que quero que você aborde

1. **Nível de rigor variável:** como ajustar o pipeline para revisão sistemática formal (protocolo PRISMA) vs. revisão narrativa (capítulo de livro) vs. pesquisa exploratória (relatório técnico)?
2. **APIs gratuitas:** qual o custo-benefício real de Semantic Scholar API (gratuita), PubMed E-utilities (gratuita), OpenAlex API (gratuita) vs. ferramentas pagas (scite.ai, Perplexity Pro)?
3. **Automação:** que partes do pipeline podem ser automatizadas com scripts (Python, bash) e quais exigem julgamento humano?
4. **Gestão de referências:** Zotero, formato BibTeX, CSL-JSON — qual a melhor forma de armazenar e gerenciar referências em um workflow baseado em Markdown/Obsidian?
5. **Como registrar buscas de forma reprodutível** — para que eu ou qualquer pessoa consiga refazer a busca e encontrar os mesmos resultados?

### Formato de resposta esperado

Quero uma **análise do problema** seguida de uma **proposta de solução**:

1. **Diagnóstico:** sua análise dos problemas que descrevi, incluindo quaisquer problemas adicionais que você identifique e que eu não tenha mencionado
2. **Arquitetura do pipeline:** fluxo visual (pode ser texto/ASCII/Mermaid) com etapas, entradas, saídas e pontos de decisão
3. **Detalhamento de cada etapa:** o que fazer, com que ferramenta, que critério aplicar, que produto gerar
4. **Papel de cada ferramenta de IA:** tabela mapeando ferramenta × etapa × uso correto × uso incorreto
5. **Automação possível:** o que pode ser scriptado vs. o que exige julgamento humano
6. **Trade-offs:** onde o pipeline sacrifica eficiência por rigor (e vice-versa), e como calibrar
7. **Implementação prática:** como começar amanhã, com as ferramentas que já tenho

Seja específico e operacional. Não quero princípios genéricos — quero um esquema que eu consiga executar.
