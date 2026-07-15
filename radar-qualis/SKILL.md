# Skill: radar-qualis — Radar Qualis (recomendação de periódicos para submissão)

**Versão**: 2.0 (2026-07-14) · **Status**: Ativa

**Radar Qualis** é o nome do sistema (assim que Raphael o chama). Dado um
manuscrito, uma semente do vault ou um tema/abstract, produz uma
**shortlist ranqueada e justificada de periódicos para submissão** pelo critério
**VIGENTE** da CAPES (ciclo 2025-2028), combinando consolidação Área 22,
aderência temática, tipo de estudo, APC, open access, tempo até 1ª decisão,
indexação e uma estimativa qualitativa de chance de aceite. **Todo dado tem
fonte + data; dado ausente aparece como "não disponível" — nunca se inventa
APC, ISSN, percentil ou consolidação.**

Tarefa DISTINTA da `pesquisa-academica` (que monta referências). Aqui a
unidade é o periódico (ISSN), não o artigo.

## ⚠️ Mudança de regime (o essencial — ler antes de operar)

O **Qualis Periódicos acabou**. A lista de jan/2026 (quadriênio 2021-2024)
foi a **última**; a CAPES não a atualizará mais. Para artigos publicados
**até 2024**, o Qualis ainda classifica (retrospectivo). Para o que Raphael
escrever **de 2025 em diante** (ciclo 2025-2028, avaliação em 2029), vale a
**classificação do artigo** — e, na Área 22 (Saúde Coletiva), o operador
prático é se o periódico é **consolidado**:

- **Porta A (bibliométrica)**: indexado em Scopus e/ou Web of Science com
  indicador de impacto **≥ percentil 50** (Q1 ou Q2) em **pelo menos uma
  categoria de indexação relacionada à Saúde Coletiva** (Public Health,
  Epidemiology, Health Policy, Health Informatics, Medicine etc.).
- **Porta B (SciELO)**: periódico da coleção **SciELO Saúde Pública** com
  índice **h5 (Google Scholar) > percentil 60** da área.

**Armadilha central (interdisciplinaridade)**: categoria só de Educação ou
Ciência da Computação **não** cumpre a Porta A, por melhor que seja o
percentil. Precisa de ≥1 categoria de **saúde** em Q1/Q2.

**A pergunta mudou** de "qual o Qualis da revista?" para "a revista tem
categoria de saúde em Q1/Q2 no Scopus/WoS, ou está na SciELO Saúde Pública
com h5 competitivo?".

## Dois cuidados de honestidade (invioláveis)

1. **Proxy**: o critério oficial é **CiteScore (Scopus)** ou **JIF (JCR)**;
   o tooling usa o **quartil SJR (Scimago)** por categoria como *proxy*
   (também derivado da Scopus, correlato mas não idêntico). Verdict
   "consolidado" = **provável; confirmar no CiteScore** (scopus.com/sources).
2. **Assimetria**: um verdict **negativo pela Porta A não é definitivo** — o
   periódico pode cumprir via CiteScore/JIF real ou via WoS, que o tooling
   não consulta. A categoria de saúde que casou é sempre mostrada.

## Infraestrutura

- CLI: `cd ~/bin/escrita-tooling && .venv/bin/python -m tools journals <cmd>`
- Base local: `data/journals/periodicos.db` (SQLite). `status` mostra
  contagens (incl. consolidados) e registros vencidos.
- Cache = o banco (TTLs por família). Qualis 2021-2024, categorias Scimago e
  coleção SciELO SP são **estáticos** (ingestão anual/única).

### Bootstrap (só se `status` mostrar base vazia)

```bash
python -m tools journals init-db
python -m tools journals download-qualis          # XLSX Sucupira (legado)
python -m tools journals ingest-qualis --arquivo data/journals/raw/qualis_2021_2024.xlsx
python -m tools journals ingest-scimago-categorias --csv data/journals/raw/scimago_categorias_2025.csv  # Porta A
python -m tools journals ingest-scielo-sp          # Porta B (coleção SciELO SP)
python -m tools journals ingest-scimago --csv <CSV SJR>   # SJR geral (opcional)
python -m tools journals seed-from-vault
```

(As fontes estáticas `scimago_categorias_2025.csv` e
`scielo_saude_publica.json` são geradas do parquet sjrdata e do articlemeta;
ver README do subpacote. Atualizar categorias anualmente — Scimago sai em
junho.)

## Arquitetura multi-modelo (regra de custo)

| Papel | Modelo | Como |
|---|---|---|
| Perfil do manuscrito | **Sonnet** (subagente) | 1 chamada, JSON estruturado |
| Coleta de APIs + consolidação | **nenhum LLM** | `recommend`/`enrich` (Python) |
| Extração de páginas de editora | **Haiku** (fan-out, 1/snapshot) | Agent tool ou Workflow |
| Julgamento final de fit | **modelo da sessão** (Fable/Opus) | 1 análise sobre ≤12 finalistas |
| Re-varredura em massa (opcional) | Ollama local | `tools/journals/llm.py` |

## Fluxo

### 1. Perfil do manuscrito (Sonnet)

Subagente Sonnet lê o manuscrito/semente/tema e devolve APENAS:

```json
{"tema": "<1 frase>", "tipo_de_estudo": "<original|revisão|ensaio|protocolo|relato de experiência|...>",
 "populacao": "<ou null>", "idioma_manuscrito": "<pt|en|...>",
 "keywords_en": ["5 a 8 termos de busca em inglês"]}
```

### 2. Candidatos + dados verificados (determinístico)

```bash
python -m tools journals recommend --perfil perfil.json --top 12 -o finalistas.json
```

Descoberta via OpenAlex /works → fontes; ∪ curados do vault; enriquece novos
ISSNs cache-first (isso já calcula a **consolidação Área 22**). Ranqueia por
consolidação (eixo dominante) + fit + APC + OA. **Nada é excluído por não
ser consolidado** — a rota do destaque qualitativo permanece; periódicos
fora da Porta A entram ranqueados e **sinalizados** (⚠️/❌).

### 3. Snapshots + fan-out Haiku (tempo de decisão, taxa, tipos aceitos)

```bash
python -m tools journals snapshot --finalistas finalistas.json
```

Para cada snapshot novo, lançar 1 subagente **Haiku** com o prompt-padrão
(dado não explícito → null; valor exige trecho LITERAL — validado por
substring). O orquestrador preenche `issn_l`/`url` deterministicamente.

```bash
python -m tools journals import-extraction --arquivo extracoes.json
python -m tools journals recommend --perfil perfil.json --top 12 -o finalistas.json  # re-rank (cache)
```

### 4. Julgamento de fit (modelo da sessão — NUNCA delegar a modelo barato)

Ler `finalistas.json` (que traz consolidação + evidência + Qualis legado).
Escolher 5-7 com justificativa. Gravar `julgamento.json`:

```json
{"aviso": "Chance de aceite é estimativa qualitativa; consolidação via SJR é proxy do CiteScore.",
 "shortlist": [{"issn_l": "...", "titulo": "...",
   "chance_estimada": "alta|média|baixa",
   "justificativa": "<fit temático e de tipo de estudo, citando dados de finalistas.json>",
   "trade_offs": "<ex.: consolidado Porta A Q1 caro vs SciELO-SP gratuito nacional>"}]}
```

Regras do julgamento:
- Priorizar **consolidados** (Porta A Q1/Q2 ou Porta B). Um periódico
  `sem_categoria_saude` (armadilha Educação/CS) só entra na shortlist se o
  valor editorial justificar a **rota do destaque qualitativo** — e isso
  deve ser dito explicitamente como aposta.
- Para `nao_por_scimago` (saúde só em Q3/Q4 pelo SJR), recomendar **conferir
  o CiteScore real no Scopus** antes de descartar — o proxy pode subestimar.
- Considerar a **regra dos 4 produtos** (a avaliação docente tem referência
  de ~4 produtos/quadriênio): não gastar uma vaga num periódico fora de porta.
- Qualis 2021-2024 é retrospectivo — **não** usar como critério de submissão.
- Citar só dados do JSON (nada de APC/percentil de memória).

### 5. Relatório e destino

```bash
python -m tools journals report --finalistas finalistas.json --julgamento julgamento.json -o <destino>
```

- Manuscrito de projeto → `~/PKM/Escrita/<Projeto>/pipeline/radar-qualis.md`.
- Semente → subseção `## Sugestões do Radar Qualis (gerado em AAAA-MM-DD)`
  no CORPO; **sem propriedade de frontmatter nova** (autoria é de Raphael).
- Pergunta avulsa → chat.

## Verificação manual que o tooling NÃO substitui

Antes de submeter, confirmar no **Scopus Sources** (scopus.com/sources) o
**CiteScore Percentile por categoria** (≥50% em categoria de saúde) ou, na
Porta B, o **h5 no Google Scholar Metrics** vs. o percentil 60 da área.
Percentis mudam todo ano (CiteScore atualiza em junho) — revalidar a cada
ciclo de submissão.

## Manutenção da base

- Qualis 2021-2024: nunca re-ingerir (descontinuado).
- Categorias Scimago + SJR: re-ingerir anualmente (junho).
- Coleção SciELO SP: lista estável; re-ingerir se mudar.
- APC/DOAJ/métricas: `enrich --all` quando `status` acusar vencidos.

## Regras de honestidade (invioláveis)

1. Todo dado no relatório traz fonte + data.
2. Dado ausente = "não disponível". Proibido estimar percentil, APC, ISSN.
3. Consolidação via SJR é **proxy** do CiteScore — sempre rotulada como tal;
   verdict negativo pela Porta A **não é definitivo**.
4. `import-extraction` rejeita trecho que não seja substring do snapshot.
5. Chance de aceite: "estimativa qualitativa, não probabilidade".
6. Periódico fora de porta é **sinalizado, nunca escondido**.
