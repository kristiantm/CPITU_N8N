# Uge 7: Deployment & Dokumentation 🚀

Velkommen til den sidste uge! Nu lærer du at deploye din agent til produktion og dokumentere den professionelt.

## 🎯 Læringsmål

Efter denne uge kan du:
- Deploye din agent til produktion
- Integrere med Discord/Slack
- Skrive god dokumentation
- Implementere monitoring og logging
- Håndtere scaling og performance
- Sikre din agent

## 📚 Deployment Options

### Option 1: Self-hosted (Docker)

**Fordele:**
- Fuld kontrol
- Ingen eksterne costs (ud over hosting)
- Data privacy

**Ulemper:**
- Kræver server maintenance
- Du er ansvarlig for uptime

### Option 2: Cloud Platforms

**n8n Cloud:**
- Managed n8n instance
- Automatisk scaling
- Built-in backup

**Railway/Render/Fly.io:**
- Easy deployment fra Git
- Auto-scaling
- Free tiers available

### Option 3: Kubernetes

For enterprise/high-scale:
- Full orchestration
- Auto-scaling
- High availability

## 🐳 Docker Production Setup

### Production docker-compose.yml

```yaml
version: '3.8'

services:
  n8n:
    image: n8nio/n8n:latest
    restart: always
    ports:
      - "5678:5678"
    environment:
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=${N8N_USER}
      - N8N_BASIC_AUTH_PASSWORD=${N8N_PASSWORD}
      - N8N_ENCRYPTION_KEY=${N8N_ENCRYPTION_KEY}
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_HOST=postgres
      - DB_POSTGRESDB_DATABASE=n8n
      - DB_POSTGRESDB_USER=${POSTGRES_USER}
      - DB_POSTGRESDB_PASSWORD=${POSTGRES_PASSWORD}
      - WEBHOOK_URL=${WEBHOOK_URL}
      - GENERIC_TIMEZONE=Europe/Copenhagen
    volumes:
      - n8n_data:/home/node/.n8n
    depends_on:
      - postgres
    networks:
      - n8n-network

  postgres:
    image: postgres:15-alpine
    restart: always
    environment:
      - POSTGRES_USER=${POSTGRES_USER}
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
      - POSTGRES_DB=n8n
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - n8n-network

  # Nginx reverse proxy (optional)
  nginx:
    image: nginx:alpine
    restart: always
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./ssl:/etc/nginx/ssl
    depends_on:
      - n8n
    networks:
      - n8n-network

volumes:
  n8n_data:
  postgres_data:

networks:
  n8n-network:
    driver: bridge
```

## 💬 Discord Integration

### 1. Opret Discord Bot

1. Gå til [Discord Developer Portal](https://discord.com/developers/applications)
2. Klik "New Application"
3. Under "Bot", klik "Add Bot"
4. Kopier bot token
5. Under OAuth2 → URL Generator:
   - Scopes: `bot`, `applications.commands`
   - Permissions: Send Messages, Read Messages, Read Message History
   - Brug URL til at invite bot

### 2. n8n Discord Workflow

```
Discord Trigger (webhook)
  ↓
Extract message & user
  ↓
Your AI Agent Logic
  ↓
Discord API (Send response)
```

**Eksempel Code Node:**
```javascript
// Parse Discord message
const discordData = $input.item.json;
const message = discordData.content;
const userId = discordData.author.id;
const channelId = discordData.channel_id;

return {
  json: {
    message,
    userId,
    channelId,
    username: discordData.author.username
  }
};
```

## 💼 Slack Integration

### 1. Opret Slack App

1. Gå til [Slack API](https://api.slack.com/apps)
2. Klik "Create New App"
3. Vælg "From scratch"
4. Giv navn og vælg workspace
5. Under "OAuth & Permissions":
   - Add scopes: `chat:write`, `channels:history`
   - Install to workspace
6. Kopier Bot Token

### 2. n8n Slack Workflow

n8n har indbygget Slack support:
- Slack Trigger node
- Slack Send Message node

## 📝 Dokumentation

### README.md Struktur

```markdown
# [Agent Navn]

## Beskrivelse
Kort beskrivelse af hvad agenten gør

## Features
- Feature 1
- Feature 2
- Feature 3

## Kom i Gang

### Forudsætninger
- Docker
- OpenRouter API key
- Supabase account (optional)

### Installation
1. Clone repo
2. Kopier .env.example til .env
3. Udfyld API keys
4. Kør `docker-compose up -d`

### Brug
Hvordan man bruger agenten...

## API Reference
Dokumenter dine endpoints

## Konfiguration
Forklar environment variables

## Troubleshooting
Common issues og løsninger

## License
MIT
```

### API Dokumentation

Hvis du exposer APIs, dokumenter dem:

```markdown
## POST /webhook/chat

Chat med agenten.

**Request:**
```json
{
  "message": "Hej, hvad kan du?",
  "userId": "user123",
  "context": {}
}
```

**Response:**
```json
{
  "response": "Hej! Jeg kan hjælpe med...",
  "role": "assistant",
  "timestamp": "2024-..."
}
```
```

## 📊 Monitoring & Logging

### Logging Best Practices

Implementer structured logging:

```javascript
// I en Code node
const logEntry = {
  timestamp: new Date().toISOString(),
  level: 'INFO',
  event: 'user_message',
  userId: $json.userId,
  message: $json.message,
  agentRole: $json.role
};

console.log(JSON.stringify(logEntry));
```

### Metrics to Track

**Usage Metrics:**
- Antal requests per dag
- Response times
- Error rates
- Most used features

**Cost Metrics:**
- API calls til OpenRouter
- Database queries
- Token usage

**Quality Metrics:**
- User satisfaction
- Task completion rate
- Fallback rate (når agent ikke kan svare)

## 🔒 Security

### Checklist

- [ ] Alle API keys i environment variables
- [ ] Rate limiting på endpoints
- [ ] Input validation
- [ ] SQL injection prevention (hvis applicable)
- [ ] HTTPS på alle connections
- [ ] Ingen sensitive data i logs
- [ ] Regular updates af dependencies
- [ ] Access control på n8n interface

### Supabase Row Level Security

```sql
-- Kun brugere kan se deres egen data
CREATE POLICY "Users see own data"
ON conversations
FOR SELECT
USING (auth.uid() = user_id);

-- Kun brugere kan indsætte deres egen data
CREATE POLICY "Users insert own data"
ON conversations
FOR INSERT
WITH CHECK (auth.uid() = user_id);
```

## 🚀 Performance Optimization

### Caching

Implementer caching for frequent requests:

```javascript
// Simple in-memory cache
const cache = {};

if (cache[cacheKey]) {
  return cache[cacheKey]; // Return cached
}

const result = await expensiveOperation();
cache[cacheKey] = result;
return result;
```

### Database Optimization

- Index frequently queried fields
- Limit query results
- Use pagination
- Archive old data

### API Optimization

- Batch requests when possible
- Use cheaper models for simple tasks
- Implement request queuing
- Cache API responses

## 📝 Opgaver

### Opgave 1: Production Deployment

Deploy din agent til en cloud platform:
1. Vælg platform (Railway, Render, etc.)
2. Setup production environment
3. Configure domain (optional)
4. Test thoroughly

### Opgave 2: Discord Bot

Integrer din agent med Discord:
1. Opret Discord bot
2. Implementer message handling
3. Test i en test-server
4. Deploy

### Opgave 3: Komplet Dokumentation

Skriv komplet dokumentation:
- README.md
- API documentation
- Setup guide
- Troubleshooting guide
- Architecture diagram

### Opgave 4: Monitoring Dashboard

Setup basic monitoring:
- Log alle requests
- Track errors
- Measure response times
- Create simple dashboard (kan være Google Sheets)

## 🎓 Final Project

**Opgave: Byg Din Ultimative Agent**

Kombinér alt du har lært:
1. Personality & Prompts (Uge 1)
2. Conditions & Roles (Uge 2)
3. Memory (Uge 3)
4. Tools (Uge 4)
5. Reasoning (Uge 5)
6. Multi-agent (Uge 6)
7. Production Deployment (Uge 7)

**Requirements:**
- [ ] Klar personlighed
- [ ] Mindst 2 forskellige roller
- [ ] Persistent memory (Supabase)
- [ ] Mindst 3 eksterne værktøjer
- [ ] Multi-step reasoning
- [ ] Deployed til produktion
- [ ] Komplet dokumentation
- [ ] Working Discord/Slack integration

## 🎉 Afslutning

### Hvad Nu?

**Videre Læring:**
- LangChain for advanced agent frameworks
- Vector databases (Pinecone, Weaviate)
- Advanced prompt engineering
- Fine-tuning AI models
- Agent evaluation & testing

**Community:**
- n8n Community Forum
- Discord servers for AI builders
- GitHub projects
- Blog om dine erfaringer

### Ressourcer

- [n8n Documentation](https://docs.n8n.io/)
- [OpenRouter Docs](https://openrouter.ai/docs)
- [Supabase Docs](https://supabase.com/docs)
- [Discord.js Guide](https://discordjs.guide/)
- [Prompt Engineering Guide](https://www.promptingguide.ai/)

## ✅ Kursus Completion Checklist

- [ ] Gennemført alle 7 uger
- [ ] Bygget alle ugentlige projekter
- [ ] Løst alle opgaver
- [ ] Deployed final project
- [ ] Skrevet dokumentation
- [ ] Testet i produktion

## 🏆 Certification

Når du har gennemført alt, har du:
- ✅ Bygget en komplet AI agent
- ✅ Mestret n8n workflows
- ✅ Forstået prompt engineering
- ✅ Lært database integration
- ✅ Implementeret multi-agent systemer
- ✅ Deployed til produktion

**Tillykke! Du er nu en AI Agent Developer! 🎓✨**

---

## 📞 Support & Community

Har du spørgsmål eller vil dele dit projekt?
- Join vores Discord
- Del på GitHub
- Tag #CPITU_N8N på sociale medier

**Tak fordi du deltog i kurset! Vi glæder os til at se hvad du bygger! 🚀**
