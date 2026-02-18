# Quick Start Guide - Kom i Gang på 15 Minutter! ⚡

Denne guide får dig i gang med det basale så hurtigt som muligt.

## ⚡ Express Setup (15 minutter)

### Step 1: Installer Docker (5 min)

**Mac/Windows:**
1. Download [Docker Desktop](https://www.docker.com/products/docker-desktop/)
2. Installer og start programmet
3. Vent til Docker kører (grøn ikon i taskbar)

**Linux:**
```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

### Step 2: Clone Repository (1 min)

```bash
git clone https://github.com/kristiantm/CPITU_N8N.git
cd CPITU_N8N
```

### Step 3: Setup Environment (3 min)

```bash
# Kopier environment template
cp .env.example .env

# Rediger .env filen (brug din favorit editor)
nano .env  # eller code .env eller vim .env
```

**Minimum configuration:**
```env
# For at starte hurtigt, brug disse placeholder values
N8N_ENCRYPTION_KEY=min32karakterlangencryptionkey123
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=changeme123

# API keys kan tilføjes senere
OPENROUTER_API_KEY=sk-or-your-key-here
```

### Step 4: Start n8n (2 min)

```bash
docker-compose up -d
```

Vent til containers er klar (~1-2 min første gang)

```bash
# Check status
docker-compose ps
```

### Step 5: Åbn n8n (1 min)

1. Åbn browser
2. Gå til: `http://localhost:5678`
3. Log ind:
   - Username: `admin`
   - Password: `changeme123` (eller hvad du satte)

🎉 **Du er nu klar!**

### Step 6: Test Dit Setup (3 min)

1. I n8n, klik "Add workflow"
2. Klik "+" og søg efter "Schedule Trigger"
3. Tilføj en "Schedule" node
4. Klik "+" igen og tilføj en "Code" node
5. I Code node, indsæt:
```javascript
return {
  json: {
    message: "Hello from n8n!",
    time: new Date().toISOString()
  }
};
```
6. Forbind noderne
7. Klik "Execute Workflow"
8. Se output i Code node

✅ **Virker det? Fantastisk! Du er klar til Uge 1!**

## 🚀 Næste Skridt

### Få OpenRouter API Key (5 min - gør det senere)

1. Gå til [OpenRouter.ai](https://openrouter.ai/)
2. Sign up (gratis)
3. Gå til Keys section
4. Opret en ny key
5. Kopier til din `.env` fil under `OPENROUTER_API_KEY`
6. Genstart n8n: `docker-compose restart n8n`

### Start Uge 1 Lektion

Nu er du klar til at starte med rigtige AI workflows!

Gå til: [Uge 1: Personlighed & Prompts](uge1-personlighed-prompts/README.md)

## 🐛 Troubleshooting Express

### Docker starter ikke
```bash
# Check Docker kører
docker ps

# Hvis fejl, genstart Docker Desktop
# Eller på Linux:
sudo systemctl restart docker
```

### Port 5678 optaget
Rediger `docker-compose.yml` og ændr:
```yaml
ports:
  - "5679:5678"  # Brug port 5679 i stedet
```

Derefter: `http://localhost:5679`

### Kan ikke tilgå n8n
```bash
# Check logs
docker-compose logs n8n

# Genstart alt
docker-compose down
docker-compose up -d
```

## 📚 Fuld Setup

Har du brug for mere detaljeret setup (Supabase, Discord, etc.)?

Se [SETUP.md](SETUP.md) for komplet guide.

## ❓ Hurtig FAQ

**Q: Skal jeg have alle API keys nu?**
A: Nej! Start med bare n8n. Tilføj OpenRouter i Uge 1, Supabase i Uge 3, etc.

**Q: Koster det noget?**
A: Docker og n8n er gratis. OpenRouter har en gratis tier til at starte.

**Q: Hvor lang tid tager kurset?**
A: ~4-6 timer per uge i 7 uger. Men arbejd i dit eget tempo!

**Q: Jeg er stuck!**
A: Check [FAQ.md](FAQ.md) eller spørg i Discord community.

## 🎯 Opsummering

```
✅ Docker installeret
✅ Repository clonet  
✅ Environment sat op
✅ n8n kørende på localhost:5678
✅ Test workflow virker
→ Klar til Uge 1!
```

**Held og lykke! 🚀**

Har du problemer? Se [SETUP.md](SETUP.md) eller [FAQ.md](FAQ.md)
