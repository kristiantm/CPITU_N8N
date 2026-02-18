# Uge 1: Personlighed & Prompts 🎭

Velkommen til den første uge! I denne uge lærer du at give din AI-agent en unik personlighed og arbejde med prompts.

## 🎯 Læringsmål

Efter denne uge kan du:
- Forstå hvordan prompts styrer AI-adfærd
- Designe en karakterfuld agent-personlighed
- Opsætte n8n workflows med OpenRouter
- Teste og forbedre prompt quality

## 📚 Teori

### Hvad er en Prompt?

En prompt er den instruktion du giver til en AI. Det er som at give en skuespiller en rolle at spille!

**Eksempel på en simpel prompt:**
```
Du er en hjælpsom assistent.
```

**Eksempel på en detaljeret prompt:**
```
Du er RoboBuddy, en venlig robot-assistent designet til at hjælpe børn med lektier.
Du er altid positiv, tålmodig og forklarer ting på en sjov måde.
Du bruger emojis og korte sætninger.
Når børn spørger om noget, giver du først et enkelt svar og spørger derefter om de vil vide mere.
```

### Prompt Engineering Principper

1. **Vær specifik**: Jo mere detaljeret, jo bedre resultat
2. **Giv kontekst**: Forklar situationen og formålet
3. **Sæt tone**: Venlig? Professionel? Sjov?
4. **Giv eksempler**: Vis hvordan agenten skal svare
5. **Definer begrænsninger**: Hvad skal agenten IKKE gøre?

### Personligheds-dimensioner

Når du designer din agent, tænk over:

- **Navn**: Hvad hedder din agent?
- **Rolle**: Hvad er agentens formål?
- **Tone**: Formel, casual, sjov, professionel?
- **Viden**: Hvilket område er agenten ekspert i?
- **Begrænsninger**: Hvad kan/må agenten ikke?
- **Stil**: Lange eller korte svar? Bruger agenten emojis?

## 🛠 Praktisk: Din Første n8n Workflow

### Trin 1: Åbn n8n

1. Start Docker containers: `docker-compose up -d`
2. Gå til `http://localhost:5678`
3. Log ind med dine credentials fra `.env`

### Trin 2: Opret en Ny Workflow

1. Klik på "New workflow"
2. Giv den et navn: "Min Første AI Agent"

### Trin 3: Tilføj en Webhook Trigger

1. Klik på "+" for at tilføje en node
2. Søg efter "Webhook"
3. Vælg "Webhook" node
4. Konfiguration:
   - HTTP Method: POST
   - Path: `chat`
   - Respond: "Using 'Respond to Webhook' Node"

### Trin 4: Tilføj HTTP Request til OpenRouter

1. Tilføj en ny node
2. Søg efter "HTTP Request"
3. Konfiguration:
   - Method: POST
   - URL: `https://openrouter.ai/api/v1/chat/completions`
   - Authentication: Generic Credential Type
     - Add a new credential:
       - Header Auth
       - Name: Authorization
       - Value: `Bearer {{$env.OPENROUTER_API_KEY}}`
       - Name: HTTP-Referer (optional)
       - Value: `http://localhost:5678`
   - Send Body: true
   - Body Content Type: JSON
   - Specify Body: Using JSON
   - JSON Body:
   ```json
   {
     "model": "openai/gpt-3.5-turbo",
     "messages": [
       {
         "role": "system",
         "content": "Du er RoboBuddy, en venlig robot-assistent til børn. Du er positiv, bruger emojis 🤖 og forklarer ting på en sjov måde!"
       },
       {
         "role": "user",
         "content": "{{$json.body.message}}"
       }
     ]
   }
   ```

### Trin 5: Tilføj Response Node

1. Tilføj en "Respond to Webhook" node
2. Konfiguration:
   - Response Code: 200
   - Response Body:
   ```json
   {
     "response": "={{$json.choices[0].message.content}}"
   }
   ```

### Trin 6: Test Din Workflow

1. Klik "Save" for at gemme
2. Klik "Execute Workflow"
3. Test med:
```bash
curl -X POST http://localhost:5678/webhook/chat \
  -H "Content-Type: application/json" \
  -d '{"message": "Hej! Hvad hedder du?"}'
```

## 📝 Opgaver

### Opgave 1: Design Din Agent-personlighed

Udfyld denne skabelon for din agent:

```
Agent Navn: _______________
Rolle: _______________
Målgruppe: _______________
Tone (1-3 stikord): _______________
Særlige egenskaber: _______________
Ting agenten IKKE må gøre: _______________
```

### Opgave 2: Skriv Dit Første System Prompt

Baseret på din personlighed, skriv et detaljeret system prompt (mindst 5 linjer).

**Eksempel:**
```
Du er TechTeacher, en entusiastisk teknologi-lærer for teenagere.
Du er lidenskabelig omkring teknologi og ønsker at inspirere unge til at lære at kode.
Du bruger moderne sprog og relaterer til ting teenagere kender (gaming, sociale medier, etc.).
Du giver altid praktiske eksempler og opmuntrer eleverne til at eksperimentere.
Når nogen er frustreret, minder du dem om at alle programmører møder fejl - det er en del af læringen!
```

### Opgave 3: Test og Forbedre

1. Implementer dit prompt i din n8n workflow
2. Test med 5 forskellige spørgsmål
3. Noter hvad virker godt og hvad der kan forbedres
4. Justér dit prompt baseret på resultaterne

### Opgave 4: Forskellige Personligheder

Opret 3 forskellige agenter med forskellige personligheder:
1. En sjov comedian-robot
2. En seriøs professor
3. En venlig babysitter

Test dem alle og se forskellen!

## 🎨 Kreativ Udfordring

Design din "drømme-agent" - hvad ville den perfekte assistent til dig være? Tænk stort!

Overvej:
- Hvad ville den hjælpe dig med?
- Hvordan ville den tale til dig?
- Hvilke superkræfter skulle den have?

## 💡 Tips & Tricks

- **Start simpelt**: Begynd med en kort prompt og byg ovenpå
- **Vær konsistent**: Hvis agenten skal være venlig, hold den tone hele tiden
- **Test, test, test**: Prøv mange forskellige inputs
- **Husk kontekst**: Forklar HVEM agenten er, ikke kun HVAD den skal gøre
- **Brug eksempler**: Vis agenten hvordan du vil have svar

## 🚀 Bonus: Avancerede Prompts

Prøv at tilføje disse elementer til dit prompt:

```
Du skal følge disse regler:
1. Hold altid dine svar under 3 sætninger
2. Stil altid et opfølgende spørgsmål
3. Brug mindst ét emoji i hvert svar
4. Hvis du ikke ved noget, sig det ærligt i stedet for at gætte
```

## 📖 Yderligere Ressourcer

- [OpenRouter Documentation](https://openrouter.ai/docs)
- [Prompt Engineering Guide](https://www.promptingguide.ai/)
- [n8n Documentation](https://docs.n8n.io/)

## ✅ Check Din Forståelse

Før du går videre til Uge 2, sørg for at du kan:
- [ ] Oprette en n8n workflow
- [ ] Integrere med OpenRouter
- [ ] Skrive et effektivt system prompt
- [ ] Teste og iterere på dit prompt
- [ ] Forklare hvordan prompts påvirker AI-adfærd

## ⏭ Næste Uge

I [Uge 2: Betingelser & Roller](../uge2-betingelser-roller/README.md) lærer du at give din agent intelligens til at træffe beslutninger baseret på forskellige situationer!

---

**Held og lykke med din første AI-agent! 🤖✨**
