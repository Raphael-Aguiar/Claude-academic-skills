---
name: verify-claims
description: Verifica se afirmações do texto são suportadas pelas referências citadas. Camada 2 — revisão interativa com julgamento semântico.
usage: /verify-claims <arquivo.md> [--project "Nome do Projeto"]
status: ativa
---

# Skill: Verificação de Claims (/verify-claims)

## Propósito

Verificar se as afirmações (claims) no texto acadêmico são realmente suportadas pelas referências citadas. Combina verificação automatizada (Camada 1) com julgamento semântico interativo (Camada 2).

## Contexto

9 de 30 referências do Cap. 16 tinham autores fabricados. Se metadados estavam errados, dados citados no texto podem estar igualmente fabricados. Este sistema detecta:

- **Dados numéricos fabricados** — números inventados ou misturados de fontes
- **Erros matemáticos** — "96→26 dias = 65%" quando o cálculo real é 72,9%
- **Citações secundárias** — dado atribuído a [13] mas originário de outra fonte
- **Aproximações questionáveis** — "$469K" no texto vs "$400K" na referência


## Fluxo de execução

### Camada 1 — Verificação automatizada

```bash
source .venv/bin/activate
python -m tools verify-claims "<arquivo.md>" --project "<Projeto>"
```

Gera dois arquivos:
- `<arquivo>.claims-report.md` — relatório legível
- `<arquivo>.claims-data.json` — dados estruturados para Camada 2

### Camada 2 — Revisão interativa (este skill)

Após executar a Camada 1, o Claude Code:

1. **Ler** o `.claims-data.json`
2. **Priorizar** claims por risco: CRITICAL > HIGH > MATH_ERROR > MEDIUM
3. **Para cada claim de risco alto:**
   a. Ler o full text da ref (via Read se MD disponível em pipeline/refs/)
   b. Se ref OA sem full text extraído: usar WebFetch no DOI
   c. Avaliar semanticamente se o claim é suportado pela referência
   d. Emitir veredicto: `CONFIRMADO` / `DISCREPÂNCIA` / `FABRICADO`
4. **Para APPROXIMATE_MATCH:**
   a. Avaliar se a diferença é arredondamento legítimo ou fabricação
   b. Se a ref é uma review: verificar se cita fonte primária do dado
5. **Atualizar** o `.claims-data.json` com veredictos (`layer2_verdict`, `layer2_notes`)
6. **Regenerar** o `.claims-report.md` com veredictos da Camada 2
7. **Sugerir correções** para o texto onde necessário


## Status de verificação

| Status | Significado | Risco |
|--------|-------------|-------|
| `VERIFIED` | Dado encontrado, contexto confere | NONE |
| `APPROXIMATE_MATCH` | Valor similar (±30%) | LOW |
| `ENTITY_FOUND` | Entidade encontrada, contexto extraído | LOW-MEDIUM |
| `THEMATIC_MATCH` | Tópico do abstract é relevante | NONE |
| `NOT_FOUND_ABSTRACT` | Não encontrado, só abstract disponível | MEDIUM |
| `NOT_FOUND_FULLTEXT` | Não encontrado, full text verificado | HIGH |
| `NOT_FOUND_ALL_REFS` | Não encontrado em nenhuma ref | CRITICAL |
| `MATH_ERROR` | Inconsistência aritmética | HIGH |
| `UNVERIFIABLE` | Ref inacessível | UNKNOWN |


## Veredictos da Camada 2

| Veredicto | Significado | Ação |
|-----------|-------------|------|
| `CONFIRMADO` | Claim suportado pela referência | Nenhuma |
| `DISCREPÂNCIA` | Diferença entre texto e referência | Corrigir texto |
| `FABRICADO` | Dado não existe na referência | Remover ou substituir dado |
| `CITAÇÃO_SECUNDÁRIA` | Dado da ref primária, não da citada | Adicionar ref primária ou notar |
| `ARREDONDAMENTO` | Diferença é arredondamento legítimo | Opcionalmente ajustar |


## Limitações

- **Abstract-only**: maioria dos data points de refs paywall não são verificáveis
- **Reviews como fontes**: o número pode estar no corpo da review, não no abstract
- **Fontes web/legislação**: ~10 refs típicas não têm API — requerem WebFetch manual
- **Paráfrase legítima**: "$400K+" vs "$469K" é ambíguo — requer julgamento humano


## Exemplo de uso

```
Usuário: /verify-claims "Livro Editora Atheneu/capitulo16.md" --project "Livro Editora Atheneu"
```

O Claude Code irá:
1. Executar a Camada 1 automatizada
2. Analisar resultados HIGH/CRITICAL
3. Para cada um, buscar evidência na referência
4. Reportar veredictos e sugerir correções
