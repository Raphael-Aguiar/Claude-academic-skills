# Prompts Adaptados por Ferramenta — Pipeline de Pesquisa Acadêmica

**Uso:** Cada seção abaixo é um prompt autônomo, calibrado para a ferramenta indicada. Copie o bloco inteiro (do `---` até o próximo `---`) e cole na ferramenta correspondente.

**Estratégia de cruzamento:** Cada ferramenta recebe uma ênfase diferente. Depois, sintetizar:
- **Perplexity** → ferramentas reais, APIs, exemplos concretos com links funcionais
- **ChatGPT** → arquitetura de sistema, diagramas, análise de trade-offs
- **Gemini AI Studio** → cenários completos ponta a ponta, análise comparativa profunda
- **Claude Pesquisa** → viabilidade técnica, implementação no meu ambiente, scripts

---
---

# 1. PERPLEXITY

> **Por que esta versão:** Perplexity é a melhor ferramenta para encontrar fontes reais com URLs funcionais. A ênfase aqui é em **ferramentas concretas, APIs com documentação, e exemplos de workflows existentes** — não em teoria.

---

Preciso projetar um **pipeline de pesquisa de literatura acadêmica** que maximize a qualidade das referências bibliográficas em textos acadêmicos. Quero que você me ajude a encontrar **ferramentas, APIs e métodos concretos** para cada etapa.

## Contexto

Realizei uma auditoria de 251 referências produzidas por 4 ferramentas de IA (Perplexity, scite.ai, Gemini, ChatGPT) para um capítulo de livro acadêmico. Resultados:
- **50% das referências eram off-topic**
- Apenas 9,6% eram "Gold" (periódico indexado + relevante + acessível)
- Uma referência tinha autores, DOI e metadados completamente fabricados
- ChatGPT forneceu dados quantitativos inverificáveis (sem URL)
- scite.ai confundiu "gestão em saúde" com "gestão de resíduos hospitalares"
- Perplexity despejou 175 URLs sem metadados bibliográficos

O problema central: **como garantir que toda referência inserida num texto acadêmico seja verificável, acessível, relevante e correta.**

## O que eu preciso de você (foco em ferramentas concretas)

### 1. APIs gratuitas para busca acadêmica

Para cada API abaixo, quero: URL da documentação, limites de uso gratuito, formato de resposta, e um exemplo de query relevante para o tema "big data healthcare management".

- **PubMed E-utilities** (NCBI)
- **Semantic Scholar API** (Allen AI)
- **OpenAlex API**
- **CrossRef API**
- **CORE API** (acesso aberto)
- **Europe PMC API**
- **Unpaywall API** (verificar acesso aberto)
- **SciELO API** (se existir)
- **Outras que eu esteja ignorando**

Para cada uma: o que ela faz melhor que as outras? Qual retorna metadados mais completos (autores, DOI, abstract, citation count)?

### 2. Ferramentas de gestão de referências compatíveis com Markdown/Obsidian

Quero saber como integrar no meu workflow (Linux Mint + Obsidian + Git):
- **Zotero + Better BibTeX:** como exportar para .bib e usar em Markdown?
- **Zotero + Obsidian Citations plugin:** funciona bem? Limitações?
- **CSL-JSON vs. BibTeX:** qual é mais adequado para um workflow Markdown → Pandoc → Word/PDF?
- **Alternativas ao Zotero** que funcionem melhor com Markdown?

### 3. Ferramentas de verificação automatizada

Busque ferramentas e scripts que:
- Verifiquem se um DOI existe e retornem metadados (autores, título, periódico)
- Verifiquem se uma URL está ativa (HTTP status check)
- Classifiquem automaticamente o tier de uma fonte (periódico indexado vs. blog vs. institucional)
- Detectem possíveis referências fabricadas (inconsistência entre DOI e metadados)

Pode ser: scripts Python existentes no GitHub, pacotes npm, ferramentas CLI, ou APIs que façam isso.

### 4. Workflows publicados

Busque artigos, posts, ou repositórios GitHub que documentem **pipelines de revisão de literatura assistidos por IA** — especialmente:
- Projetos que usem Semantic Scholar ou OpenAlex para triagem automatizada
- Workflows que combinem Zotero + Obsidian + Pandoc para escrita acadêmica
- Métodos publicados de verificação de referências geradas por IA
- Protocolos PRISMA adaptados para revisões narrativas (não sistemáticas)

### 5. scite.ai — uso correto

Minha auditoria mostrou que scite.ai é péssimo como buscador de temas (46% off-topic) mas supostamente bom para verificar citation context (supporting/contrasting/mentioning). Busque:
- Documentação da API do scite.ai
- Artigos que avaliem a acurácia do scite.ai como ferramenta de verificação
- Alternativas gratuitas para verificar se uma citação é apoiada ou contestada

### Formato de resposta

Para cada item, quero:
- Nome da ferramenta/API
- URL funcional da documentação
- O que faz e o que NÃO faz
- Preço (grátis / freemium / pago)
- Como integra com meu stack (Linux + Obsidian + Python + Git)

Não me dê recomendações genéricas. Quero links que funcionem e exemplos concretos.

---
---

# 2. CHATGPT (Deep Research)

> **Por que esta versão:** ChatGPT é forte em análise estruturada, design de sistemas e diagramas. A ênfase aqui é na **arquitetura do pipeline, análise de trade-offs e pontos de decisão** — a parte conceitual que o Perplexity não faz bem.

---

Preciso projetar um **pipeline de pesquisa de literatura acadêmica** que maximize a qualidade das referências bibliográficas em textos acadêmicos (capítulos de livro, artigos, relatórios). Quero que você analise o problema em profundidade e proponha uma **arquitetura de sistema** com etapas, pontos de decisão e trade-offs explícitos.

## Evidência empírica

Realizei uma auditoria rigorosa de 251 referências produzidas por 4 ferramentas de IA para um capítulo de livro acadêmico. Os dados completos:

**Por ferramenta:**

| Ferramenta | Refs | URLs fornecidas | % Tier 1 (periódico) | % Off-topic | Risco alucinação |
|---|---|---|---|---|---|
| Perplexity | 175 | 100% | 58% | 60% | Baixo |
| scite.ai | 24 | 0% | n/a | 46% | Médio |
| Gemini | 42 | 100% | 12% | 21% | Médio |
| ChatGPT | 10 | 0% | n/a | 0% | Alto |

**Agregado:** 50% off-topic, 42% Tier 1, apenas 9,6% "Gold" (T1 + relevante + acessível).

**Problemas graves:** 1 referência fabricada (autores/DOI/metadados inventados), dados quantitativos inverificáveis, confusão semântica de escopo, mistura de fontes acadêmicas com blogs sem hierarquização.

## Critérios de avaliação (use estes como base)

**Tier:** T1 (periódico indexado) > T2 (institucional: OMS, DATASUS) > T3 (relatório técnico: PwC, McKinsey) > T4 (blog/informal — inadmissível)

**Relevância:** 2 (diretamente relevante) > 1 (tangencial) > 0 (off-topic)

**Acessibilidade:** Acessível > Restrito (paywall, mas existe) > Quebrado > Sem URL

**Composta:** Gold (T1-2 + rel.2 + acessível) > Silver (T1-2 + rel.1, ou T3 + rel.2) > Bronze > Descartar

## O que eu preciso de você

### Parte 1 — Diagnóstico expandido

Analise os dados da auditoria e identifique:
- **Falhas sistêmicas** que expliquem por que 50% das referências foram off-topic
- **Falhas de processo** que permitiram uma referência fabricada passar despercebida
- **Problemas que eu posso não ter percebido** — vieses cognitivos, lacunas no meu framework de avaliação, riscos que a auditoria não capturou
- **Tensões inerentes** do problema: velocidade vs. rigor, automação vs. julgamento, volume vs. precisão

### Parte 2 — Arquitetura do pipeline

Proponha uma arquitetura com:
- **Diagrama de fluxo** (use Mermaid se possível) mostrando etapas, entradas, saídas e decisões
- **Gates de qualidade:** pontos onde uma referência é aceita, mandada para revisão, ou descartada
- **Loops de feedback:** como erros detectados numa etapa retroalimentam etapas anteriores
- **Modos de operação:** como o mesmo pipeline se adapta para (a) capítulo de livro ≈15-30 refs, (b) artigo de revisão ≈50-100 refs, (c) relatório técnico ≈5-15 refs

### Parte 3 — Papel de cada ferramenta

Crie uma **matriz ferramenta × etapa** com:

| Etapa | Melhor ferramenta | Uso correto | Uso incorreto (anti-padrão) |
|---|---|---|---|
| ... | ... | ... | ... |

Ferramentas a mapear: PubMed, SciELO, Google Scholar, Semantic Scholar, OpenAlex, Perplexity, ChatGPT, Gemini, Claude, scite.ai, Zotero, CrossRef.

### Parte 4 — Análise de trade-offs

Para cada trade-off abaixo, analise as opções e recomende:

1. **Rigor vs. velocidade:** verificar cada referência individualmente vs. triagem por amostragem
2. **Busca manual vs. automatizada:** PubMed web interface vs. E-utilities API vs. Semantic Scholar API
3. **IA como assistente vs. IA como fonte:** onde exatamente está a fronteira segura?
4. **Verificação humana vs. verificação por script:** que checks podem ser automatizados com confiança?
5. **Gestão simples vs. robusta:** notas em Markdown vs. Zotero + BibTeX vs. banco de dados próprio

### Parte 5 — Falhas de modo (failure modes)

Para cada etapa do pipeline, identifique:
- O que pode dar errado?
- Qual é o impacto (referência errada no texto final)?
- Como detectar a falha?
- Como prevenir?

Preste atenção especial a: alucinação de IA, link rot, atribuição incorreta de dados, viés de confirmação na seleção de fontes.

### Formato de resposta

- Diagnóstico em prosa (não lista de bullet points)
- Diagrama Mermaid do pipeline
- Tabelas para matrizes e trade-offs
- Conclusão com as 3 decisões de design mais importantes que eu preciso tomar

Seja analítico, não prescritivo. Onde houver incerteza real, apresente as opções com prós/contras em vez de fingir que há uma resposta óbvia.

---
---

# 3. GEMINI (AI STUDIO)

> **Por que esta versão:** Gemini AI Studio tem contexto longo e é bom em análise comparativa e cenários detalhados. A ênfase aqui é em **cenários ponta a ponta** — simular a execução completa do pipeline para diferentes tipos de projeto, identificando onde ele quebra.

---

Preciso projetar um **pipeline de pesquisa de literatura acadêmica** que maximize a qualidade das referências bibliográficas em textos acadêmicos. Quero que você analise o problema através de **cenários concretos de uso**, simulando a execução do pipeline para diferentes tipos de projeto.

## Contexto e dados

Realizei uma auditoria de 251 referências produzidas por 4 ferramentas de IA para um capítulo de livro acadêmico. Dados-chave:

- 50% das referências eram completamente off-topic
- Apenas 9,6% eram "Gold" (periódico indexado + relevante + acessível)
- 1 referência fabricada (autores/DOI inventados por IA) passou despercebida
- Ferramentas de IA são úteis para descoberta conceitual, mas perigosas como fonte de referências

**Tabela comparativa:**

| Ferramenta | Refs | URLs | % Tier 1 | % Off-topic | Risco alucinação |
|---|---|---|---|---|---|
| Perplexity | 175 | 100% | 58% | 60% | Baixo |
| scite.ai | 24 | 0% | n/a | 46% | Médio |
| Gemini | 42 | 100% | 12% | 21% | Médio |
| ChatGPT | 10 | 0% | n/a | 0% | Alto |

**Critérios de avaliação que uso:**

- **Tier:** T1 (periódico indexado) > T2 (institucional) > T3 (relatório técnico) > T4 (blog — inadmissível)
- **Relevância:** 2 (diretamente relevante) > 1 (tangencial) > 0 (off-topic)
- **Composta:** Gold (T1-2 + rel.2 + acessível) > Silver > Bronze > Descartar

## O que eu preciso de você

### Análise 1 — Simulação de cenários

Simule a execução completa do pipeline para **três cenários reais**. Para cada um, descreva passo a passo o que o pesquisador faria, com que ferramenta, quanto tempo levaria, e onde o processo pode falhar:

**Cenário A — Capítulo de livro-texto** (meu caso atual)
- Tema: "Aplicações de Big Data e IA na gestão hospitalar"
- Meta: 15-25 referências, mix de artigos + fontes institucionais + relatórios
- Estilo: Vancouver
- Contexto: Brasil prioritário, complementado por internacional
- Prazo: 2 semanas para pesquisa
- Restrição: evitar sobreposição com capítulos adjacentes sobre telemedicina e wearables

**Cenário B — Artigo de revisão narrativa**
- Tema: "Detecção de fraudes em operadoras de saúde com machine learning"
- Meta: 40-60 referências, predominantemente artigos indexados
- Estilo: ABNT
- Contexto: global com foco em exemplos brasileiros
- Prazo: 4 semanas para pesquisa

**Cenário C — Relatório técnico interno**
- Tema: "Viabilidade de implementação de analytics preditivo em hospital público brasileiro"
- Meta: 8-12 referências, mix pragmático (artigos + dados oficiais + benchmarks)
- Estilo: livre (referências como notas de rodapé)
- Prazo: 3 dias

Para cada cenário, quero:
1. **Dia a dia:** o que fazer em cada dia/fase
2. **Ferramentas por etapa:** qual ferramenta usar e por quê
3. **Pontos de falha:** onde o cenário pode dar errado (baseado nos dados da minha auditoria)
4. **Critério de parada:** como saber que tenho referências suficientes e de qualidade suficiente
5. **Resultado esperado:** distribuição de tiers e classificações ao final

### Análise 2 — Comparação de estratégias de busca

Para o **Cenário A** (capítulo de livro), compare **três estratégias alternativas** de busca:

**Estratégia 1 — IA-first:** Começar com deep research (Perplexity/Gemini/ChatGPT), usar output como base, verificar depois
**Estratégia 2 — Bases-first:** Começar em PubMed/SciELO/Scholar, usar IA só para descobrir fontes institucionais
**Estratégia 3 — Híbrida:** IA para mapeamento conceitual (sem extrair refs), depois bases indexadas para referências, depois IA para verificação

Para cada estratégia, estime:
- Tempo total
- % esperado de referências Gold no resultado final
- Risco de alucinação
- Cobertura (chance de perder referências relevantes)
- Esforço de verificação posterior

### Análise 3 — O problema do "80% não estruturados"

Na minha auditoria, encontrei que o dado "80% dos dados clínicos são não estruturados" — amplamente repetido na literatura de saúde digital — **não aparecia na fonte citada** (Dash et al., 2019). Esse tipo de dado-zumbi (repetido sem fonte primária rastreável) é um problema sistêmico.

Analise:
- Como dados-zumbi se propagam na literatura acadêmica?
- Como um pipeline de pesquisa pode detectar e evitar citação de dados sem fonte primária?
- Que verificações adicionais são necessárias para afirmações quantitativas específicas (ex: "redução de 40% nos custos")?

### Análise 4 — Proposta de pipeline

Com base nas simulações e análises, proponha:
1. **Arquitetura geral** do pipeline (etapas, entradas/saídas, decisões)
2. **Checklists** por etapa
3. **Métricas de qualidade** — como saber se o pipeline está funcionando (ex: % Gold nas refs finais, taxa de verificação com sucesso)
4. **Adaptações por cenário** — o que muda entre capítulo de livro, artigo de revisão e relatório técnico

### Formato de resposta

- Use tabelas comparativas sempre que possível
- Para os cenários, use formato de cronograma (Dia 1, Dia 2...) com ações concretas
- Seja específico: nomes de ferramentas, strings de busca, critérios numéricos
- Inclua uma seção "O que pode dar errado" para cada proposta

---
---

# 4. CLAUDE (Pesquisa)

> **Por que esta versão:** Claude com pesquisa web pode verificar ferramentas e APIs em tempo real. A ênfase aqui é em **viabilidade técnica, implementação no meu ambiente específico, e automação prática** — o que realmente funciona hoje.

---

Preciso projetar um **pipeline de pesquisa de literatura acadêmica** que maximize a qualidade das referências bibliográficas em textos acadêmicos. Quero que você foque na **viabilidade técnica e implementação prática** no meu ambiente de trabalho.

## Contexto

Realizei uma auditoria de 251 referências produzidas por 4 ferramentas de IA. Resultados-chave:
- 50% off-topic, apenas 9,6% "Gold"
- 1 referência fabricada (autores/DOI inventados)
- Ferramentas de IA são boas para descoberta conceitual, perigosas como fonte de referências

**Meu ambiente:**
- Linux Mint 22
- Obsidian (vault em ~/PKM/Escrita/, versionado com Git)
- Python 3.x disponível
- Claude (API + Desktop com MCP filesystem), ChatGPT Plus, Perplexity Pro, Gemini Advanced, scite.ai
- Acesso Portal CAPES
- Texto em Markdown, conversão final via Pandoc para Word/PDF
- Estilo de citação varia por projeto (Vancouver, ABNT)

**Critérios de avaliação de referências:**
- Tier: T1 (periódico indexado) > T2 (institucional) > T3 (relatório técnico) > T4 (inadmissível)
- Relevância: 2 (direto) > 1 (tangencial) > 0 (off-topic)
- Classificação composta: Gold > Silver > Bronze > Descartar

## O que eu preciso de você

### Parte 1 — APIs acadêmicas: o que funciona HOJE

Pesquise e verifique **agora** o estado atual destas APIs. Para cada uma, quero: URL da documentação, se está funcionando, limites gratuitos, e o que retorna (campos disponíveis).

1. **PubMed E-utilities (NCBI)** — busca + metadados de artigos biomédicos
2. **Semantic Scholar API (Allen AI)** — busca semântica + citation intents
3. **OpenAlex API** — substituto aberto do Microsoft Academic
4. **CrossRef API** — metadados a partir de DOI
5. **Unpaywall API** — verificar acesso aberto a partir de DOI
6. **Europe PMC API** — alternativa ao PubMed com acesso a full-text
7. **CORE API** — repositórios de acesso aberto
8. **SciELO** — existe API? Como buscar programaticamente?
9. **LILACS/BVS** — existe API acessível?

Para cada uma que estiver funcional, escreva um **exemplo de chamada** (curl ou Python requests) para buscar artigos sobre "big data healthcare management" publicados entre 2020-2026.

### Parte 2 — Script de verificação de referências

Proponha a arquitetura de um script Python (`verify_refs.py`) que:

1. **Entrada:** arquivo .md ou .json com lista de referências (DOI, URL, autores, título, periódico, ano)
2. **Verificações automáticas:**
   - DOI existe? (CrossRef API)
   - URL acessível? (HTTP HEAD request)
   - Metadados do DOI batem com os declarados? (autores, título, ano)
   - Periódico é indexado? (verificar contra lista de ISSNs do Scopus/PubMed)
   - Acesso aberto disponível? (Unpaywall)
3. **Saída:** relatório com status por referência (✅ verificada / ⚠️ atenção / ❌ problema)

Não precisa implementar tudo agora — quero a **arquitetura** (funções, fluxo, dependências) e uma **implementação mínima** que verifique DOIs e URLs.

### Parte 3 — Integração Zotero + Obsidian + Pandoc

Pesquise como montar este workflow:
1. Referências armazenadas no Zotero (com DOI e PDF quando possível)
2. Exportação via Better BibTeX para arquivo .bib atualizado automaticamente
3. Citações no Obsidian (Markdown) usando chaves do BibTeX
4. Pandoc converte .md → .docx usando .bib + estilo CSL (Vancouver ou ABNT)

Questões específicas:
- **Obsidian Citations plugin** vs. **Pandoc Reference List** vs. **citar manualmente e converter depois** — o que funciona melhor na prática?
- **CSL para Vancouver (ICMJE):** existe CSL pronto? URL?
- **CSL para ABNT:** existe CSL pronto e confiável? (ABNT tem peculiaridades)
- **Pandoc command line** exata para converter .md com citações → .docx formatado

### Parte 4 — Automação no Claude Desktop / Claude API

Dado que o Claude é meu executor principal (com acesso a web_search, web_fetch e filesystem):
- Que partes do pipeline de pesquisa o Claude consegue executar autonomamente?
- Que partes exigem intervenção humana?
- Como estruturar prompts para que o Claude faça buscas em PubMed/SciELO via web_search de forma eficaz?
- Como o Claude pode usar as APIs acima via web_fetch?

### Parte 5 — Proposta de pipeline focada em implementação

Com base nas partes anteriores, proponha um pipeline **que eu consiga começar a usar amanhã** com as ferramentas que já tenho. Quero:

1. **Etapas com ferramentas concretas** (não "use uma base indexada" — qual base, com que query)
2. **O que automatizar primeiro** (quick wins de maior impacto)
3. **O que deixar manual** (por ser impossível ou não valer a pena automatizar agora)
4. **Ordem de implementação** — o que montar primeiro para ter valor imediato

### Formato de resposta

- Código real (Python, bash, curl) para as partes técnicas
- Links verificados para documentação e ferramentas
- Separação clara entre "funciona hoje" e "ideal futuro"
- Seja pragmático: prefira soluções simples que funcionam a arquiteturas elegantes que exigem semanas de setup
