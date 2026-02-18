# FAQ - Hyppigt Stillede Spørgsmål

## Generelle Spørgsmål

### Q: Hvad er dette kursus?
A: Dette er et 7-ugers progressivt kursus hvor du bygger én intelligent AI chatbot-agent fra bunden ved hjælp af Docker, n8n, OpenRouter, Supabase og Discord/Slack.

### Q: Hvem er kurset til?
A: Kurset er designet til børn og unge med grundlæggende interesse for teknologi. Du behøver ikke at kunne programmere på forhånd, men det hjælper.

### Q: Hvor lang tid tager hver uge?
A: Hver uge indeholder 3-5 timers materiale inklusiv teori, praksis og opgaver. Du kan arbejde i dit eget tempo.

### Q: Koster det noget?
A: Kursusmaterialet er gratis! Du skal dog bruge:
- OpenRouter API key (kan starte gratis, små costs senere)
- Supabase (gratis tier er mere end nok)
- Discord/Slack (gratis)
- Server til deployment (gratis tiers findes på Railway, Render, etc.)

## Setup & Installation

### Q: Hvilke computer specs skal jeg bruge?
A: Næsten enhver moderne computer kan bruges:
- 4GB RAM minimum (8GB anbefales)
- 10GB fri diskplads
- Windows 10+, macOS 10.15+, eller Linux

### Q: Docker virker ikke på min computer
A: Prøv:
1. Sørg for virtualization er aktiveret i BIOS
2. På Windows: Installer WSL2 først
3. Genstart computeren efter installation
4. Check Docker Desktop kører i system tray

### Q: Jeg kan ikke tilgå n8n på localhost:5678
A: Check:
1. Er Docker container kørende? Kør `docker ps`
2. Er porten optaget? Prøv at ændre port i docker-compose.yml
3. Check firewall settings
4. Prøv `http://127.0.0.1:5678` i stedet

### Q: Hvor får jeg API keys?
A: Se [SETUP.md](SETUP.md) for detaljerede instruktioner for hver service.

## n8n Spørgsmål

### Q: Hvordan gemmer jeg min workflow?
A: Klik "Save" knappen øverst til højre i n8n interface. Giv din workflow et beskrivende navn.

### Q: Kan jeg eksportere/importere workflows?
A: Ja! I workflow menu, vælg "Download" for at eksportere som JSON. Brug "Import from File" for at importere.

### Q: Min workflow virker ikke - hvordan debugger jeg?
A: 
1. Klik "Execute Workflow" for at se fejl
2. Check hver node's output (klik på node)
3. Brug "Test step" på individuelle nodes
4. Check environment variables er sat korrekt
5. Se logs med `docker-compose logs n8n`

### Q: Hvordan tilføjer jeg environment variables?
A: Tilføj dem i `.env` filen, derefter genstart containers:
```bash
docker-compose down
docker-compose up -d
```

## AI & OpenRouter

### Q: Hvilken AI model skal jeg bruge?
A: For start:
- **Billig & Hurtig**: `openai/gpt-3.5-turbo`
- **Bedre kvalitet**: `openai/gpt-4`
- **Budget**: `anthropic/claude-instant`
- **Dansk optimeret**: GPT-4 er bedst til dansk

### Q: Mine API calls er for dyre!
A: Tips til at reducere costs:
1. Brug billigere models til simple opgaver
2. Implementer caching
3. Begræns samtale-historik længde
4. Brug kortere system prompts
5. Batch requests når muligt

### Q: AI'en svarer forkert - hvad gør jeg?
A: 
1. Forbedre dit system prompt (vær mere specifik)
2. Giv flere eksempler i promptet
3. Prøv en stærkere model
4. Verificer at du sender korrekt context

### Q: Hvordan får jeg AI til at svare på dansk?
A: Tilføj til dit system prompt:
```
Du skal ALTID svare på dansk.
```
Eller send beskeder på dansk - AI matcher ofte sproget.

## Supabase & Database

### Q: Hvordan opretter jeg tabeller i Supabase?
A: 
1. Gå til din Supabase project
2. Klik på "SQL Editor" i sidebar
3. Indsæt SQL CREATE TABLE statements
4. Klik "Run"

### Q: Kan ikke forbinde til Supabase fra n8n
A: Check:
1. Er dine API keys korrekte i `.env`?
2. Er din Supabase project aktiv?
3. Prøv at teste connection direkte med curl:
```bash
curl https://your-project.supabase.co/rest/v1/ \
  -H "apikey: your-anon-key"
```

### Q: Hvordan sletter jeg data fra Supabase?
A: I Supabase dashboard:
1. Gå til "Table Editor"
2. Vælg table
3. Select rows
4. Klik delete

Eller via SQL:
```sql
DELETE FROM table_name WHERE condition;
```

## Discord/Slack Integration

### Q: Min Discord bot svarer ikke
A: Check:
1. Er bot invited til server?
2. Har botten de rette permissions?
3. Er bot token korrekt i `.env`?
4. Er webhook/workflow aktiv i n8n?

### Q: Hvordan inviterer jeg bot til min Discord server?
A: 
1. Gå til Discord Developer Portal
2. Vælg din application
3. OAuth2 → URL Generator
4. Vælg `bot` scope og permissions
5. Kopier URL og åbn i browser
6. Vælg server at invitere til

### Q: Kan jeg bruge både Discord OG Slack?
A: Ja! Du kan have separate workflows for hver, eller én workflow der håndterer begge.

## Fejlfinding

### Q: "Permission denied" fejl
A: På Linux/Mac, du skal måske tilføje din user til docker group:
```bash
sudo usermod -aG docker $USER
```
Log ud og ind igen.

### Q: "Port already in use" fejl
A: En anden service bruger porten. Find og stop den:
```bash
# Find hvilken process bruger port 5678
lsof -i :5678
# Eller på Windows
netstat -ano | findstr :5678
```

### Q: Workflows kører langsomt
A: 
1. Check din internet forbindelse
2. Reducer samtale-historik længde
3. Brug hurtigere AI models
4. Optimér database queries
5. Check Docker resource limits

### Q: "Out of memory" fejl
A: Docker kan løbe tør for memory. Øg limits:
```yaml
# I docker-compose.yml under n8n service
deploy:
  resources:
    limits:
      memory: 2G
```

## Kursus Progression

### Q: Skal jeg gennemføre ugerne i rækkefølge?
A: Ja! Hver uge bygger videre på den forrige. Start med Uge 1.

### Q: Jeg er hængt fast på en opgave - hvad nu?
A: 
1. Check løsningerne i `/losninger/` mappen
2. Gennemgå teorien igen
3. Prøv at lave en simplere version først
4. Spørg i community Discord

### Q: Kan jeg skippe en uge?
A: Det anbefales ikke, da koncepterne bygger på hinanden. Men hvis du allerede kender materialet, kan du skimme igennem.

### Q: Hvor lang tid tager hele kurset?
A: 7 uger hvis du følger en uge ad gangen. Men du kan arbejde hurtigere eller langsommere efter behov.

## Deployment

### Q: Hvor skal jeg deploye min agent?
A: Populære options:
- **Railway**: Nemt, gratis tier
- **Render**: God free tier
- **Fly.io**: Fleksibel
- **DigitalOcean**: Hvis du vil have fuld kontrol
- **n8n Cloud**: Managed n8n (betalt)

### Q: Koster det at deploye?
A: Mange platforme har gratis tiers:
- Railway: $5 free credit/måned
- Render: 750 timer free/måned
- Fly.io: Generous free tier

For hobby projekter er gratis tiers ofte nok.

### Q: Hvordan opdaterer jeg min deployed agent?
A: Afhænger af platform, men generelt:
1. Push ændringer til Git
2. Platform auto-deployer (hvis konfigureret)
3. Eller kør deploy command

## Sikkerhed & Privacy

### Q: Er det sikkert at dele min API key?
A: **NEJ!** Aldrig del API keys offentligt. De giver adgang til dine betalte services.

### Q: Hvad hvis jeg accidentelt committer min .env fil til Git?
A: 
1. Skift alle API keys STRAKS
2. Fjern .env fra git history:
```bash
git rm --cached .env
git commit -m "Remove .env"
```
3. Sørg for `.env` er i `.gitignore`

### Q: Må jeg gemme bruger beskeder?
A: Vær opmærksom på GDPR:
1. Informer brugere om data storage
2. Få consent
3. Tillad brugere at slette deres data
4. Anonymiser hvis muligt

## Avancerede Spørgsmål

### Q: Kan jeg bruge andre AI providers end OpenRouter?
A: Ja! Du kan kalde:
- OpenAI direkte
- Anthropic (Claude)
- Cohere
- Hugging Face
- Lokal models (Ollama, LM Studio)

### Q: Kan jeg træne min egen AI model?
A: Det er uden for scope af dette kursus, men ja - du kan fine-tune modeller. Det kræver mere avanceret viden og kan være dyrt.

### Q: Hvordan skalerer jeg til mange brugere?
A: 
1. Brug database connection pooling
2. Implementer rate limiting
3. Cache aggresivt
4. Brug message queues
5. Overvej Kubernetes for orchestration

### Q: Kan jeg monitere min agent's performance?
A: Ja! Implementer:
1. Structured logging
2. Metrics collection (Prometheus)
3. Error tracking (Sentry)
4. APM tools (New Relic, DataDog)

## Community & Support

### Q: Hvor kan jeg få hjælp?
A: 
1. Check denne FAQ
2. Læs dokumentationen igen
3. Se løsninger i repo
4. Spørg i community Discord
5. Åbn et GitHub issue

### Q: Kan jeg bidrage til kurset?
A: Absolut! Pull requests er velkomne for:
- Fejlrettelser
- Nye eksempler
- Oversættelser
- Forbedringer af dokumentation

### Q: Hvor kan jeg dele mit projekt?
A: 
- GitHub (link i README)
- Community Discord
- Social media med #CPITU_N8N
- Blog posts

## Næste Skridt

### Q: Jeg er færdig med kurset - hvad nu?
A: 
1. Byg et rigtigt projekt folk kan bruge
2. Lær LangChain for advanced patterns
3. Eksperimenter med vector databases
4. Lær om agent evaluation & testing
5. Del din viden - undervs andre!

### Q: Hvilke projekter kan jeg bygge?
A: Idéer:
- Customer support bot til en hjemmeside
- Study buddy til elever
- Personal productivity assistant
- News aggregator & summarizer
- Social media manager
- Code review assistant
- Recipe suggestion bot
- Travel planning assistant
- Og meget mere!

---

**Har du et spørgsmål der ikke er besvaret her?**
Åbn et GitHub issue eller spørg i community Discord!
