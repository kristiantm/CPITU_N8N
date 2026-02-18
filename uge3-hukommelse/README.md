# Uge 3: Hukommelse (Memory) 🧠

Velkommen til uge 3! Nu får din agent langtidshukommelse med Supabase - den kan huske brugere, præferencer og tidligere samtaler.

## 🎯 Læringsmål

Efter denne uge kan du:
- Integrere Supabase med n8n
- Gemme og hente brugerdata
- Implementere samtale-historik
- Bygge en agent der husker kontekst
- Forstå database-design for agents

## 📚 Teori

### Hvorfor Hukommelse?

En agent uden hukommelse er som at møde en person med hukommelsestab - hver samtale starter forfra!

**Med hukommelse kan din agent:**
- Huske brugerens navn og præferencer
- Henvise til tidligere samtaler
- Lære fra interaktioner
- Tilpasse sig brugerens behov over tid

### Typer af Hukommelse

1. **Session Memory**: Hukommelse under én samtale
2. **Short-term Memory**: Sidste par samtaler
3. **Long-term Memory**: Permanent lagring i database
4. **Semantic Memory**: Fakta og viden
5. **Episodic Memory**: Specifikke hændelser

### Supabase Setup

Supabase er en open-source Firebase-alternativ bygget på PostgreSQL.

**Hvad vi skal bruge:**
- Users table: Gem brugerinformation
- Conversations table: Gem samtaler
- Memories table: Gem vigtig kontekst

## 🗄 Database Design

### Users Table

```sql
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  username VARCHAR(255) UNIQUE NOT NULL,
  name VARCHAR(255),
  preferences JSONB,
  created_at TIMESTAMP DEFAULT NOW(),
  last_seen TIMESTAMP DEFAULT NOW()
);
```

### Conversations Table

```sql
CREATE TABLE conversations (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES users(id),
  message TEXT NOT NULL,
  response TEXT NOT NULL,
  role VARCHAR(50),
  created_at TIMESTAMP DEFAULT NOW()
);
```

### Memories Table

```sql
CREATE TABLE memories (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES users(id),
  memory_type VARCHAR(50),
  content TEXT NOT NULL,
  importance INT DEFAULT 5,
  created_at TIMESTAMP DEFAULT NOW()
);
```

## 🛠 Supabase i n8n

### Metode 1: HTTP Request (REST API)

**Hent bruger:**
```
GET https://[project-ref].supabase.co/rest/v1/users?username=eq.john
Headers:
  - apikey: [your-anon-key]
  - Authorization: Bearer [your-anon-key]
```

**Opret bruger:**
```
POST https://[project-ref].supabase.co/rest/v1/users
Headers:
  - apikey: [your-anon-key]
  - Authorization: Bearer [your-anon-key]
  - Content-Type: application/json
Body:
{
  "username": "john",
  "name": "John Doe",
  "preferences": {"theme": "dark"}
}
```

### Metode 2: Supabase Node (hvis tilgængelig)

n8n har en Supabase node der gør det nemmere:
- Select
- Insert
- Update
- Delete

## 🔨 Praktisk Workshop

### Del 1: Setup Supabase

1. Gå til [supabase.com](https://supabase.com)
2. Opret et nyt projekt
3. I SQL Editor, kør database scripts (se ovenfor)
4. Kopier din API keys til `.env`

### Del 2: Byg Memory-enabled Agent

**Workflow:**
1. Webhook modtager besked + username
2. Check om bruger eksisterer (Supabase SELECT)
3. Hvis ikke, opret ny bruger (Supabase INSERT)
4. Hent sidste 5 samtaler (Context)
5. Send til OpenRouter med historie
6. Gem ny samtale (Supabase INSERT)
7. Respond

### Del 3: Konversations-historik

Giv agenten adgang til tidligere samtaler:

```javascript
// I en Code node
const conversations = $input.all();
let context = "Tidligere samtaler:\n";

conversations.forEach(conv => {
  context += `Bruger: ${conv.json.message}\n`;
  context += `Agent: ${conv.json.response}\n\n`;
});

return { context };
```

Tilføj til system prompt:
```
{{ $json.context }}

Baseret på ovenstående historik, fortsæt samtalen naturligt.
```

## 📝 Opgaver

### Opgave 1: Simpel User Lookup

Byg en workflow der:
1. Modtager username
2. Checker om user eksisterer i Supabase
3. Hvis ja: Returnér bruger info
4. Hvis nej: Opret ny bruger

### Opgave 2: Gem Præferencer

Lad brugere gemme præferencer:
- Foretrukken sprog
- Respons længde (kort/lang)
- Foretrukken tone
- Emner de interesserer sig for

Agent skal bruge disse præferencer i svar.

### Opgave 3: Samtale-historik

Implementer:
- Gem hver samtale i database
- Hent sidste 3 samtaler
- Inkluder i context til AI
- Test at agenten husker tidligere snakke

### Opgave 4: Smart Memories

Lad agenten identificere og gemme vigtig information:
- Brugerens navn
- Brugerens interesser
- Vigtige datoer (fødselsdag, etc.)
- Præferencer

**Bonus:** Brug AI til at ekstrahere key facts fra samtaler.

## 🎨 Avanceret Features

### Feature 1: Conversation Summary

Efter hver 10. besked, generer et summary:

```javascript
// Prompt til summary
`Sammenfat følgende 10 samtaler i 2-3 bullets:
${conversations.map(c => `- ${c.message}: ${c.response}`).join('\n')}

Fokuser på:
- Vigtige informationer om brugeren
- Beslutninger der blev taget
- Handlings-punkter
`
```

### Feature 2: Memory Retrieval

Når bruger spørger "hvad snakkede vi om sidst?":
- Hent relevante samtaler
- Sammenfat dem
- Præsenter på en struktureret måde

### Feature 3: Adaptive Personality

Lad agentens personlighed tilpasse sig baseret på brugerens præferencer:
- Hvis bruger liker jokes → Vær sjovere
- Hvis bruger vil have fakta → Vær mere præcis
- Hvis bruger er trist ofte → Vær mere støttende

## 💡 Best Practices

### DO:
✅ Altid verificer bruger før gemning
✅ Gem timestamps på alle data
✅ Implementer error handling for database fejl
✅ Begræns hvor meget historik du sender til AI (cost!)
✅ Anonymiser sensitive data

### DON'T:
❌ Gem passwords i plain text
❌ Send hele database til AI hver gang
❌ Glem at handle database errors
❌ Gem PII uden brugerens vidende
❌ Ignorer GDPR hvis du har europæiske brugere

## 🔒 Privacy & Security

**Vigtige overvejelser:**
- Informer brugere om hvad du gemmer
- Tillad brugere at slette deres data
- Krypter følsomt data
- Brug Row Level Security (RLS) i Supabase
- Log aldrig API keys

**Supabase RLS Eksempel:**
```sql
ALTER TABLE conversations ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users can only see their own conversations"
ON conversations FOR SELECT
USING (auth.uid() = user_id);
```

## 📖 Eksempel Workflows

### 1. Memory-enabled Chat
Se `n8n-workflows/memory-chat.json`

### 2. User Profile Manager
Se `n8n-workflows/user-profile.json`

### 3. Conversation History
Se `n8n-workflows/conversation-history.json`

## 🧪 Test Din Implementation

Test disse scenarier:
1. Ny bruger første gang
2. Returnerende bruger
3. Gem præferencer
4. Hent præferencer
5. Lang samtale med kontekst
6. Bruger spørger om tidligere samtale

## ✅ Tjekliste

Før du går videre til Uge 4, sørg for at du kan:
- [ ] Oprette forbindelse til Supabase fra n8n
- [ ] Gemme brugerdata i database
- [ ] Hente data fra database
- [ ] Implementere samtale-historik
- [ ] Bruge context i AI prompts
- [ ] Handle database errors gracefully

## ⏭ Næste Uge

I [Uge 4: Værktøjer](../uge4-værktøjer/README.md) lærer du at give din agent superkræfter ved at forbinde den med eksterne APIs, web scraping og mere!

---

**Din agent er nu meget smartere med hukommelse! 🧠✨**
