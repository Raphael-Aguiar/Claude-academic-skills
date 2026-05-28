# Extract Facts — Extração Preventiva de Fatos Verificados

## Propósito

Extrair fatos verificados das referências ANTES de escrever, criando um registro estruturado (.facts-registry.json) como "single source of truth". Camada preventiva que complementa o verify-claims (camada corretiva).

**Motivação:** O verify-claims detectou problemas reais no Cap. 16:
- "$469K" no texto vs "exceeding $400,000" na ref (valor possivelmente fabricado)
- "65%" derivado de cálculo que dá 72,9% (erro matemático)
- 9/30 referências com autores fabricados por LLMs

**Princípio:** É mais barato prevenir do que corrigir. Extrair dados verificados ANTES de escrever elimina esses erros na origem.


## Quando usar

- **Após** coletar referências (pesquisa-academica ou pipeline)
- **Antes** de escrever qualquer seção que cite dados das refs
- Etapa 1.5 do workflow em ~/bin/escrita-tooling/CLAUDE.md (entre Pesquisa e Rascunho)


## Pré-requisitos

- Referências já resolvidas (pipeline/refs/*.md ou acessíveis via PubMed/CrossRef)
- `source .venv/bin/activate`


---


## Fase 1 — Preparação (Python, automatizada)

```bash
python -m tools extract-facts "Projeto" --refs 11-17
```

O que faz:
1. Resolve conteúdo das refs (reutiliza cache de .refs-cache.json)
2. Extrai números automaticamente via regex (filtrando ruído: DOIs, anos, citation brackets)
3. Classifica cada número: percentagem, valor monetário, contagem, métrica
4. Gera `.facts-candidates.md` com candidatos para revisão

**Se .facts-pending.jsonl já existe:** avisa para rodar `facts-import` primeiro.

**Limitação conhecida:** A extração automática cobre apenas fatos quantitativos (~30% do total). Fatos qualitativos e conceituais requerem extração manual na Fase 2.


## Fase 2 — Extração (Claude, interativa)

### Protocolo: UMA ref por vez

Processar uma ref por vez para resiliência a compressão de contexto.

Para cada ref no `.facts-candidates.md`:

1. **Ler** os candidatos quantitativos daquela ref
2. **Ler** o `pipeline/refs/*.md` correspondente se necessário
3. **Aceitar, rejeitar ou ajustar** cada candidato quantitativo:
   - Aceitar: o número é relevante e está correto
   - Rejeitar: o número é irrelevante (page number, sample size sem contexto, etc.)
   - Ajustar: corrigir valor, unidade ou contexto
4. **Extrair fatos qualitativos/conceituais** adicionais (não capturados pela Camada 1)
5. **Append** ao `.facts-pending.jsonl` — um JSON por linha

### Formato JSONL

```jsonl
{"ref_number": 13, "fact_type": "quantitative", "statement": "Tampa General economizou 3000 min/semana com IA no CC", "exact_quote": "saving 3,000 minutes per week across 28 of its 52 operating rooms", "value": 3000, "unit": "minutes/week", "sections": ["16.3"], "tags": ["centro-cirurgico", "IA"]}
{"ref_number": 13, "fact_type": "qualitative", "statement": "Tampa General usou IA para otimizar agendamento cirúrgico", "exact_quote": "AI-driven scheduling optimization across surgical departments", "sections": ["16.3"], "tags": ["centro-cirurgico", "agendamento"]}
```

### Campos obrigatórios

| Campo | Tipo | Descrição |
|-------|------|-----------|
| ref_number | int | Número da referência |
| fact_type | str | "quantitative", "qualitative" ou "conceptual" |
| statement | str | Afirmação concisa em PT-BR |
| exact_quote | str | Citação EXATA do texto fonte (em inglês se fonte EN) |

### Campos opcionais

| Campo | Tipo | Quando usar |
|-------|------|-------------|
| value | float | Quantitativos — o número |
| unit | str | Quantitativos — unidade |
| sections | list[str] | Seção(ões) onde o fato será usado |
| tags | list[str] | Tags para busca |
| quote_context | str | +-200 chars ao redor da citação |
| derivation_formula | str | "percentage_reduction", "cagr", etc. |
| source_values | list[float] | Valores-fonte para derivados |
| stated_result | float | Valor afirmado para derivados |
| notes | str | Observações |

### Regras de status por tipo

| Tipo | Regra |
|------|-------|
| QUANTITATIVE | Número DEVE estar na fonte. Sem exceção. |
| QUALITATIVE | Citação exata preferida; contexto aceito se suporta o statement |
| CONCEPTUAL | Mesma regra que qualitativo |


### Fase 2b — Verificação de fontes web (Claude, interativa)

Para cada ref com ref_type = "web" ou "legislation":
1. Claude usa WebFetch na URL da ref
2. Se acessível: extrai fatos do conteúdo obtido → status VERIFIED_WEB
3. Se inacessível: status UNVERIFIABLE_SOURCE
4. Para fontes UNVERIFIABLE: registrar fatos, mas o writing guard exige confirmação manual do autor antes de usar no texto


## Fase 3 — Importação (Python, automatizada)

```bash
python -m tools facts-import "Projeto"
```

O que faz:
1. Backup automático do .facts-registry.json (se existir)
2. Processa .facts-pending.jsonl linha a linha (linhas inválidas: skip + log)
3. Para cada fato quantitativo: validação cruzada (o número existe na ref?)
4. Validação de citação: substring match (< 40 chars) ou fuzzy ≥ 0.93 (>= 40 chars)
5. Para derivados: calcula resultado e compara com valor afirmado
6. Importa com dedup + status adequado
7. Limpa .facts-pending.jsonl

**Output:** "32 fatos importados (28 verified, 3 context_supported, 1 rejected)"


## Fase 4 — Relatório (Python)

```bash
# Brief para escrita (conciso, ~2-5 KB)
python -m tools facts-status "Projeto" --section 16.3

# Relatório completo
python -m tools facts-report "Projeto"

# Crosscheck com verify-claims
python -m tools facts-crosscheck "Projeto"
```


---


## Writing Guard — Protocolo durante escrita

Ao escrever uma seção que cita dados:

1. **Consultar** `facts-status --section X` para ver fatos disponíveis
2. **Usar apenas dados registrados** no Facts Registry
3. **Se um dado não está no registry:** extrair primeiro com extract-facts
4. **Nunca citar de memória** — sempre verificar o registry

### Regras por tipo de fato

| Tipo | Regra no texto |
|------|----------------|
| VERIFIED | Pode citar livremente com a referência indicada |
| CONTEXT_SUPPORTED | Pode parafrasear; não usar como citação direta |
| DERIVED | Citar usando o calculated_result (não o stated_result se diferente) |
| VERIFIED_WEB | Pode citar; incluir data de acesso |
| UNVERIFIABLE | Citar com ressalva ("conforme [fonte], embora não verificável diretamente...") OU solicitar confirmação do autor |
| STALE | NÃO usar — re-extrair primeiro |
| REJECTED | NÃO usar — dado não suportado pela ref |


---


## Artefatos

| Arquivo | Descrição | Gerado por |
|---------|-----------|-----------|
| .facts-candidates.md | Candidatos quantitativos (legível) | extract-facts (Fase 1) |
| .facts-pending.jsonl | Fatos aguardando importação (JSONL) | Claude (Fase 2) |
| .facts-registry.json | Registry validado (JSON) | facts-import (Fase 3) |
| .facts-registry.json.bak | Backup automático | facts-import |

Todos ficam na pasta do projeto (ex: `Livro Editora Atheneu/`).


## Referências

- verify-claims (camada corretiva): `~/bin/escrita-tooling/skills/pesquisa-academica/verify-claims.md`
- Código: `~/bin/escrita-tooling/tools/extract_facts.py`
- CLI: `python -m tools extract-facts|facts-import|facts-status|facts-report|facts-crosscheck`
