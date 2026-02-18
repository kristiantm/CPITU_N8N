# Uge 6: Multi-agent Samarbejde 🤝

Velkommen til uge 6! Nu lærer du at bygge systemer hvor flere agenter arbejder sammen - hver med deres specialitet.

## 🎯 Læringsmål

Efter denne uge kan du:
- Designe multi-agent arkitekturer
- Implementere agent-to-agent kommunikation
- Bygge specialist agenter
- Orkestrere agent workflows
- Håndtere konflikter mellem agenter

## 📚 Teori

### Hvorfor Multi-agent?

Ligesom et team er stærkere end en enkelt person, er flere specialiserede agenter bedre end én generalist.

**Single Agent:**
```
User → Agent (gør alt) → Response
```

**Multi-agent:**
```
User → Manager Agent
         ↓
    ┌────┴────┬────────┬────────┐
    ↓         ↓        ↓        ↓
  Researcher Writer  Critic  Editor
    ↓         ↓        ↓        ↓
    └────┬────┴────────┴────────┘
         ↓
    Final Response
```

### Multi-agent Patterns

#### 1. Sequential Pattern (Pipeline)
```
Agent 1 → Agent 2 → Agent 3 → Result
(Each agent processes and passes to next)
```

#### 2. Parallel Pattern
```
              ┌→ Agent 1 →┐
Input → Router ├→ Agent 2 →┤→ Aggregator → Output
              └→ Agent 3 →┘
```

#### 3. Hierarchical Pattern
```
          Manager
        /    |    \
    Agent1 Agent2 Agent3
      |      |      |
   Sub1A  Sub2A  Sub3A
```

#### 4. Debate Pattern
```
Agent 1 proposes → Agent 2 critiques → Agent 1 revises
     ↓                    ↓                   ↓
(Loop until consensus or max iterations)
```

## 🎭 Agent Roller

### 1. Manager Agent
- Forstår brugerens behov
- Delegerer til specialist agenter
- Koordinerer output
- Sammenfatter resultater

### 2. Specialist Agenter

**Researcher:**
```
Specialitet: Informations-indsamling
Tools: Web search, APIs, databases
Output: Faktuel information
```

**Writer:**
```
Specialitet: Content creation
Input: Research data
Output: Velskrevet indhold
```

**Critic:**
```
Specialitet: Kvalitets-kontrol
Input: Draft content
Output: Feedback og forbedringsforslag
```

**Editor:**
```
Specialitet: Finpudsning
Input: Content + feedback
Output: Polished final version
```

## 🛠 Implementation i n8n

### Pattern 1: Simple Delegation

```
Webhook (User input)
  ↓
Manager Agent (Routes task)
  ↓
Switch Node (Determines which specialist)
  ├→ Research Agent
  ├→ Writing Agent
  ├→ Math Agent
  └→ General Agent
  ↓
Format & Return
```

### Pattern 2: Sequential Pipeline

```
Webhook
  ↓
Agent 1: Research (gather info)
  ↓
Agent 2: Analyze (process info)
  ↓
Agent 3: Synthesize (create output)
  ↓
Agent 4: Review (quality check)
  ↓
Return final output
```

### Pattern 3: Parallel + Merge

```
Webhook
  ↓
Split (send to multiple agents in parallel)
  ├→ Agent A (task 1)
  ├→ Agent B (task 2)
  └→ Agent C (task 3)
  ↓
Merge Node (combine results)
  ↓
Synthesis Agent (create cohesive response)
  ↓
Return
```

## 🔨 Praktisk Workshop

### Workshop 1: Byg et Content Team

**Opgave:** Lav en blog post om et emne

**Agenter:**
1. **Researcher**: Finder fakta om emnet
2. **Writer**: Skriver første draft
3. **Critic**: Giver feedback
4. **Editor**: Laver final version

**Workflow:**
```
User: "Skriv en blog post om AI"
  ↓
Researcher: Finder key points om AI
  ↓
Writer: Skriver draft baseret på research
  ↓
Critic: "Godt, men mangler eksempler"
  ↓
Writer: Reviderer med eksempler
  ↓
Editor: Polisher og formatterer
  ↓
Final blog post til user
```

### Workshop 2: Byg et Customer Support System

**Agenter:**
1. **Triage Agent**: Kategoriserer spørgsmål
2. **Technical Agent**: Svarer på tekniske spørgsmål
3. **Billing Agent**: Håndterer fakturering
4. **General Agent**: Håndterer alt andet
5. **QA Agent**: Checker svar kvalitet

## 📝 Opgaver

### Opgave 1: Manager + 3 Specialists

Byg et system med:
- 1 Manager agent der router
- 3 specialist agenter (vælg selv speciale)
- Proper delegation baseret på input

### Opgave 2: Debate System

Implementer et debat-system:
- Agent A foreslår en løsning
- Agent B kritiserer/forbedrer
- Agent A reviderer
- (Max 3 rounds)
- Final decision

**Test med:** "Hvad er den bedste måde at lære at kode?"

### Opgave 3: Research Pipeline

Byg en research pipeline:
1. **Query Expander**: Laver flere søge-queries fra bruger input
2. **Researchers** (parallel): Hver søger på en query
3. **Synthesizer**: Kombinerer alle findings
4. **Summarizer**: Laver final sammenfatning

### Opgave 4: Code Review Team

Simuler et code review team:
- **Coder**: Skriver kode til et problem
- **Security Reviewer**: Checker for security issues
- **Performance Reviewer**: Checker for performance
- **Style Reviewer**: Checker code style
- **Integrator**: Implementerer alle forbedringsforslag

## 🎨 Avanceret: Emergent Behavior

Når agenter samarbejder, kan der opstå emergent behavior:

**Eksempel - Automatic Task Breakdown:**
```
User: "Hjælp mig med at planlægge en rejse til Japan"

Manager: *Ser dette er komplekst*
         *Opdeler i sub-tasks*
         *Delegerer hver til specialist*

Budget Agent: Beregner costs
Itinerary Agent: Planlægger rute
Culture Agent: Giver culture tips
Food Agent: Anbefaler restauranter

Manager: *Kombinerer alt til cohesive plan*
```

## 🔄 Agent Communication

### Message Passing

Agenter kommunikerer via strukturerede beskeder:

```json
{
  "from": "manager",
  "to": "researcher",
  "task": "Find information about topic X",
  "context": {...},
  "deadline": "high_priority"
}
```

### Shared Context

Alle agenter har adgang til shared context:

```javascript
context = {
  originalRequest: "User's question",
  currentPhase: "research",
  findings: [],
  decisions: [],
  nextSteps: []
};
```

## 💡 Best Practices

### DO:
✅ Giv hver agent en klar, smal rolle
✅ Definer klare interfaces mellem agenter
✅ Implementer timeouts for hver agent
✅ Log alle agent interaktioner
✅ Lad manager agent koordinere

### DON'T:
❌ Lav overlappende agent ansvar
❌ Lad agenter løbe i cirkler
❌ Ignorer conflicts mellem agenter
❌ Glem at aggregere outputs
❌ Over-complicate med for mange agenter

## 🔍 Konflikt Håndtering

Når agenter er uenige:

### Strategi 1: Voting
```
Agent A: "Gør X"
Agent B: "Gør Y"
Agent C: "Gør X"
Result: Majority wins → X
```

### Strategi 2: Weighted Opinion
```
Expert agent's opinion = 2x weight
General agent's opinion = 1x weight
```

### Strategi 3: Manager Decides
```
Agenter giver input → Manager træffer final beslutning
```

## 📖 Eksempel Workflows

### 1. Content Creation Team
Se `n8n-workflows/content-team.json`

### 2. Research Squad
Se `n8n-workflows/research-squad.json`

### 3. Customer Support System
Se `n8n-workflows/support-system.json`

## 🧪 Test Scenarios

Test dit multi-agent system med:
1. Simple opgaver (skal ikke bruge alle agenter)
2. Komplekse opgaver (kræver mange agenter)
3. Ambiguous opgaver (manager skal vælge)
4. Conflicting requirements
5. Edge cases

## ✅ Tjekliste

Før du går videre til Uge 7:
- [ ] Implementere multi-agent arkitektur
- [ ] Bygge specialist agenter
- [ ] Implementere agent kommunikation
- [ ] Håndtere agent coordination
- [ ] Håndtere conflicts
- [ ] Aggregere outputs fra multiple agenter

## ⏭ Næste Uge

I [Uge 7: Deployment & Dokumentation](../uge7-deployment/README.md) lærer du at deploye din færdige agent og dokumentere den professionelt!

---

**Dit agent-team er klar til at tackle enhver opgave! 🎉✨**
