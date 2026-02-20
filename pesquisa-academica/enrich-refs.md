---
name: enrich-refs
description: "Enriquece MDs individuais de referências com resumos estruturados gerados pelo Claude Code. Atualiza tanto os MDs quanto o research-brief."
status: ativa
version: 1.0
date: 2026-02-19
trigger: "/enrich-refs"
---

# Enrich Refs — Resumos Claude para Referências

## Propósito

Gerar resumos estruturados para cada referência extraída pelo pipeline.
Atualiza tanto o MD individual (`pipeline/refs/*.md`) quanto a entrada
correspondente no `research-brief.md`.

**Quando usar:** Após executar o pipeline (`python -m tools run`) e antes de iniciar a escrita.

**Tokens:** Usa os tokens do plano do autor (Claude Code interativo).


## Workflow

```
1. Ler o research-brief.md para contexto
2. Para cada MD em pipeline/refs/:
   a. Ler o MD completo (abstract + conteúdo se disponível)
   b. Gerar resumo estruturado
   c. Atualizar a seção "## Resumo Claude" do MD
   d. Atualizar a entrada correspondente no research-brief.md
3. Reportar quantos MDs foram enriquecidos
```


## Formato do Resumo Claude

```markdown
## Resumo Claude

**Achados principais:**
- [Achado 1 com dados quantitativos quando disponíveis]
- [Achado 2]

**Metodologia:** [tipo de estudo, amostra, período]

**Dados-chave:** [números específicos, métricas, resultados estatísticos]

**Limitações:** [limitações relevantes para o projeto]

**Relevância para o projeto:**
- **[Eixo 1]:** [como este paper contribui para este eixo]
- **[Eixo 2]:** [como este paper contribui para este eixo]
```


## Instruções para o Claude Code

Ao executar `/enrich-refs`:

1. **Ler o scope.yaml** do projeto para entender os eixos de pesquisa
2. **Ler o research-brief.md** para contexto
3. **Listar MDs** em `pipeline/refs/`
4. **Para cada MD:**
   - Ler o conteúdo completo
   - Se a seção "## Resumo Claude" já contém texto (não o placeholder), pular
   - Gerar resumo seguindo o formato acima
   - Substituir o placeholder por resumo real
   - Atualizar a entrada no research-brief.md
5. **Opções de execução:**
   - `/enrich-refs` → todos os MDs pendentes
   - `/enrich-refs Arquivo.md` → apenas um MD específico
   - `/enrich-refs --eixo "Conceitos"` → apenas refs de um eixo


## Exemplo de MD Enriquecido

```markdown
# The role of data science in healthcare advancements

**Link:** https://doi.org/10.1007/s11845-021-02730-z
**Autores:** Subrahmanya et al. | **Ano:** 2022 | **Journal:** Irish J Med Sci
**Grade:** GOLD | **Tier:** T1 | **Citações:** 142
**Eixos:** Conceitos fundamentais, Aplicações hospitalares
**Acesso:** Open Access | **PMID:** 34626326

## Resumo Claude

**Achados principais:**
- Revisão abrangente que mapeia 4 domínios de aplicação de data science em saúde: IoT, dados não-estruturados, ML para predição, NLP para prontuários
- 80% dos dados de saúde são não-estruturados (citando relatório IBM 2017)
- ROI médio de 3:1 em projetos de analytics hospitalares (citando McKinsey 2021)

**Metodologia:** Revisão narrativa, 87 artigos incluídos (2015-2021)

**Dados-chave:** 4 domínios identificados, 12 aplicações práticas categorizadas, 6 barreiras à adoção documentadas

**Limitações:** Revisão narrativa (não sistemática), predominância de estudos de países desenvolvidos, dados de ROI baseados em relatórios de mercado (T3)

**Relevância para o projeto:**
- **Conceitos fundamentais:** Excelente para introdução e taxonomia de big data em saúde
- **Aplicações hospitalares:** Lista aplicações concretas com exemplos de hospitais

---

## Abstract
[Abstract original]

## Conteúdo
[Full text]
```


## Notas

- O enriquecimento é idempotente: re-executar não sobrescreve resumos existentes
- Para forçar re-geração: apagar manualmente o conteúdo da seção "## Resumo Claude"
- O research-brief.md é atualizado junto com os MDs
- Resumos são concisos (200-400 palavras) e focados em utilidade para a escrita
