# 🌊 stream of consciousness — CS50 Knowledge Graph

## Context
Building a knowledge graph for CS50 (Harvard's intro to CS) using 12 weeks of
lecture transcripts → 132 independent extractions → 12 canonical merges.
Now optimizing the dependency graph with GEPA + GEPAViz.

## Guiding Taste — what makes a good CS50 graph?

**CS50 is fundamentally different from Math Academy's math:**
- It's not purely hierarchical (C → Python is a jump, not a derivation)
- Some weeks are parallel tracks (Web is its own universe)
- The "prerequisite" relation is often conceptual, not mechanical

**Taste heuristics I'm exploring:**
1. A node with >4 prerequisites probably means I'm conflating concepts
2. If a gate feels like "can you recite" vs "can you do", it's wrong
3. The graph should let you start from Scratch OR Python OR Web depending
   on background — not force one entry point
4. Some concepts are "latent" — they appear early but only crystallize later
   (e.g., "abstraction" in Scratch week, becomes real in C week)

## Key Decisions
- [ ] Use high_confidence from merge as a proxy for node stability
- [ ] Enforce Math Academy's 3-4 prereq limit per node
- [ ] Allow multiple entry points (not just week 0)
- [ ] "Gate" should be a concrete self-check, not a completion marker

## Open Questions
- How to handle concepts that appear in multiple weeks at different depths?
- Do we need "encompassing weight" (like Math Academy) or is binary enough?
- Should gates be LLM-graded or checklist-based?

## What I learned from debugging GEPA
1. **Custom adapter was overengineered** — `optimize_anything` API (pip installed from git main) is the right entry, not `gepa.optimize()` with a custom adapter. Single-task mode: the graph IS the artifact.
2. **The evaluator signature** for `optimize_anything` is `(candidate: str) -> tuple[float, dict]` or just `float`. `oa.log()` sends ASI.
3. **GEPAViz** attaches via `config.callbacks=[viz_cb]`. It implements `GEPACallback` with hooks like `on_candidate_accepted`, `on_candidate_rejected`, etc.
4. **litellm** is used internally for the reflection LM. Need `DEEPSEEK_API_KEY` env var.
5. **Taste + structural blend** (30% deterministic, 70% LLM judge) seems to work but the LLM judge is slow (~few seconds per evaluation).

## Key Insight: what GEPA actually needs
- A candidate = a text artifact (in our case, the graph JSON as a string)
- An evaluator that scores the candidate
- ASI (Actionable Side Information) fed via `oa.log()` so the reflection LM knows what to improve
- The reflection LM mutates the candidate text based on the ASI

## GEPA Run #1 — Postmortem (2026-06-07)

**Resultado**: Plateau em 0.684, não melhorou. 15 iterações rodadas.

**O que aprendi:**
1. GEPA optimize_anything com single-task mode funciona — o pipeline está íntegro
2. GEPAViz serviu corretamente (callback via `config.callbacks=[viz_cb]`)
3. **MAS**: grafo de 304 nós ~500KB é muito grande pra one-shot mutation via LLM
   - O reflection LM trunca a resposta, perdendo mudanças no final
   - As mutações são difusas — melhora um gate aqui, piora outro ali
   - Sem dataset/valset com per-exemplo, não dá pra usar Pareto frontier direito
4. O que AMADURECEU foram os GATES — o LM começou a reescrever
   "I can explain and use X" → "I can write a hash function that distributes keys uniformly"

**Se eu fosse refazer:**
- Abordagem bottom-up: otimizar um SUBCONJUNTO do grafo por vez
  (ex: só Week 1 de C, ~50 nós)
- Ou usar o optimize_anything pra gerar o pipeline de construção do grafo,
  não o grafo em si
- Ou usar com dataset/valset pra ter Pareto de verdade

## Timeline
- Merges complete: ✅
- GEPA setup: ✅ (optimize_anything API, GEPA 0.1.1 from git main)
- GEPAViz: ✅ live on port 5151
- First optimization run: 🔄 running now with 50 metric calls
