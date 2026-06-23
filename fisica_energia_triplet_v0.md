# Física Energia Triplet v0

Created: 2026-06-19

## Object

Three tightly coupled videos inside the `energia_calor_potencia` cluster.

This is not a mini-course on all of thermodynamics. It is the first 3-video base that lets a student start ENEM energy questions without freezing.

The coupling is:

```text
energia como quantidade conservada
  -> calor como energia em trânsito que muda temperatura
  -> potência como taxa de transferência de energia
```

## Video 1 - Energia e Sistema: A Conta Que Fecha

- id: `fis_energia_001_conta_que_fecha`
- length: 3-4 min
- prerequisite: arithmetic, units, basic reading
- core question: "O que permanece sendo contado quando a forma muda?"
- target insight: Energia is not "a thing" you see; it is the accounting quantity that lets you track transformations.

### Matter Atoms

- sistema vs ambiente
- energia inicial, energia final, energia transferida
- conservação: what leaves one place enters another form/place
- units: joule as the common accounting unit
- loss in ENEM usually means "not useful for the asked purpose", not "destroyed"

### Visual Plan

1. Start with three reservoirs: height/water, hot object, moving object.
2. Show tokens labeled `J` moving between reservoirs.
3. Collapse different physical situations into the same ledger:
   `energia inicial + entrada - saída = energia final`.
4. Show why ENEM asks for "energia aproveitada", "energia dissipada", "energia não aproveitada".

### Gate

The student must answer:

> In this problem, what is the system, where is energy entering, where is it leaving, and what quantity is being conserved or accounted for?

### Unlocks

- conservation framing
- energy transformation questions
- hydropower/power setup in video 3
- heat as energy transfer in video 2

## Video 2 - Calor, Temperatura e Q = mcΔT

- id: `fis_energia_002_calor_temperatura`
- length: 4-5 min
- prerequisite: video 1
- core question: "Why does the same energy heat different things by different amounts?"
- target insight: Calor is transferred energy; temperature change depends on mass and material.

### Matter Atoms

- heat is energy in transit because of temperature difference
- temperature is state, not amount of heat
- thermal equilibrium
- `Q = m c ΔT`
- `c` as resistance to temperature change
- conduction as rate/path intuition, without overloading formulas

### Visual Plan

1. Two blocks receive the same packet of energy; one small, one large.
2. The thermometer rises more in the smaller block.
3. Replace size with material: same mass, different `c`.
4. Animate `Q = m c ΔT` as a balance: if `m` or `c` grows, `ΔT` shrinks for the same `Q`.
5. Show thermal equilibrium: once temperatures match, the net heat flow stops.

### Gate

The student must answer:

> What is given: mass, material/calor específico, temperature change, or time? Which one is missing?

### ENEM Shapes

- water/heating/cooling problems
- thermal pollution
- material conductivity
- why something does or does not boil

## Video 3 - Potência: Energia Por Tempo

- id: `fis_energia_003_potencia_fluxo`
- length: 4-5 min
- prerequisite: videos 1 and 2
- core question: "How fast is the energy being transferred?"
- target insight: Potência is not a new kind of energy; it is energy flow per unit time.

### Matter Atoms

- `P = E / t`
- watt = joule per second
- if the energy is thermal: `P = Q / t = mcΔT/t`
- if the energy is gravitational/hydraulic: `P = ρ g vazão h`
- useful vs non-useful power
- unit conversion around seconds, minutes, MW, kW

### Visual Plan

1. Reuse the energy tokens from video 1.
2. Put a clock under the token stream.
3. Same total energy, different transfer times: high power vs low power.
4. Connect video 2: heat packets per second become `P = mcΔT/t`.
5. Connect hydropower: falling water is a continuous stream of mass; `ρ * vazão` gives mass per second, then `g h` gives energy per mass.

### Gate

The student must answer:

> Am I calculating total energy, or energy per second?

If "per second", convert time/flow first, then compute.

## Printable Gate After The Three Videos

Give 5 questions:

1. Identify the system and energy flow.
2. Use `Q = mcΔT` with one missing variable.
3. Explain a heat-flow/equilibrium situation without calculation.
4. Convert thermal energy into power using time.
5. Use hydropower form `P = ρ g Q h` and compare useful vs available power.

Pass rule:

- 4/5 correct, and
- student explicitly distinguishes "energy amount" from "power/rate" at least once.

## What This Excludes For Now

- full gas laws
- first law of thermodynamics in formal notation
- entropy
- calorimetry with phase changes
- advanced mechanical energy conservation

Those can come later. This triplet is the base layer.
