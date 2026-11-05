---
name: texto-didatico
description: "Escrever texto didático narrativo na base Textos Didáticos do Notion, no formato e na voz autoral da série de Cabrobró do Oeste (modelo canônico: 'Necessidade, demanda e oferta em Saúde'). Cobre estrutura da página, arco narrativo, semântica dos callouts, verificação zero-trust das fontes, diagramas e mecânica de publicação no Notion."
status: ativa
version: 1.1
date: 2026-11-05
evidence: Formato validado em três textos da série — Necessidade/demanda/oferta (2022, texto-fonte de Raphael), Suicídio e autolesão infanto-juvenil na APS (2026-08-11) e Acesso avançado na APS (2026-08-12).
---

# Texto Didático — série narrativa da base Textos Didáticos (Notion)

## Propósito e gatilho

Produzir textos didáticos para estudantes de medicina/saúde coletiva no formato consolidado da base **📚 Textos Didáticos** do Notion de Raphael. Usar esta skill SEMPRE que ele pedir "um texto na base de textos didáticos", "um texto no formato do de necessidades e demandas" ou "mais um texto da série".

O produto é uma página do Notion, não um arquivo local. A voz autoral é a de Raphael (o texto sai assinado por ele); o rigor factual é o do sistema de escrita acadêmica (zero-trust).

## Onde publicar (mecânica Notion)

- Base: database **Textos Didáticos** — `44a6eb06-b153-45c6-b959-e93c76ee935d`; data source `collection://7bcfaa56-c603-4865-93b1-619955c99562`.
- Criar com `notion-create-pages` usando `parent: {type: "data_source_id", data_source_id: "7bcfaa56-c603-4865-93b1-619955c99562"}`.
- Propriedades: `Nome` (título) e `Assunto` (select). **Usar somente valores existentes do enum** (hoje: "Políticas de Saúde", "Inteligência Artificial") — autoria de novos valores é exclusiva de Raphael (memória `feedback-autoria-nomenclatura-raphael`).
- Ícone: um emoji temático por página (🧑🏾‍⚕️, 💛, 📅...).
- Imagens: subir SVG por `notion-create-attachment` (campo `content`; SVG é texto) e referenciar `![legenda com FONTE](file-upload://<id>)`. Para binários, `notion-create-file-upload` + POST, ou `source_url` com URL assinada fresca (truque do banner: `notion-fetch` na página anterior renova a URL S3 por 300 s; anexar imediatamente).
- Após criar, **fazer `notion-fetch` da página nova** para conferir renderização (colunas, callouts, tabela, imagens).

## Estrutura canônica da página

1. **Cabeçalho em duas colunas** (ratios ~56/44):
   - Esquerda: `**SUMÁRIO** {color="brown"}` + `<table_of_contents color="brown_bg"/>`.
   - Direita: `**Autor**: Raphael A. T. de Aguiar`; selo Creative Commons BY-NC-SA 4.0 (imagem `https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png` com legenda-link); citação de transparência: `> Gerado com apoio de IA (Modelo <modelo>).<br>Números, estudos e experiências: **verificados em <mês de ano>**.`; banner UFMG (reaproveitar o anexo `banner-ufmg.png` de página anterior da série).
2. **Seções numeradas em H1**, cada uma seguida de divisor `---`. Padrão de arco:
   - `# 1. Introdução: <cena>` — abre com cena concreta no universo da série.
   - `# 2..N. <Ordinal> descoberta: <tese em linguagem viva>` — uma tese por seção, encadeadas.
   - Uma seção de **críticas/contraditório** sempre que o tema tiver disputa real.
   - `# N. De volta a <personagem>` — retorno ao caso da abertura, aplicando o aprendido.
   - `# N+1. Roteiro para o Grupo de Discussão` — 6 a 8 perguntas operacionais, ancoradas no município fictício, com citações.
   - `# N+2. Para se capacitar` — cursos/materiais REAIS com URL testada (ou instrução de busca quando o link é instável).
   - `# N+3. Referências Bibliográficas` — ABNT, numeradas por ordem de aparição, com `[**Disponível aqui**](url)` apenas para URLs testadas.
   - `# N+4. Para saber mais` — leituras além do escopo + links para os textos anteriores da série.
   - Fecho em itálico: **nota metodológica** declarando como os números foram verificados e listando as ressalvas incorporadas ao corpo.
3. **Modular a carga cognitiva** (pedido de Raphael na revisão do texto de acesso avançado, nov. 2026):
   - Dentro de cada seção H1, **subtítulos H2** para cada bloco de ideia; quando a seção apresenta uma tipologia, **H2 para as categorias e H3 para os itens** (ex.: "Modelos que separam a demanda..." em H2; "Tradicional", "Carve-out" em H3).
   - Parágrafos curtos, uma ideia por parágrafo. Enumerações de passos ou movimentos viram lista numerada com frases completas.
   - Todo conceito-chave recém-apresentado ganha um **box de definição** (📖, ver callouts) logo depois do parágrafo que o introduz.
   - Quadros comparativos da fonte (ex.: os quatro quadrantes de pressão assistencial × frequência) viram **tabela + box** com uma linha por categoria; tipologias com cinco ou mais itens ganham tabela-síntese.

## Universo narrativo (continuidade obrigatória)

- Cenário fixo: **Cabrobró do Oeste**, cidade fictícia (~8 mil habitantes) do interior de Minas; leitores são "**você e sua dupla**" no **Internato Rural** (2ª pessoa do singular dirigida ao leitor + a dupla).
- **Supervisão no Internato Rural (desenho real; não inventar outro)**. Não existe "preceptora". Há dois papéis distintos:
  - **Supervisor local**: o médico da equipe de Saúde da Família do município que supervisiona os alunos. É a referência para a **discussão clínica durante o atendimento** (conduta, retorno, exame, encaminhamento, limites do que o interno pode fazer). Na série, é o **Dr. Anselmo**, há três anos na mesma equipe da UBS de Cabrobró.
  - **Professor**: docente da UFMG que supervisiona a dupla e **viaja ao município** para encontrá-la. É a referência para a **discussão de fluxos, APS e sistema de saúde**, propõe o tema da semana e conduz o grupo de discussão.
  - Regra prática: pergunta que nasce dentro do consultório → supervisor local; pergunta sobre organização do serviço, política ou rede → professor. Os textos publicados até agosto de 2026 ainda usam "preceptora"; ao revisá-los, aplicar este desenho.
- **Estrutura de Cabrobró** (manter coerência): UBS com duas equipes de Saúde da Família (a de seu Geraldo, com alta rotatividade de médicos, e a do Dr. Anselmo); **pronto atendimento no próprio município**; **não há UPA**, cujo porte não cabe numa cidade de ~8 mil habitantes; especialidades e maternidade por pactuação regional (Montes Claros, no texto de referências).
- **Verossimilhança dos números do cenário**: os números de Cabrobró são fictícios, mas precisam caber num município de ~8 mil habitantes (ex.: espera de semanas, não de meses, para consulta agendada na UBS) e ser declarados como fictícios na nota metodológica.
- Personagens recorrentes (reutilizar antes de criar): o **secretário de saúde** recém-empossado e sem formação em saúde (texto 1); o **Dr. Anselmo**, supervisor local; o **professor** da UFMG; **Dona Marlene** (texto de referências/contrarreferências); **Larissa, 14 anos** (texto de suicídio); **seu Geraldo, 52 anos, pedreiro, hipertenso** (texto de acesso avançado). Novos personagens-paciente podem ser criados; ganham nome, idade, ofício e um detalhe humano.
- Arco temporal: a semana (segunda a sexta) até o **grupo de discussão**; cada seção avança um dia ou um turno.
- **Amarração entre textos**: citar explicitamente conceitos e cenas dos textos anteriores com link Notion (ex.: demanda reprimida e o caso das tardes de pré-natal vazias, do texto 1). O leitor da série deve sentir um mundo só.

## Voz autoral e proibições

- Português brasileiro pleno, frases completas, tom de conversa culta com humor sutil e concreto (cafezinho com pão de queijo, grupos de WhatsApp como praça). Didática por **descoberta progressiva**: os estudantes erram, se corrigem, formulam.
- Negrito para dados e teses centrais; itálico para termos estrangeiros e ênfase leve; perguntas retóricas com parcimônia.
- **Proibições explícitas de Raphael**: travessões em excesso (preferir vírgula, dois-pontos, parênteses; travessão quase só em diálogo); frases vazias porém exclamativas ("Fascinante!", "E isso muda tudo!"); jargão sem explicação; listas telegráficas onde caberia prosa.
- Honestidade metodológica NO CORPO do texto (não só em nota): efeitos modestos, resultados negativos, vieses e limites entram na narrativa como achados, muitas vezes como parágrafo "Honestidade sobre limites".
- Dados sempre com contexto de grandeza e citação `\[N\]` (escapar os colchetes no markdown do Notion).

## Semântica fixa dos callouts

| Callout | Uso |
|---|---|
| `<callout icon="📖" color="green_bg">` | Definição de um conceito que acabou de ser apresentado, para fixação: abre com o termo em negrito, uma ideia por box, com citação |
| `<callout icon="💡" color="pink_bg">` | Síntese para reter ("**Guarde este ponto.**", "**A síntese da semana.**") |
| `<callout icon="💬" color="orange_bg">` | Provocação para o grupo ("**Para discutir com o professor:**", "**Pergunta desconfortável para o grupo.**") |
| `<callout icon="📅" color="yellow_bg">` | Validade temporal dos dados ("**Panorama verificado em <mês/ano>.**" + instrução de reverificação) |
| `<callout icon="💡" color="blue_bg">` | Curiosidade ou caso real (institucional, histórico, UFMG) |

Tabela `<table header-row="true">` para síntese de evidência (pergunta → resposta → fonte; ou intervenção → efeito → fonte → quem opera).

## Rigor factual (inegociável)

1. **Ler as fontes primárias na íntegra** antes de escrever (pdftotext/leitura visual; registrar notas com números exatos e localização). Nenhum dado sai de memória de modelo.
2. Metadados de toda referência conferidos em PubMed/CrossRef; **toda URL testada** (código HTTP) na data da escrita; URL instável (.gov.br) → citar sem link com instrução de busca.
3. Verificação reforçada de números: localizar o número NO texto-fonte antes de citá-lo (protocolo da skill `pesquisa-academica`, checklist 3.1 de `referencias-e-citacoes`).
4. Diagramas: **ler a skill `dataviz` antes** de qualquer SVG; paleta validada pelo script da skill; legenda "FONTE: elaboração própria, a partir de \[N\]..."; renderizar (PNG) e inspecionar visualmente antes de subir.
5. Nota metodológica final: o que foi verificado, quando, e quais ressalvas estão no corpo.

## Checklist de saída

- [ ] Página criada na base certa, com `Assunto` de enum existente e ícone.
- [ ] Cabeçalho completo (sumário, autor, CC, aviso de IA com mês de verificação, banner).
- [ ] Arco narrativo fechado (cena de abertura → descobertas → contraditório → retorno ao caso).
- [ ] Links para textos anteriores da série presentes; personagens/conceitos reutilizados.
- [ ] Todos os `\[N\]` têm entrada na lista; toda URL da lista foi testada hoje.
- [ ] Supervisão no desenho real: clínica com o supervisor local, sistema e grupo de discussão com o professor; nenhuma "preceptora".
- [ ] Carga cognitiva: H2 por bloco de ideia, H3 para itens de tipologia, parágrafos curtos, box 📖 depois de cada conceito-chave novo.
- [ ] Callouts seguem a semântica fixa; há pelo menos um 💬 provocativo e um 📅 de validade temporal quando há dados datados.
- [ ] Releitura final caçando: travessões em excesso, exclamações vazias, jargão sem explicação, fio lógico solto entre seções.
- [ ] `notion-fetch` de conferência da renderização.
