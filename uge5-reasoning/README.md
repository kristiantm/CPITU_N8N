# Uge 5: Multi-step Reasoning & Beslutninger 🧩

Velkommen til uge 5! Nu lærer du at bygge agenter der kan tænke i flere trin og træffe komplekse beslutninger.

## 🎯 Læringsmål

Efter denne uge kan du:
- Implementere multi-step reasoning
- Bygge komplekse beslutnings-flows
- Bruge Chain-of-Thought prompting
- Implementere ReAct pattern (Reasoning + Acting)
- Håndtere komplekse opgaver med sub-tasks

## 📚 Teori

### Hvad er Multi-step Reasoning?

I stedet for at svare direkte, "tænker" agenten gennem problemet trin for trin.

**Simpelt svar:**
```
Q: "Hvad er 15% af 80?"
A: "12"
```

**Multi-step reasoning:**
```
Q: "Hvad er 15% af 80?"
Thinking: 
  1. Først skal jeg konvertere 15% til decimal: 0.15
  2. Derefter ganger jeg 80 * 0.15
  3. 80 * 0.15 = 12
A: "15% af 80 er 12"
```

### Chain-of-Thought (CoT)

Chain-of-Thought får AI til at "tænke højt":

**Prompt teknik:**
```
Løs dette problem trin for trin:
1. Forstå hvad der spørges om
2. Identificer de nødvendige steps
3. Udfør hvert step
4. Verificer resultatet
5. Giv det endelige svar

Problem: [...]
```

### ReAct Pattern (Reason + Act)

ReAct kombinerer reasoning med handlinger:

```
Thought: Brugeren vil vide om det regner i morgen
Action: Kald Weather API med location=København, forecast=tomorrow
Observation: API returnerer: 70% chance of rain
Thought: Der er høj sandsynlighed for regn
Action: Foreslå at tage paraply
Final Answer: Ja, der er 70% chance for regn i København i morgen. Tag en paraply med!
```

## 🔄 Implementer Multi-step Workflows

### Pattern 1: Sequential Steps

```
Step 1: Forstå opgaven
  ↓
Step 2: Indsaml nødvendig information
  ↓
Step 3: Proces informationen
  ↓
Step 4: Træf beslutning
  ↓
Step 5: Udfør handling
  ↓
Step 6: Verificer resultat
```

### Pattern 2: Decision Tree med Loops

```
Start
  ↓
Evaluer situation
  ↓
Har vi nok info? → Nej → Indsaml mere → Loop back
  ↓ Ja
Træf beslutning
  ↓
Udfør handling
  ↓
Success? → Nej → Re-evaluer → Loop back
  ↓ Ja
Færdig
```

### Pattern 3: Planlægning → Udførelse

```
Phase 1: PLANNING
- Bryd opgaven ned i sub-tasks
- Identificer nødvendige ressourcer
- Planlæg rækkefølge

Phase 2: EXECUTION
- Udfør hver sub-task
- Verificer efter hver step
- Adjust plan hvis nødvendigt

Phase 3: VERIFICATION
- Check om alt er udført
- Verificer resultat
- Report tilbage
```

## 🛠 n8n Implementation

### Loop Node

n8n's Loop node lader dig køre samme workflow flere gange:

```
Loop for each item:
  1. Process item
  2. Check condition
  3. If not done → continue loop
  4. If done → exit loop
```

### Recursive Workflows

Workflows kan kalde sig selv for komplekse opgaver:

```
Main Workflow:
  If task is simple → Solve directly
  If task is complex → Break down → Call self for each part
```

## 🎯 Praktiske Teknikker

### Teknik 1: Step-by-Step Prompting

```
System prompt:
"Du løser problemer ved at tænke trin for trin.
For hvert problem:
1. Først, list hvad du ved
2. Så, list hvad du skal finde ud af
3. Derefter, planlæg dine steps
4. Udfør hver step
5. Verificer dit svar
6. Forklar din konklusion"
```

### Teknik 2: Self-ask

Lad agenten stille sig selv spørgsmål:

```
"Før du svarer, stil dig selv disse spørgsmål:
- Hvad er det reelle problem?
- Hvilken information mangler jeg?
- Hvilke antagelser gør jeg?
- Hvad er den bedste tilgang?
- Hvordan kan jeg verificere mit svar?"
```

### Teknik 3: Verificerings-step

Tilføj altid et verificerings-step:

```
After solving:
"Lad mig double-check:
- Er mit svar logisk?
- Har jeg addresseret alle dele af spørgsmålet?
- Er der noget jeg overså?"
```

## 📝 Opgaver

### Opgave 1: Math Reasoning Agent

Byg en agent der løser matematik-opgaver trin for trin:

```
User: "Hvis en pizza koster 80kr og jeg har 15% rabat, 
       hvor meget betaler jeg?"

Agent skal:
1. Identificere at det er en procentregning
2. Beregne rabatten (80 * 0.15 = 12)
3. Trække rabatten fra (80 - 12 = 68)
4. Præsentere svaret med forklaring
```

### Opgave 2: Research Assistant

Byg en agent der kan researche emner:

```
User: "Fortæl mig om klimaforandringer"

Agent skal:
1. Bryde emnet ned i sub-topics
2. Søge information om hver (via APIs)
3. Sammenfatte findings
4. Præsentere struktureret
5. Tilbyde at dykke dybere i specifikt område
```

### Opgave 3: Task Decomposer

Byg en agent der bryder komplekse opgaver ned:

```
User: "Jeg skal planlægge en fødselsdag for 20 personer"

Agent skal:
1. Liste alle sub-tasks (venue, mad, aktiviteter, etc.)
2. Prioritere tasks
3. Foreslå rækkefølge
4. Tilbyde at hjælpe med hver task
5. Holde styr på hvad er færdigt
```

### Opgave 4: Decision Tree Agent

Implementer en agent der guider gennem beslutninger:

```
User: "Hvilken laptop skal jeg købe?"

Agent skal:
1. Spørge om budget
2. Spørge om use case
3. Spørge om præferencer
4. Analysere options baseret på svar
5. Anbefale baseret på logik
6. Forklare hvorfor
```

## 🎨 Avanceret: ReAct Implementation

Implementer en fuld ReAct loop:

```javascript
// Pseudo-code
while (!taskComplete) {
  // THINK
  const thought = await ai.think("Hvad er næste step?");
  
  // ACT
  if (thought.requiresAction) {
    const action = await performAction(thought.action);
    const observation = action.result;
    
    // OBSERVE
    context.add(observation);
    
    // EVALUATE
    const isComplete = await ai.evaluate("Er opgaven løst?", context);
    if (isComplete) {
      taskComplete = true;
    }
  }
}

return finalAnswer;
```

## 💡 Best Practices

### DO:
✅ Bryd komplekse opgaver ned i simple steps
✅ Verificer output efter hver step
✅ Giv agenten mulighed for at korrigere sig selv
✅ Log reasoning process for debugging
✅ Sæt max iterations for at undgå infinite loops

### DON'T:
❌ Gør steps for store
❌ Glem error handling i loops
❌ Lad loops køre uendeligt
❌ Ignorer intermediate results
❌ Over-complicate simple opgaver

## 🔍 Debugging Multi-step Agents

**Tips til debugging:**
1. Log hvert step's input/output
2. Vis reasoning process til brugeren (optional)
3. Implementer breakpoints i kritiske steps
4. Test hver step isoleret først
5. Brug små test cases før komplekse

## 📖 Eksempler

### 1. Math Tutor
Se `n8n-workflows/math-reasoning.json`

### 2. ReAct Agent
Se `n8n-workflows/react-agent.json`

### 3. Task Planner
Se `n8n-workflows/task-planner.json`

## 🧪 Test Cases

Test din agent med:
1. Simple opgaver (1-2 steps)
2. Medium kompleksitet (3-5 steps)
3. Komplekse opgaver (5+ steps)
4. Opgaver der kræver backtracking
5. Impossible opgaver (skal kunne sige "ved ikke")

## ✅ Tjekliste

Før du går videre til Uge 6:
- [ ] Implementere Chain-of-Thought prompting
- [ ] Bygge multi-step workflows
- [ ] Implementere beslutnings-træer
- [ ] Bruge loops i n8n
- [ ] Håndtere complex reasoning opgaver
- [ ] Verificere agent's reasoning

## ⏭ Næste Uge

I [Uge 6: Multi-agent Samarbejde](../uge6-multi-agent/README.md) lærer du at lade flere agenter arbejde sammen for at løse endnu mere komplekse opgaver!

---

**Din agent kan nu virkelig tænke! 🤔✨**
