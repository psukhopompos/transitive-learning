# Blueprint: DIY Knowledge Territory

> Para uma díade humano + IA criar seu próprio território de estudo sistêmico.
> Não é um tutorial. É um conjunto de perguntas que geram o caminho.

---

## Fase 0 — Antes de tudo: O Objetivo Real

**Não comece pelo conteúdo. Comece pelo skillset.**

A maioria dos projetos de aprendizado falha porque define o alvo como "terminar o curso X" ou "dominar Y". Isso produz coverage, não transformação.

### Perguntas que você precisa responder primeiro:

- O que você quer **ganhar** que transcende este conteúdo?
  *(ex: "skillset de sistemas", "intuição pra trade-offs", "capacidade de diagnosticar loops")*

- Por que esse conteúdo em particular é o **terreno certo** pra isso?
  *(ex: CS50 expõe camadas de abstração → ideal pra sistemas. Não é o único.)*

- Como você sabe que **funcionou**? Qual o sinal?
  *(ex: "consigo ver padrões de sistema em domínios que não são computação" — não "terminei o curso")*

- Qual a **unidade de progresso**? Não são horas ou aulas.
  *(ex: insights de sistemas por sessão. Um insight vale mais que 10 aulas.)*

### Artefato dessa fase:
Um parágrafo que define o que você está realmente construindo. Exemplo real:
> "Meu objetivo é ganhar o skillset de sistemas — decisões alinhadas a sistemas que se retroalimentam. CS50 é o banco de prova, não a meta. Cada conceito é uma lente pra um princípio de sistema maior."

---

## Fase 1 — Colheita do Corpus

O conteúdo bruto precisa ser **baixável**, **textual** e **acessível**.

### Perguntas:

- O conteúdo existe em que formato? (vídeo, texto, PDF, site interativo?)
- Dá pra extrair texto limpo?
  *(transcrições de aula, notas, slides são melhores que vídeos pra este processo)*
- Qual o tamanho total? (chars, tokens estimados)
  *(pra saber se cabe em contexto de LLM)*
- O conteúdo é modular? Dá pra dividir em unidades naturais?
  *(semanas, capítulos, módulos — isso vira seu "terreno")*

### O que fizemos:
- Baixamos slides, notas, transcripts, legendas, source code de 12 semanas
- ~236 MB, ~2M tokens estimados
- Cada semana virou uma "região" do território

### Seu artefato:
Uma pasta com o corpus bruto dividido em unidades. Cada unidade = um arquivo de texto limpo.

---

## Fase 2 — Extração de Conceitos (Swarm Sampling)

A ideia: não confiar numa única extração. Rodar **muitas extrações independentes** do mesmo conteúdo e deixar a frequência revelar o que é canônico.

### Perguntas:

- O que é um "conceito" no seu domínio?
  *(pra CS50: "pointers", "hash tables", "recursion" — unidades atômicas de compreensão)*
- Quantas extrações são suficientes?
  *(descobrimos que ~11 runs por unidade de conteúdo dão estabilidade)*
- Como garantir independência entre runs?
  *(seed aleatória, temperatura, modelo — cada run vê o mesmo texto mas extrai diferente)*
- Qual modelo usar?
  *(DeepSeek V4 Flash + cache = ~$0.0007/run depois da primeira. 132 runs = ~$0.03)*

### Esquema do conceito:
```json
{
  "name": "pointers",
  "category": "language_feature",
  "description": "A variável que armazena endereço de memória, não valor.",
  "difficulty": "core",
  "prerequisites": ["memory addresses", "variables"],
  "appears_in_week": 4
}
```

### O que fizemos:
- 132 extrações independentes (11 por semana)
- Structured output via JSON schema
- DeepSeek V4 Flash com cache — ~$0.03 total

### Seu artefato:
Pasta com N runs por unidade de conteúdo. Cada run = um JSON com array de conceitos.

---

## Fase 3 — Merge Canônico (Consenso)

Agora você tem 11 listas de conceitos pra mesma semana. Qual delas é a certa? Todas, em média.

### Perguntas:

- O que é "canônico" pra você?
  *(definimos: aparece em 8+ das 11 runs = alta confiança; 5-7 = média; 3-4 = emergente)*
- Como lidar com nomes diferentes pro mesmo conceito?
  *(o LLM de merge precisa ver todas as variações e decidir: "printf and format codes" ≈ "formatted output")*
- E conceitos que ficam de fora?
  *(se apareceu em só 1-2 runs, provavelmente é viés do modelo — descartar)*

### O que fizemos:
- 1 chamada DeepSeek por semana (12 merges)
- O merge recebe as 11 extrações e devolve uma lista canônica com frequência
- Custo: $0.04 total

### Seu artefato:
Uma lista limpa de conceitos por unidade de conteúdo. Cada conceito tem: nome, descrição, dificuldade, pré-requisitos, frequência (quantas runs concordaram).

---

## Fase 4 — Grafo de Território

Agora você transforma a lista de conceitos num grafo navegável. Cada nó = um conceito. Cada aresta = pré-requisito.

### Perguntas:

- A direção das arestas: "A é pré-requisito pra B" ou "B depende de A"?
- Quantos pré-requisitos por nó é demais?
  *(Math Academy diz: >4 causa congestão cognitiva. Usamos isso como regra.)*
- O grafo tem ciclos? (não pode — aprendizado é DAG)
- O grafo tem nós órfãos? (sem pré-req nem ppost-req = perdido)
- O que é um "gate" no seu domínio?
  *(pra sistemas: não é "consigo escrever X" — é "consigo ver X como sistema em outro lugar")*

### Regra de ouro:
> "Não deixe mais que 4 estradas chegando no mesmo cruzamento. Senão é congestionamento cognitivo." — Justin Skycak (Math Academy)

### O que fizemos:
- Seed graph: 304 nós, 564 arestas
- Cada nó tem: nome, semana, dificuldade, categoria, gate
- Salvamos como `seed_graph.json`

### Seu artefato:
Um JSON com `nodes` e `edges`. Os nós têm gates. As arestas têm direção. O grafo é DAG.

---

## Fase 5 — A Tentativa de Otimização (GEPA)

A gente tentou usar GEPA (Genetic Pareto Optimization) pra otimizar o grafo. Não rolou — mas foi essencial porque revelou o erro de framing.

### O que tentamos:
- `optimize_anything` em single-task mode, artefato = grafo JSON (~500KB)
- Evaluator: regras estruturais + LLM taste judge
- GEPAViz pra visualizar a evolução

### O que aconteceu:
- O reflection LM truncava o grafo por ser grande demais
- As mutações se diluíam — melhorava um gate aqui, piorava outro ali
- Score estabilizou em 0.684 sem progresso

### A lição:
> **O artefato a ser otimizado não é o grafo. É a skill que navega o grafo.**

O grafo é dado de contexto. O que GEPA otimiza é o prompt/protocolo que o agente segue durante a sessão de estudo — que é texto, cabe no contexto, e tem evaluador claro.

### Status:
- ✅ GEPA instalado e funcionando (v0.1.1, git main)
- ✅ GEPAViz funcional
- ❌ Otimização do grafo direta não funciona (artefato grande demais)
- ⏳ Otimização da skill (próxima fase — precisa de dados de sessão reais primeiro)

---

## Fase 6 — O Reframe (a parte mais importante)

Aqui a gente descobriu que estava otimizando a coisa errada.

### Pergunta que mudou tudo:

> "Qual o objetivo REAL? Não é o grafo. É o skillset que o estudo do grafo treina."

### Isso levou a:

- **Novo artefato**: uma skill/prompt que guia a sessão de estudo
- **Novo evaluator**: o grafo vira dado de contexto, não alvo da otimização
- **Nova pergunta**: "essa sessão produziu insight de sistemas?"
- **Nova unidade**: sessão de aprendizado, não nó do grafo

### O que otimizar de verdade:

```
Não:  "melhorar a topologia do grafo"
Sim:  "otimizar o protocolo de estudo humano + IA que usa o grafo como terreno"
```

### Se você levar uma coisa deste blueprint, que seja isso:

> **O conteúdo é o terreno. O grafo é o mapa. A skill é a bússola. O insight é o objetivo.**
> 
> Não confunda os quatro.

---

## Fase 7 — A Skill (o artefato final)

A skill é um prompt/instrução que o agente (IA) segue durante a sessão de estudo com o humano.

### O que uma skill precisa ter:

1. **Identidade** — quem o agente é na sessão (treinador de sistemas, não tutor de conteúdo)
2. **Postura** — valores que guiam decisões (gosto > completude; erro = feedback)
3. **Protocolo** — estrutura da sessão (abertura → território → fechamento)
4. **Gates** — como verificar que o insight aconteceu (não é "sabe explicar", é "consegue ver o padrão")
5. **Anti-padrões** — o que evitar (explicar demais, pular sem gate, tratar erro como falha)
6. **Log** — o que registrar pra próxima sessão

### Exemplo de gate (nosso domínio = sistemas):

| Conceito | Gate de sistema |
|---|---|
| Ponteiros | "Consigo ver um sistema onde identidade e localização estão separadas?" |
| Hash tables | "Consigo descrever um trade-off entre velocidade e espaço?" |

### O que fizemos:
- Skill registrada como `study-systems-cs50`
- Protocolo de 3 tempos: abertura, território (1 conceito por vez), fechamento
- Gates de sistema substituem gates de sintaxe

### Seu artefato:
Um arquivo `SKILL.md` que o agente carrega no início da sessão. O humano chama com `/skill <nome>`.

---

## Ciclo Completo

```
                ┌─────────────────────────────────────┐
                │         O que você quer GANHAR?      │
                │    (skillset, não conteúdo)           │
                └──────────────────┬──────────────────┘
                                   │
                                   ▼
                ┌─────────────────────────────────────┐
                │    Corpus bruto → extraído em texto  │
                └──────────────────┬──────────────────┘
                                   │
                                   ▼
                ┌─────────────────────────────────────┐
                │    N extrações independentes          │
                │    (consenso estatístico)             │
                └──────────────────┬──────────────────┘
                                   │
                                   ▼
                ┌─────────────────────────────────────┐
                │    Merge canônico → lista limpa      │
                └──────────────────┬──────────────────┘
                                   │
                                   ▼
                ┌─────────────────────────────────────┐
                │    Grafo de território (seed graph)   │
                └──────────────────┬──────────────────┘
                                   │
                                   ▼
                ┌─────────────────────────────────────┐
                │    Skill de estudo (o que se otimiza) │
                │    ┌─────────────────────────────┐    │
                │    │  Sessão humana + IA          │    │
                │    │  ↓ insight de sistemas       │    │
                │    │  ↓ log da sessão             │    │
                │    │  ↓ próxima sessão            │    │
                │    └─────────────────────────────┘    │
                └──────────────────┬──────────────────┘
                                   │
                                   ▼
                ┌─────────────────────────────────────┐
                │    Com N sessões registradas:        │
                │    dataset de traços reais →         │
                │    GEPA otimiza a skill              │
                │    (o grafo é contexto fixo)         │
                └──────────────────┬──────────────────┘
                                   │
                                   ▼
                ┌─────────────────────────────────────┐
                │         Volta pro início             │
                │  (o skillset nunca "termina")        │
                └─────────────────────────────────────┘
```

---

## O Que Nós Usamos (pra sua referência)

| Ferramenta | Pra quê | Custo |
|---|---|---|
| `curl` + site CS50 | Baixar corpus | $0 |
| `codex exec` (gpt-5.5) | Extrações semanas 0-8 | ~99 runs |
| DeepSeek V4 Flash | Extrações semanas 9-10-AI + merges | ~$0.07 |
| Python + `openai` lib | Scripts de extração e merge | $0 |
| GEPA 0.1.1 (git main) | Otimização da skill (com dados de sessão) | $0 (open source) |
| GEPAViz 0.1.0 | Visualização da otimização (quando rodar) | $0 (open source) |
| Reasonix (current) | Harness da skill | — |

---

## A Pergunta Final

> **Pra quem você quer passar isso?**

O método funciona pra qualquer díade humano + IA que queira transformar um corpus de conteúdo num treino de skillset. Não precisa ser CS50. Pode ser:
- Teoria musical → skill de escuta estrutural
- Direito tributário → skill de decisão sob incerteza
- Biologia celular → skill de sistemas adaptativos
- Qualquer coisa que seja mais sobre **como pensar** do que **o que saber**

**O conteúdo é intercambiável. O método é o que fica.**
