---
name: referencias-e-citacoes
description: Gestão, formatação e verificação de referências bibliográficas em múltiplos estilos. Usar ao inserir, organizar, formatar ou auditar referências em qualquer projeto de escrita.
status: ativa (seção de verificação operacional; demais seções em desenvolvimento)
---

# Referências e Citações

## Propósito

Automatizar ao máximo o trabalho de gestão de referências — a tarefa que o autor mais detesta fazer manualmente. Cobrir desde a inserção inline até a geração da lista final formatada, incluindo verificação de integridade.


## 1. Estilos de citação suportados

- **Vancouver (ICMJE):** Padrão para publicações médicas. Numeração sequencial [1], [2], etc.
- **ABNT:** Padrão brasileiro. Autor-data (Sobrenome, Ano).
- **APA:** Padrão em ciências sociais. Autor-data (Sobrenome, ano).
- Outros conforme demanda do projeto.

Cada projeto define seu estilo no respectivo `SKILL.md`. Sempre verificar antes de inserir referências.


## 2. Inserção e formatação

### Inserção inline
- Vancouver: numeração sequencial conforme ordem de aparição no texto
- ABNT/APA: formato autor-data

### Lista de referências
- Formatar conforme estilo definido no projeto
- Periódicos, livros, Internet e legislação têm formatos distintos (consultar Manual do projeto)

### Verificação de consistência
- Toda referência citada no texto deve constar da lista final
- Toda referência da lista deve estar citada no texto
- Numeração sequencial sem lacunas (Vancouver)


## 3. Verificação e monitoramento de referências

**Esta seção é operacional** — usar como checklist ao inserir referências e ao auditar referências existentes.

### 3.1 Checklist por referência (executar ao inserir cada nova referência)

Antes de inserir qualquer referência no texto canônico, verificar:

| # | Verificação | Como fazer | Obrigatório? |
|---|---|---|---|
| 1 | **URL/DOI acessível?** | WebFetch na URL ou `https://doi.org/[DOI]` | Sim (para fontes Internet) |
| 2 | **Conteúdo corrobora a afirmação?** | Ler a fonte e confirmar que os dados citados estão presentes | Sim |
| 3 | **Formato correto?** | Conferir com o estilo definido no SKILL.md do projeto | Sim |
| 4 | **Procedência documentada?** | Referência registrada em `notas_pesquisa.md` com URL, DOI e data de verificação | Sim |
| 5 | **Autores verificados?** | Confirmar que os autores listados são os autores reais do artigo/documento | Sim |
| 6 | **Metadados conferidos?** | Volume, issue, páginas, ano — conferir com a fonte primária | Sim |

### 3.2 Cuidados especiais com referências de IA

Referências sugeridas por modelos de linguagem (Perplexity, scite.ai, ChatGPT, Claude) **DEVEM ser verificadas individualmente** antes de inserção no texto. Riscos conhecidos e documentados:

- **Autores fabricados** — nomes plausíveis mas inexistentes no periódico citado (caso Aldoseri et al. [7], Cap. 16)
- **Metadados misturados** — título de um artigo combinado com volume/páginas de outro
- **DOIs inventados** — DOIs com formato correto mas que retornam 404
- **Periódicos errados** — artigo atribuído ao periódico errado
- **Dados quantitativos sem fonte** — números específicos sem URL (ChatGPT: "HC-SP reduziu 40% tempo de auditoria" — inverificável)
- **Refs off-topic** — scite.ai gerou 45,8% de refs off-topic sobre resíduos hospitalares e geriatria para um capítulo sobre Big Data/IA em gestão

**Protocolo obrigatório para referências de IA:**
1. Verificar existência do artigo via DOI ou busca no site do periódico
2. Conferir autores, título, volume, páginas e ano na fonte primária
3. Para dados quantitativos específicos: localizar o número na fonte — não confiar que está lá
4. Se qualquer dado não conferir, descartar e buscar a referência correta manualmente
5. Documentar no `notas_pesquisa.md` que a referência foi verificada

**Avaliação empírica das ferramentas** (auditoria de 288 refs / 5 ferramentas, 2026-02-18):

| Ferramenta | % off-topic | URLs fornecidas | Risco de alucinação |
|---|---|---|---|
| Perplexity | 59% | 100% (verificável) | Baixo |
| scite.ai | 46% | 0% (inverificável) | Médio |
| Google Gemini | 21% | 100% (verificável) | Médio |
| ChatGPT | 0% | 0% (inverificável) | Alto |
| Claude | 8% | 97% (verificável) | Médio-baixo |

→ Relatório completo: `~/PKM/Escrita/Livro Editora Atheneu/relatorio-audit-deep-research.md`
→ Pipeline recomendado: `~/bin/escrita-tooling/skills/revisao-literatura/SKILL.md`

### 3.3 Protocolo de prevenção (integrado ao workflow de escrita)

**Ao pesquisar (etapa 1 do workflow):**
- Registrar em `notas_pesquisa.md`: URL de acesso, DOI (quando aplicável), data de verificação
- Para periódicos: anotar DOI sempre que disponível (DOIs raramente quebram)
- Para sites institucionais: anotar URL completa + capturar texto relevante (sites governamentais mudam URLs frequentemente)

**Ao inserir referência no texto (etapa 3 do workflow):**
- Executar checklist 3.1 completo
- Incluir URL ou DOI mesmo para artigos de periódico (facilita verificação futura)
- Para legislação: separar referência legal (decreto/portaria) de dados quantitativos (relatórios operacionais)

**Ao finalizar seção (entre etapas 3 e 4 do workflow):**
- Verificar consistência: todas as referências citadas estão na lista?
- Verificar numeração sequencial (Vancouver)
- Rodar checklist 3.1 para referências que foram inseridas sem verificação completa

### 3.4 Monitoramento periódico

**Antes de submeter o capítulo/artigo final:**
- Re-verificar todos os links (URLs podem quebrar em semanas)
- Priorizar DOIs como âncora estável (DOIs > URLs de sites)
- Para URLs governamentais (DATASUS, ANS, Planalto): verificar com atenção redobrada

**Classificação de risco de link rot:**
| Risco | Tipo de fonte | Ação |
|---|---|---|
| Baixo | Periódicos via DOI | Manter DOI; URL é backup |
| Médio | Sites de consultorias e organizações (HIMSS, Straits Research) | Verificar antes de submeter |
| Alto | Sites governamentais brasileiros (.gov.br) | Verificar antes de submeter; manter cópia do conteúdo relevante no notas_pesquisa.md |
| Muito alto | URLs diretas de PDF (download links) em periódicos | Evitar; usar URL canônica do artigo |
| Muito alto | URLs dinâmicas, páginas de busca | Evitar; buscar URL permanente |

**Evidência empírica (auditoria Cap. 16, 2026-02-18):** dos 217 links verificados, 71,4% acessíveis, 8,8% bloqueados por bot (não implica inexistência), 0,9% 404, 3,7% timeout. URLs de download de PDF foram as únicas 404 reais (2 casos).


## 4. Verificação de claims (afirmações vs. referências)

**Status:** Operacional (Camada 1 automatizada + Camada 2 interativa)

### O que é

Sistema que verifica se as afirmações no texto são realmente suportadas pelas referências citadas. Motivado pela descoberta de que 9/30 referências do Cap. 16 tinham autores fabricados — se metadados estavam errados, dados citados podem estar igualmente fabricados.

### Tipos de verificação

| Tipo | Descrição | Método |
|------|-----------|--------|
| **A — Quantitativo** | Números, percentagens, valores monetários | Busca numérica com variantes PT↔EN |
| **B — Qualitativo** | Métodos, modelos, instituições | Entity matching + contexto |
| **C — Conceitual** | Frameworks, conceitos gerais | Sobreposição temática |

### Uso

```bash
# Camada 1 — verificação automatizada
source .venv/bin/activate
python -m tools verify-claims "Projeto/capitulo.md" --project "Projeto"

# Camada 2 — revisão interativa via skill
/verify-claims "Projeto/capitulo.md" --project "Projeto"
```

### Outputs

- `<arquivo>.claims-report.md` — relatório legível com claims agrupados por risco
- `<arquivo>.claims-data.json` — dados estruturados para atualização pela Camada 2

### Detalhes técnicos

- Hierarquia de resolução: pipeline/refs → PMC full text → PubMed abstract → CrossRef abstract
- Cache em `.refs-cache.json` (reutilizado entre execuções)
- Variantes numéricas PT↔EN (vírgula decimal, mil/billion, moeda)
- Detecção de erros matemáticos (valores absolutos + percentagem derivada)
- Skill interativa: `~/bin/escrita-tooling/skills/pesquisa-academica/verify-claims.md`


## 4.5 Facts Registry (camada preventiva)

**Status:** Operacional

### Relação entre as camadas

| Camada | Ferramenta | Quando | O que faz |
|--------|-----------|--------|-----------|
| **Preventiva** | extract-facts + Facts Registry | ANTES de escrever | Extrai fatos verificados das refs → `.facts-registry.json` |
| **Corretiva** | verify-claims | DEPOIS de escrever | Verifica se afirmações do texto são suportadas pelas refs → `.claims-data.json` |

### facts-crosscheck: ponte entre as camadas

O `facts-crosscheck` conecta as duas camadas:
```bash
python -m tools facts-crosscheck "Projeto"
```

Cruza os dados quantitativos do `.claims-data.json` (output do verify-claims) com o `.facts-registry.json`. Identifica dados usados no texto que NÃO foram registrados previamente no Facts Registry.

### Quando usar cada ferramenta

| Situação | Ferramenta |
|----------|-----------|
| Vou começar a escrever uma seção | `extract-facts` → registrar dados antes |
| Terminei de escrever, quero verificar | `verify-claims` → verificar depois |
| Quero ver se todos os dados usados estão registrados | `facts-crosscheck` → cruzar |
| Vou citar um número no texto | Consultar `facts-status --section X` |

### Referência

Skill completa: `~/bin/escrita-tooling/skills/pesquisa-academica/extract-facts.md`


## 5. Formato de armazenamento — .bib + Pandoc

### Decisão (2026-02-18)

O formato de armazenamento é **.bib (BibTeX)**, gerado automaticamente pelo pipeline como subproduto da verificação via CrossRef.

**Abordagem em camadas (não excludentes):**

| Camada | O que | Status |
|---|---|---|
| **Camada 1 — Output do pipeline** | `.bib` por projeto, gerado automaticamente pela etapa 9 do pipeline. Pandoc + CSL para ABNT/Vancouver. | **Implementado (MVP)** |
| **Camada 2 — Rastreamento no Index** | Extensão da tabela `contextos` do `acervo.db` para registrar DOI/PMID. | Planejado |
| **Camada 3 — Zotero + Better BibTeX** | Importar .bib dos projetos para Zotero se/quando necessário. | Futuro/opcional |

### Geração de .bib

O pipeline gera `refs.bib` automaticamente na etapa 9 (`s09_synthesize.py`). Cada referência verificada contém metadados completos (título, autores, ano, DOI, journal, volume, issue, páginas) obtidos do CrossRef.

```bash
# Gerar .bib do projeto
python -m tools export "Projeto" --format bib
# Output: ~/PKM/Escrita/Projeto/pipeline/refs.bib
```

### Uso com Pandoc + CSL

Para converter Markdown com citações para Word/PDF com referências formatadas:

```bash
# Vancouver (padrão médico)
pandoc capitulo16.md --citeproc --bibliography pipeline/refs.bib \
  --csl vancouver.csl -o capitulo16.docx

# ABNT
pandoc artigo.md --citeproc --bibliography pipeline/refs.bib \
  --csl abnt.csl -o artigo.docx
```

**CSL files:** baixar de https://www.zotero.org/styles

### Verificação automatizada

O pipeline inclui verificação na etapa 10 (`s10_validate.py`):
- Re-verificar DOIs e URLs das referências finais
- Checar consistência das grades
- Relatório de problemas encontrados

```bash
python -m tools run "Projeto" --from-stage 10
```


## 7. Auditoria de cruzamento texto↔lista (extração de citações in-text)

**Status:** Operacional — método obrigatório a partir de 2026-04-07.

### Contexto e motivação

Em abril de 2026, ao auditar a tese de doutorado de Renato Rodrigues da Silva (~118 referências), dois agentes independentes — o original e um revisor lançado em paralelo — falharam em capturar a mesma citação real (`Souza Filho & Struchiner, 2021, p. 88`). Ambos haviam usado, na prática, a mesma estratégia (extração baseada em padrões `(Autor, ano)`), e portanto erraram da mesma forma. Dois agentes "independentes" que convergem para a mesma técnica de extração **não somam confiança — produzem ilusão de confiança**.

### Modos de falha conhecidos da extração de citações

Não confiar em regex `(Autor, ano)` como método primário. Os seguintes formatos passam batido por essa abordagem:

| Padrão | Exemplo | Por que falha |
|---|---|---|
| Citação narrativa | `Souza Filho & Struchiner (2021, p. 88) defendem que...` | Parêntese contém só o ano, não o autor |
| Sobrenome composto | "Souza Filho", "da Silva", "de Castro Barbosa" | Regex `[A-Z][a-z]+\s*&` falha pelo espaço |
| Ampersand escapado | `\u0026` no JSON do Google Docs | Não casa com `&` literal |
| Verbo + autor | `segundo Fulano (2020)`, `conforme`, `argumenta`, `propõe` | Padrão narrativo, não parentético |
| Em legendas de figuras/tabelas/quadros | `Fonte: Adaptado de Autor (2019)` | Frequentemente fora do fluxo principal de texto |
| Em notas de rodapé | — | Mesmo problema |
| Em citações diretas (block quotes) | citação de citação | Aninhamento confunde parser |
| Texto com typo adjacente | `"defendemdefende"` | Ruído tipográfico faz heurística pular o trecho |

### Método obrigatório para auditoria de cruzamento

Quando a tarefa for verificar correspondência texto↔lista de referências em um documento longo:

1. **Não usar regex como método primário.** Regex serve apenas como rede de segurança secundária.
2. **Varredura linear em chunks pequenos** (5-10 mil caracteres por vez), do início ao fim do documento, sem pular nenhum trecho. Para cada chunk, enumerar TODA menção a referência em qualquer formato.
3. **Dois métodos divergentes**, não dois agentes com a mesma estratégia:
   - **Método 1**: leitura linear humana/semântica (o agente lê e identifica)
   - **Método 2**: grep agressivo por padrões variados (`\(\d{4}\)`, `, \d{4}\)`, `\\u0026`, ` et al\.`, verbos de citação seguidos de ano, etc.)
   - Discrepâncias entre os dois métodos = sinal a investigar manualmente.
4. **Usar caso-teste de calibração.** Antes de aceitar a extração como confiável, escolher uma citação conhecidamente difícil (idealmente uma citação narrativa com sobrenome composto e `&`) e verificar se está no resultado. Se não está, refazer com chunks menores.
5. **Para documentos no Google Docs**, lembrar que o dump JSON contém ampersands como `\u0026` — o grep precisa cobrir as duas formas.
6. **Lista de referências final**: extrair também via leitura linear, não por regex de cabeçalho.

### Anti-padrões a evitar

- ❌ Lançar dois agentes "independentes" sem especificar **estratégias diferentes**. Se ambos forem livres, ambos vão convergir para regex, e você terá duas extrações com os mesmos pontos cegos.
- ❌ Confiar no número agregado de citações extraídas como medida de cobertura ("184 citações encontradas" não significa "todas as citações encontradas").
- ❌ Tratar a Parte A (cruzamento) como tarefa "fácil/determinística". Ela é determinística *se a extração for completa*, e a extração é o passo difícil.
- ❌ Reportar os achados de cruzamento sem incluir o **trecho exato** de cada citação suspeita — sem o trecho, o avaliador não consegue confirmar e você não tem como saber se é falso positivo.

### Anti-padrões em auditoria de metadados (Parte B)

Aprendidos na mesma auditoria:

- ❌ Confiar apenas em CrossRef API. CrossRef não indexa DOIs DataCite/arXiv (`10.48550/arXiv.*`), e tem comportamento inconsistente com DOIs contendo caracteres especiais. **Sempre testar também via `https://doi.org/<doi>` direto** antes de marcar um DOI como quebrado.
- ❌ Tratar divergência de ano CrossRef vs lista como erro automático. Periódicos têm "online first year" diferente de "print issue year"; isso é um *falso positivo* clássico. Investigar antes de reportar.
- ❌ Pular referências sem DOI ("não auditáveis"). Livros (Google Books), normas técnicas (sites das entidades), teses (repositórios institucionais), legislação (Planalto), relatórios governamentais (sites .gov.br) **são** verificáveis via WebFetch — só não automaticamente via API. Auditar manualmente em vez de descartar.

### Anti-padrão crítico de apresentação: truncamento de referências em tabelas

Aprendido em 2026-04-07, durante a auditoria da tese de Renato Rodrigues da Silva.

**Problema observado.** Ao reportar 118 referências em formato tabular, o agente truncou cada entrada em ~100 caracteres ("Referência (truncada)"). Para a ref #14 (Batko & Ślęzak 2022), o corte caiu *no meio do DOI*, exibindo `https://doi.org/10.1186/s40537-02` — o que parece um DOI quebrado, mas na verdade é o DOI completo `10.1186/s40537-021-00553-4` cortado pela formatação. O leitor (membro de banca) lê a string como se fosse o DOI completo e conclui "DOI inválido" — gerando falso positivo de problema.

**Regra absoluta.** NUNCA truncar uma referência de modo que o corte caia dentro de um DOI, URL, ISBN, ISSN ou qualquer identificador único. Estes não podem ser exibidos parcialmente — ou aparecem inteiros, ou não aparecem.

**Como apresentar referências em tabela:**

- ✅ **Opção 1 — referência inteira sem corte.** Tabela larga, uma referência por linha, sem limite de caracteres. Sacrifica densidade visual mas preserva integridade.
- ✅ **Opção 2 — corte no nome do autor + ano + título curto, com DOI em coluna SEPARADA.** Exemplo: `| # | Autor (ano) | Título (até 60 chars) | DOI completo | Status |`. O DOI tem sua própria coluna e nunca é truncado.
- ✅ **Opção 3 — chave canônica + número da ref.** Exibir só `[14] Batko & Ślęzak 2022` e ter a referência completa em apêndice numerado consultável.
- ❌ **NUNCA**: cortar a referência inteira (incluindo DOI/URL) em N caracteres e exibir o resultado como se fosse a referência. Isto produz strings que parecem dados completos mas são fragmentos enganosos.

**Verificação obrigatória antes de entregar relatório.** Para cada DOI/URL que aparece em qualquer tabela do relatório, conferir que aparece *inteiro*. Buscar pela substring `]/s\d` (ou similar) seguida do final da célula sem fechamento — sinal de truncagem mid-identifier. Se encontrado, refazer a tabela.

**Por que isso importa.** Dois leitores diferentes (o agente revisor crítico e o usuário humano) podem ler o mesmo DOI truncado de modos opostos: o agente sabe que a tabela está truncada e desconta; o humano lê literalmente. O relatório precisa ser à prova de leitura literal — porque o membro de banca vai ler literalmente, em 10 minutos, durante a arguição.

### Anti-padrões em auditoria de links (Parte C)

- ❌ Marcar um DOI como quebrado sem testar via `doi.org`. CrossRef API ≠ resolvedor DOI.
- ❌ Pular URLs não-DOI. URLs de PDFs em sites institucionais quebram com facilidade e precisam ser testadas individualmente.

### Workflow recomendado para auditoria de tese/livro

1. Um único agente faz a Parte B (metadados) e a Parte C (links) — são tarefas com verificação externa empírica e o risco de auto-engano é menor.
2. **Dois ou mais agentes** fazem a Parte A (cruzamento), mas com **estratégias prescritas e diferentes**: um por leitura linear, outro por grep agressivo de padrões variados. Comparar resultados.
3. Um agente revisor crítico final lê o relatório consolidado e faz spot-checks de pelo menos 5 afirmações concretas contra o documento original.
4. **Sempre usar caso-teste de calibração** quando o solicitante for capaz de fornecer um exemplo conhecido (ex: "vocês precisam encontrar X").

### Erro estrutural a evitar no briefing de subagentes

Quando lançar subagentes para tarefas de extração exaustiva, a instrução "extraia todas as citações" é insuficiente. Briefing correto deve incluir:
- O método de varredura específico (linear, em chunks pequenos)
- A proibição explícita de regex como filtro primário
- A lista de formatos de citação a cobrir (parentético, narrativo, verbal, em legendas, em notas, em block quotes)
- Um caso-teste de calibração quando disponível
- A exigência de registrar trecho de contexto de cada citação encontrada (para o usuário poder verificar)


## 6. Experiência acumulada

### Caso Atheneu (Capítulo 16, 2025-2026)

- Estilo: Vancouver adaptado ao Manual do Autor Atheneu
- Referências incluem: artigos de periódico, relatórios de consultorias, legislação brasileira, sites institucionais
- Formato registrado no `SKILL.md` do projeto
- **Auditoria realizada em 2026-02-18**: 10 referências verificadas. Achados principais:
  - 1 referência inválida (possível alucinação de IA) — removida/substituída
  - 1 URL quebrada (site governamental) — corrigida
  - 1 dado sem fonte adequada ("80% não estruturados") — fonte complementar adicionada
  - Relatório completo: `~/PKM/Escrita/Livro Editora Atheneu/relatorio-auditoria-referencias.md`
- **Lição aprendida:** Referências de modelos de linguagem devem ser verificadas individualmente. O Deep Research do scite.ai produziu referências tangenciais de baixa utilidade.
