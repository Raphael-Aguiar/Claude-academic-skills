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

Montar uma **base de referências verificadas** como ponto de partida para desenvolver um texto acadêmico. O produto é **intermediário** — o texto final será outra coisa (capítulo, artigo, relatório). O workflow é sistemático (bases e critérios explícitos), auditável (reproduzível), automatizado (`python -m tools`) e **resistente a alucinações** (zero-trust: toda referência verificada antes de uso).

**Premissa fundamental (validada pela auditoria de 288 refs, Cap. 16):** ferramentas de IA generativa servem para *mapeamento conceitual* e *descoberta de fontes*, **nunca** como fonte final de referências.

- **Usar esta skill** para capítulo de livro-texto, artigo original (introdução/discussão), material didático, relatório técnico.
- **Usar `revisao-literatura`** quando a revisão É o produto final (integrativa, sistemática, de escopo).

## Pipeline automatizado

Orquestrada pelo pipeline em `~/bin/escrita-tooling/tools/` (doc completa: `tools/README.md`). O pipeline automatiza busca, triagem, verificação e outputs; a delimitação humana e a triagem semântica (revisor LLM) permanecem interativas.

```bash
python -m tools scope "Projeto" --init --modality pesquisa-base   # cria scope.yaml
# editar scope.yaml: keywords, research_axes, exclusion_keywords, etc.
python -m tools seed "Projeto"          # (opcional) extrai seed refs de textos existentes
python -m tools run "Projeto"           # pipeline completo (10 etapas + extração + brief)
python -m tools status "Projeto"        # verificar resultado
# revisar validation-list.md (~5-10 min); obter não-OA de to-obtain.md
python -m tools extract-manual "Projeto"  # processar PDFs manuais
# enriquecer refs com resumos: /enrich-refs (Claude Code interativo)
```

**Perguntas que o scope.yaml deve coletar ao iniciar um projeto:** (1) pergunta de pesquisa; (2) eixos de pesquisa (3-8 subtemas/dimensões); (3) keywords por eixo + sinônimos; (4) exclusões explícitas (temas fora do escopo); (5) acesso institucional (CAPES/ProBE/assinatura — artigos indispensáveis não-OA serão listados para providenciar); (6) meta de refs (default 40 para pesquisa-base).

## Busca em três trilhas (A primária, B institucional, C só descoberta)

### Trilha A — bases indexadas (PRINCIPAL)

| Base | O que encontrar | Acesso |
|---|---|---|
| **PubMed / PMC** | Artigos biomédicos revisados por pares (MeSH) | Gratuito |
| **SciELO Brasil** | Artigos brasileiros e latino-americanos (busca em PT) | Gratuito |
| **Google Scholar** | Cobertura ampla (periódicos + teses + grey lit) | Gratuito (bloqueia scraping) |
| **Scopus / Web of Science** | Métricas de impacto, indexação | Via CAPES (institucional) |
| **LILACS** | Literatura latino-americana em saúde (descritores DeCS) | Gratuito (BVS) |
| **Semantic Scholar** | Busca semântica; citation context; API gratuita | Gratuito + API |

- **Preferência de busca do Claude** nas bases abertas: PubMed > PMC > SciELO > Scholar > web geral.
- Para cada resultado, extrair já: **DOI** (âncora mais estável), PMID, título completo, autores, periódico/vol/issue/pág/ano e **URL canônica** (nunca URL de download/PDF).

### Trilha B — fontes institucionais e literatura cinza

| Tipo | Fontes |
|---|---|
| Governo BR | DATASUS, ANS, RNDS, Ministério da Saúde (portarias, notas técnicas, dados) |
| Legislação | Planalto, Câmara, Senado, LexML |
| Organismos internacionais | OMS, OPAS, OECD, World Bank (relatórios, guidelines) |
| Associações setoriais | ANAHP, CFM, SBIS, HIMSS |
| Dados de pesquisa BR | CETIC.br (TIC Saúde), IBGE, IPEA |
| Relatórios de mercado | PwC, Deloitte, McKinsey, Straits, Grand View |

- **Consultorias (PwC, McKinsey…) são Tier 3:** citar como "relatório de mercado/técnico", nunca como evidência científica primária.
- **URLs governamentais BR têm alta taxa de link rot:** registrar URL completa + data de acesso, copiar o trecho relevante no `notas_pesquisa.md`, buscar alternativa (LexML para legislação; BVS para Ministério da Saúde).

### Trilha C — IA assistida (SOMENTE DESCOBERTA)

**Regra fundamental:** nenhuma referência da Trilha C entra no texto sem passar pela Trilha A ou B para verificação. Para o que a IA erra (com evidência), ver §Anti-padrões.

| A IA serve bem para | Ferramenta |
|---|---|
| Mapear o ecossistema de um tema (subtemas, atores, terminologia) | Gemini, ChatGPT |
| Descobrir fontes institucionais BR | Gemini |
| Curadoria editorial (quais fontes importam) | ChatGPT |
| Triagem em volume de URLs | Perplexity |
| Verificar se citação é apoiada/contestada | scite.ai |

## Triagem — três scores independentes + classificação composta

**Tier de qualidade (fonte):**

| Tier | Definição | Exemplos | Citável? |
|---|---|---|---|
| **T1** | Periódico revisado por pares, indexado | PubMed, SciELO, Scopus, IEEE | Sim, sem ressalva |
| **T2** | Fonte institucional de referência | OMS, DATASUS, ANS, CETIC.br, HIMSS | Sim, sem ressalva |
| **T3** | Relatório técnico/especializado | PwC, McKinsey, ANAHP | Sim, como "relatório técnico" com nota |
| **T4** | Notícia, blog, site corporativo | healthtechs, YouTube, sites pessoais | **Não** — só como pista para a fonte primária |

**Relevância:** 2 = diretamente relevante ao escopo; 1 = tangencial; 0 = off-topic.
**Acessibilidade:** acessível (HTTP 200/navegador) · restrito (paywall, existe — via CAPES) · quebrado (404/timeout) · sem URL (exige busca manual).

**Classificação composta:**

| Classe | Critério | Ação |
|---|---|---|
| 🥇 **Gold** | T1/T2 + relevância 2 + acessível | Usar prioritariamente |
| 🥈 **Silver** | T1/T2 + relev. 1 + acessível, OU T3 + relev. 2 + acessível | Usar se necessário; contextualizar |
| 🥉 **Bronze** | T3 + relev. 1, OU qualquer tier restrito | Reserva; buscar alternativa melhor |
| ❌ **Descartar** | Relevância 0, OU T4, OU sem URL inverificável, OU quebrado | Não usar |

**Meta de composição:** T1 ≥ 50%, T2 15-25%, T3 10-20%, T4 0%. Se a distribuição final tiver < 50% de T1, voltar à Trilha A e buscar mais artigos indexados.

## Verificação individual

Rodar o **Checklist 3.1** da skill `referencias-e-citacoes` para cada referência que passou pela triagem (URL/DOI acessível; conteúdo corrobora a afirmação; formato no estilo do projeto; procedência em `notas_pesquisa.md`; autores conferidos na fonte primária; metadados — vol/issue/pág/ano). Protocolo para refs sugeridas por IA: seção 3.2 da mesma skill.

**Dados quantitativos (verificação reforçada):** para toda afirmação com número específico ("redução de 40%", "mercado de US$ 50 bi"), acessar a fonte, **localizar o número exato no texto** (Ctrl+F) e registrar "dado X confirmado em [fonte], parágrafo/página Y". Se o número não estiver lá, descartar a afirmação ou achar fonte que o contenha. *(Caso real: "80% dos dados clínicos são não estruturados" é atribuído a Dash et al. 2019, que não contém o número.)*

## Contestação (scite.ai)

O scite.ai **não é buscador** (auditoria: 45,8% off-topic quando usado assim). Uso correto: inserir DOI/título exato de um artigo → ver o *citation context* (supporting/contrasting/mentioning) → se > 20% forem *contrasting*, investigar as objeções antes de citar. Usar para afirmações **centrais** ou dados surpreendentes. Alternativas gratuitas: Google Scholar "Citado por", Semantic Scholar Citation Context (API), Connected Papers, PubMed Related Articles.

## Registro em `notas_pesquisa.md`

Nunca no texto canônico. Para cada referência verificada:

```markdown
### Ref [N] — Sobrenome et al. (Ano)
- Título / Periódico v.X n.Y p.Z ano / DOI / PMID / URL canônica
- Tier: T1|T2|T3 · Relevância: 2|1 · Classificação: Gold|Silver|Bronze
- Data de verificação
- Dados sustentados: [afirmações que esta ref sustenta]
- Verificação de dados quantitativos: [número X confirmado no parágrafo Y / não encontrado]
- Origem da descoberta: [PubMed / SciELO / Gemini(descoberta) / ...]
```

Manter no topo do arquivo uma tabela-resumo: `# | Autor(es) | Ano | Tier | Relev. | Class. | DOI | Status`.

## Modos de operação por tipo de projeto

O rigor varia conforme a produção:

| Aspecto | Capítulo livro-texto | Artigo/revisão | Relatório/aula |
|---|---|---|---|
| Trilha A (indexadas) | Obrigatória, ≥50% | Obrigatória (PRISMA se sistemática) | Recomendada (refs-chave) |
| Trilha B (institucional) | Obrigatória (BR + intl) | Conforme tema | Obrigatória (fontes primárias) |
| Trilha C (IA) | Opcional (mapeamento) | Mapeamento; documentar no método | Livre + verificação básica |
| Triagem | Gold + Silver | Gold (Silver c/ justificativa) | Gold + Silver + Bronze |
| Verificação | Checklist completo | Completo + cruzada | Simplificado (URL+autores+conteúdo) |
| scite.ai | Afirmações centrais | Obrigatória p/ quantitativos | Opcional |
| Meta de refs | 15-30 | 30-100+ | 5-15 |
| % T1 mínimo | 50% | 80% | 30% |

## Anti-padrões (validados pela auditoria de 288 refs, Cap. 16 Atheneu)

| Anti-padrão | Consequência / evidência |
|---|---|
| Usar deep research para **gerar a lista final de referências** | 49,8% off-topic, 13,5% inverificáveis |
| Inserir dados quantitativos sem URL/DOI verificável | Inverificável (ChatGPT "HC-SP −40%", Gemini "R$ 2 mi") |
| Citar blogs/sites corporativos como referência acadêmica | Baixo rigor (Gemini: 35,7% Tier 4) |
| Usar scite.ai como buscador de tema | 45,8% off-topic (resíduos hospitalares, geriatria) |
| Não verificar URL/DOI antes de inserir | Link rot, 404, URLs dinâmicas |
| Confiar em metadados bibliográficos fornecidos por IA | Autores/DOIs fabricados (caso Aldoseri — fabricação completa) |
| Atribuir dados operacionais a documentos normativos | Decreto não contém estatística de uso (Decreto 12.560) |
| Repetir estatística amplamente citada sem rastrear a fonte primária | Dado sem fonte real ("80% não estruturados", Dash et al. não contém) |

## Extract Facts — camada preventiva

Após coletar referências e **antes de escrever**, extrair fatos verificados das refs para o Facts Registry (previne dados fabricados e derivações erradas). Skill: `~/bin/escrita-tooling/skills/pesquisa-academica/extract-facts.md`.

```bash
python -m tools extract-facts "Projeto" --refs 11-17   # extrair candidatos → Claude escreve .facts-pending.jsonl
python -m tools facts-import "Projeto"                  # validar e importar
python -m tools facts-status "Projeto" --section 16.3   # brief para escrita
```

## Triagem semântica e descritores (Fase 2, 2026-07-10)

- **Descritores MeSH/DeCS:** `python -m tools descriptors "Projeto"` sugere descritores oficiais (API MeSH da NLM) → `pipeline/descriptors-suggested.md` com bloco YAML pronto. DeCS aceita o rótulo MeSH em inglês no campo `mh:` da BVS; traduções em decs.bvsalud.org.
- **Triagem semântica (dupla triagem):** critérios de inclusão/exclusão em prosa aplicados por revisor LLM — `screen-export` → Claude julga cada ref → `screen-import` (kappa de Cohen keyword×LLM + divergências em `screening-report.md`) → `run --from-stage 5`. Divergência nunca descarta silenciosamente; decide o revisor humano.
- **PRISMA:** `python -m tools prisma "Projeto"` (automático na etapa 9 para revisões).

## Roadmap (aberto)

Integração com o Index (acervo na VPS) para reusar materiais já classificados; template de fichamento por artigo; integração Zotero via Better BibTeX.
