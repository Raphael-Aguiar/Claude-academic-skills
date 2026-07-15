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

Conduzir revisão de literatura como **produto final** (o artigo de revisão É o texto sendo produzido). Quatro subtipos, cada um mapeado a uma `--modality` do pipeline:

| Subtipo (`--modality`) | Uso | Framework |
|---|---|---|
| `revisao-integrativa` | Sintetizar pesquisa sobre um tópico | Whittemore & Knafl (2005) |
| `revisao-sistematica` | Síntese exaustiva com protocolo rigoroso | Cochrane + PRISMA 2020 |
| `revisao-escopo` | Mapear extensão e natureza da evidência | Arksey & O'Malley (2005); JBI (Peters et al. 2020) |
| `meta-revisao` | Umbrella review — sintetizar REVISÕES existentes | JBI umbrella review; PRIOR (Gates et al. 2022); qualidade via AMSTAR-2 |

**Princípio central (inviolável):** ferramentas de IA mapeiam o terreno, **nunca** geram a lista final de referências. Toda referência entra no texto só após verificação individual (ver skill `referencias-e-citacoes`).

- **Usar esta skill** quando a revisão é o produto: artigo de revisão, TCC/dissertação/tese.
- **Usar `pesquisa-academica`** quando a revisão é intermediária (capítulo, artigo original, material didático).

**Fundamentação metodológica:** `~/PKM/Escrita/Pesquisas sobre Pipeline/revisao-metodologica-tipos-de-revisao.md`

## Pipeline automatizado

Esta skill é orquestrada pelo pipeline em `~/bin/escrita-tooling/tools/` (doc completa: `tools/README.md`). Ele automatiza busca, deduplicação, verificação zero-trust, classificação, triagem e geração de `.bib`/relatório; as diferenças entre subtipos (profundidade, documentação, avaliação de qualidade) são configuradas pela modalidade.

```bash
python -m tools scope "Projeto" --init --modality <revisao-integrativa|revisao-sistematica|revisao-escopo|meta-revisao>
python -m tools descriptors "Projeto"   # sugere descritores MeSH/DeCS oficiais → curar e colar no scope.yaml
python -m tools run "Projeto" --modality <...>   # busca ≥3 bases, dedup, verificação, triagem por keywords
python -m tools screen-export "Projeto" # VOCÊ é o revisor 1: aplica os critérios I/E em prosa a cada ref
python -m tools screen-import "Projeto" # kappa de Cohen + divergências → depois: run --from-stage 5
python -m tools semantic "Projeto"      # opcional: re-ranqueamento + resgate por embeddings (Ollama local)
```

**Fatos do pipeline que não se inferem:**
- Bases default por modalidade: PubMed, OpenAlex, Semantic Scholar, Europe PMC, **BVS**.
- **BVS** (LILACS + SciELO + MEDLINE federados) exige `BVS_API_KEY` — chave gratuita em https://api.bvsalud.org.
- **Camada semântica** (`semantic_rerank: true` no scope.yaml): além de re-ranquear, faz **resgate semântico** — refs que as keywords não pegaram (sinônimos imprevistos) mas próximas da pergunta sobem para revisão humana em vez de serem descartadas.
- Após o pipeline, restam passos **manuais**: o revisor humano (Raphael, revisor 2) decide as divergências em `screening-report.md` e valida `validation-list.md`; a avaliação de qualidade dos incluídos (RoB 2/GRADE, MMAT/CASP, AMSTAR-2 conforme modalidade) é manual, com o framework indicado no audit-report. O `pipeline/prisma-flow.md` (PRISMA 2020/ScR/PRIOR) é gerado automaticamente.

## Critérios de qualidade de fontes (tiers)

| Tier | Tipo | Uso |
|---|---|---|
| 1 | Periódico revisado por pares indexado (PubMed, SciELO, IEEE, Scopus, WoS) | Preferencial |
| 2 | Fonte institucional de referência (Ministério da Saúde, DATASUS, IBGE, OMS) | Sim — com ressalva de tipo |
| 3 | Relatório técnico especializado (CETIC.br, HIMSS, consultorias) | Citar **como relatório técnico/de mercado, com data de acesso — nunca como evidência científica** |
| 4 | Notícia / blog / site corporativo / healthtech | Só como ilustração, nunca para sustentar dado |
| 5 | Inacessível / inexistente | Não |

**Domínios confiáveis (lista curada da auditoria):**
- **Tier 1:** pmc.ncbi.nlm.nih.gov, pubmed.ncbi.nlm.nih.gov, scielo.br/.org, ieeexplore.ieee.org, bmj.com, biomedcentral.com, mdpi.com, jmir.org, tandfonline.com, semanticscholar.org, journals.library.columbia.edu, jmphc.com.br, abccardiol.org, researchgate.net, arxiv.org (pré-print), fi-admin.bvsalud.org.
- **Tier 2 (institucionais BR/global):** saude.gov.br, datasus.gov.br, ans.gov.br, anvisa.gov.br, ibge.gov.br, fiocruz.br, ipea.gov.br, who.int, paho.org, himss.org, cetic.br (TIC Saúde), iess.org.br, cfm.org.br, sbis.org.br (informática em saúde), rnds-guia.saude.gov.br, ces.ibge.gov.br, pwc.com.br (pesquisas públicas).
- **Tier 3 (técnicos/mercado):** medicinasa.com.br, anahp.com.br, sindihospa.com.br, grandviewresearch.com, straitsresearch.com, statista.com.
- **Tier 4 (evitar como referência):** blogs corporativos, consultores individuais, YouTube, LinkedIn, sites de startups/healthtechs.

## Avaliação de relevância

Ao triar referências (inclusive as sugeridas por IA), pontuar por aderência ao escopo declarado: **2** = diretamente relevante; **1** = tangencial; **0** = off-topic (descartar). As listas de `relevance_keywords`/`exclusion_keywords` são **específicas de cada projeto** e vivem no `scope.yaml` — não há lista genérica reutilizável.

## Ferramentas de IA — risco empírico (auditoria de 288 refs, 2026-02-18)

Relatório completo: `~/PKM/Escrita/Livro Editora Atheneu/Deep Researches (Antigas)/relatorio-audit-deep-research.md`.

| Ferramenta | Melhor uso | Pior uso | Risco |
|---|---|---|---|
| **Perplexity** | Triagem de URLs em volume (76% T1) | Gerar lista de refs para uso direto | Baixa alucinação (URLs verificáveis), mas 59% off-topic |
| **scite.ai** | Verificar se citação é apoiada/contestada | Busca de tema / geração de refs | Off-topic graves (46%); sem URLs |
| **Google Gemini** | Mapear fontes institucionais BR (T2) | Referências acadêmicas diretas | Mistura blogs e periódicos; 36% T4 |
| **ChatGPT** | Identificar fontes-chave com contexto editorial | Qualquer uso sem verificação | Alta alucinação em números; zero URLs |
| **Claude** | Base bibliográfica estruturada (Vancouver; 8% off-topic) | Referências acadêmicas diretas | 51% T4 (blogs); verificar fonte primária para números |

**Sequência recomendada:** ChatGPT/Gemini (mapeamento de terreno) → PubMed/SciELO (busca primária) → CETIC.br/ANS/DATASUS (fontes nacionais) → scite.ai (verificação pontual de afirmação específica, nunca busca de tema) → Zotero (gestão).

## Registro e verificação de cada referência

Registrar cada referência em `notas_pesquisa.md` (nunca no texto canônico), com: autores, título, periódico, ano, **DOI**, URL, data de acesso, status de verificação (acessível / verificado / bloqueado / 404) e — crucial — o **dado utilizado** + confirmação de que o número/afirmação está mesmo no texto da fonte.

**Estabilidade de link (preferir nesta ordem):** DOI > URL canônica do artigo no periódico > URL de download/PDF > URL de site governamental (alta taxa de link rot). Re-verificar URLs perto da submissão.

A verificação individual antes de inserir (DOI resolve, autores conferem, conteúdo corrobora a afirmação, formato pelo SKILL.md do projeto) é responsabilidade da skill **`referencias-e-citacoes`** — seguir o protocolo zero-trust e a auditoria de cruzamento texto↔lista de lá.

## Referências desta skill

- Auditoria: `~/PKM/Escrita/Livro Editora Atheneu/Deep Researches (Antigas)/relatorio-audit-deep-research.md`
- Skill complementar: `~/bin/escrita-tooling/skills/referencias-e-citacoes/SKILL.md`
- Verificação de referências no pipeline: `python -m tools verify` / `verify-refs` (ver `tools/DECISOES.md` T1/T8).
