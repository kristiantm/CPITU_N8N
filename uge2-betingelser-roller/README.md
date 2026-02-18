# Uge 2: Betingelser & Roller 🎯

Velkommen til uge 2! Nu skal du lære at give din agent intelligens til at træffe beslutninger og skifte mellem forskellige roller.

## 🎯 Læringsmål

Efter denne uge kan du:
- Implementere IF/ELSE logik i n8n workflows
- Lade agenten skifte rolle baseret på kontekst
- Analysere bruger-input og reagere forskelligt
- Bygge beslutnings-træer i din agent

## 📚 Teori

### Hvad er Betingelser?

Betingelser (conditions) gør din agent smart ved at lade den reagere forskelligt baseret på situationen.

**Simpelt eksempel:**
```
HVIS bruger spørger om vejret
  → Giv vejrinformation
ELLERS HVIS bruger siger "hej"
  → Sig hej tilbage
ELLERS
  → Giv generelt svar
```

### Hvorfor Roller?

Roller gør din agent mere fleksibel. Den samme agent kan være:
- En lærer når du har brug for hjælp
- En ven når du vil snakke
- En træner når du vil motiveres

### Beslutnings-træer

Et beslutnings-træ hjælper agenten med at vælge den rigtige handling:

```
Start
  │
  ├─ Kategori: Lektier?
  │   ├─ Ja → Skift til Lærer-rolle
  │   └─ Nej → Fortsæt
  │
  ├─ Tone: Trist?
  │   ├─ Ja → Skift til Supporter-rolle
  │   └─ Nej → Fortsæt
  │
  └─ Standard → Almindelig Chat-rolle
```

## 🛠 n8n IF/Switch Nodes

### IF Node

IF node checker en betingelse og går en vej hvis sand, en anden hvis falsk.

**Eksempel konfiguration:**
```
Betingelse: {{ $json.body.message.toLowerCase() }}
Operator: contains
Værdi: "hjælp"

TRUE output: Hjælpe-flow
FALSE output: Normal-flow
```

### Switch Node

Switch node kan checke mange betingelser på én gang - som en række IF statements.

**Eksempel:**
```
Mode: Rules

Rule 1: {{ $json.body.message }} contains "vejr" → Output 0
Rule 2: {{ $json.body.message }} contains "hjælp" → Output 1
Rule 3: {{ $json.body.message }} contains "hej" → Output 2
Fallback: → Output 3
```

## 🎭 Implementer Roller

### Metode 1: System Prompt Switcher

Brug forskellige system prompts baseret på bruger-input:

```javascript
// I en Function node
let systemPrompt = "";

if (message.toLowerCase().includes("lektier")) {
  systemPrompt = "Du er en tålmodig lærer...";
} else if (message.toLowerCase().includes("trist")) {
  systemPrompt = "Du er en opmuntrende ven...";
} else {
  systemPrompt = "Du er en almindelig chatbot...";
}

return { systemPrompt, message };
```

### Metode 2: Dynamic Context

Tilføj kontekst til din prompt dynamisk:

```json
{
  "model": "openai/gpt-3.5-turbo",
  "messages": [
    {
      "role": "system",
      "content": "Du er en AI-assistent. {{ $json.roleContext }}"
    },
    {
      "role": "user",
      "content": "{{ $json.message }}"
    }
  ]
}
```

## 🔨 Praktisk Workshop

### Del 1: Simpel IF Betingelse

**Byg en workflow der:**
1. Checker om bruger siger "hej"
2. Hvis ja: Svar med venlig hilsen
3. Hvis nej: Send til normal AI

**Nodes du skal bruge:**
- Webhook
- IF node
- HTTP Request (OpenRouter) x2
- Respond to Webhook

### Del 2: Multi-rolle Agent

**Byg en agent med 3 roller:**

1. **Lærer-rolle** (triggers: "hjælp", "lektier", "forklaring")
   ```
   Du er Professor Smart, en tålmodig lærer. Du forklarer ting grundigt
   og sikrer eleven forstår før du går videre.
   ```

2. **Ven-rolle** (triggers: "snakke", "keder", "trist")
   ```
   Du er BestBuddy, en lyttende ven. Du er empatisk og støttende.
   Du stiller spørgsmål for at forstå bedre.
   ```

3. **Quiz-master rolle** (triggers: "quiz", "test", "spørgsmål")
   ```
   Du er QuizWiz, en energisk quiz-master. Du laver sjove quizzer
   og spørgsmål for at teste viden på en underholdende måde.
   ```

### Del 3: Sentiment Analysis

Lad agenten detektere brugerens humør og reagere passende:

**Positive ord:** glad, dejligt, fedt, super
→ Vær entusiastisk og bekræft glæden

**Negative ord:** trist, træt, sur, dårlig
→ Vær støttende og opmuntrende

**Neutrale ord:** alt andet
→ Vær normal og hjælpsom

## 📝 Opgaver

### Opgave 1: Byg en IF Chain

Opret en workflow med mindst 3 IF nodes i kæde der checker for:
1. Om bruger spørger om vejret
2. Om bruger spørger om tiden
3. Else: Normal AI respons

### Opgave 2: Switch Between Roles

Implementer en agent der kan være:
- Historielærer
- Matematik lærer
- Dansk lærer

Baseret på hvilket fag brugeren nævner.

### Opgave 3: Sentiment-aware Agent

Byg en agent der:
- Detekterer positive emojis (😊, 😄, 🎉) → Vær glad
- Detekterer negative emojis (😢, 😞, 😟) → Vær støttende
- Ingen emojis → Vær neutral

### Opgave 4: Morgen vs. Aften Personlighed

Lad agenten opføre sig forskelligt baseret på tidspunktet:
- Morgen (6-12): Energisk og motiverende
- Eftermiddag (12-18): Produktiv og fokuseret
- Aften (18-24): Afslappet og rolig
- Nat (24-6): Påmind om at sove! 😴

## 🎨 Avanceret: Multi-layer Decisions

Kombiner flere betingelser:

```
Check 1: Hvilket emne? (historie, matematik, dansk)
  ↓
Check 2: Hvor svært? (let, mellem, svær)
  ↓
Check 3: Brugerens humør? (glad, trist, frustreret)
  ↓
Vælg perfekt kombination af rolle + tone + kompleksitet
```

## 💡 Best Practices

### DO:
✅ Start med simple betingelser og byg kompleksitet gradvist
✅ Test alle betingelser grundigt
✅ Giv clear fallback når ingen betingelser matcher
✅ Dokumenter hvad hver rolle gør
✅ Hold roller konsistente

### DON'T:
❌ Gør betingelser for komplekse fra start
❌ Glem fallback cases
❌ Lav overlappende betingelser
❌ Skift roller midt i en samtale uden grund
❌ Lav for mange roller (start med 2-3)

## 🔍 Debugging Tips

Når din workflow ikke virker som forventet:

1. **Check IF betingelser:**
   - Er de i den rigtige rækkefølge?
   - Matcher værdierne det du forventer?

2. **Test hver rolle separat:**
   - Virker hver rolle for sig selv?

3. **Log data mellem nodes:**
   - Brug "Code" nodes til at logge hvad der sker

4. **Test edge cases:**
   - Hvad hvis bruger skriver stort/småt?
   - Hvad hvis der er stavefejl?

## 📖 Eksempel: Complete Multi-role Workflow

Se `n8n-workflows/multi-role-agent.json` for et komplet eksempel med:
- Webhook trigger
- Switch node for role detection
- Separate prompts for hver rolle
- Fallback handling
- Response formatering

## ✅ Tjekliste

Før du går videre til Uge 3, sørg for at du kan:
- [ ] Bruge IF nodes i n8n
- [ ] Bruge Switch nodes til multiple betingelser
- [ ] Implementere forskellige agent-roller
- [ ] Detektere bruger-intent fra text
- [ ] Kombinere betingelser for kompleks logik
- [ ] Debug workflows med betingelser

## ⏭ Næste Uge

I [Uge 3: Hukommelse](../uge3-hukommelse/README.md) lærer du at give din agent langtidshukommelse med Supabase - så den kan huske brugere og tidligere samtaler!

---

**Nu bliver din agent virkelig smart! 🧠✨**
