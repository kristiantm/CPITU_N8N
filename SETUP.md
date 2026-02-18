# Setup Guide - CPITU AI Agent Kursus

Denne guide hjælper dig med at sætte dit udviklingsmiljø op til kurset.

## 📋 Forudsætninger

Før du starter, skal du have følgende installeret:

- **Docker Desktop** (Windows/Mac) eller **Docker Engine** (Linux)
- **Git** til versionskontrol
- En **teksteditor** (VS Code anbefales)
- **Node.js** (v18 eller nyere) - valgfrit, men nyttigt

## 🔧 Installation

### 1. Docker Setup

#### Windows/Mac:
1. Download og installer [Docker Desktop](https://www.docker.com/products/docker-desktop/)
2. Start Docker Desktop
3. Verificer installation:
```bash
docker --version
docker-compose --version
```

#### Linux:
```bash
# Ubuntu/Debian
sudo apt-get update
sudo apt-get install docker.io docker-compose
sudo systemctl start docker
sudo systemctl enable docker

# Tilføj din bruger til docker gruppen
sudo usermod -aG docker $USER
```

### 2. Clone Repository

```bash
git clone https://github.com/kristiantm/CPITU_N8N.git
cd CPITU_N8N
```

### 3. Opsæt Environment Variables

Kopier `.env.example` til `.env` og udfyld dine nøgler:

```bash
cp .env.example .env
```

Rediger `.env` filen:

```env
# OpenRouter API Key
OPENROUTER_API_KEY=your_openrouter_key_here

# Supabase
SUPABASE_URL=your_supabase_url
SUPABASE_ANON_KEY=your_supabase_anon_key
SUPABASE_SERVICE_KEY=your_supabase_service_key

# Discord (valgfrit)
DISCORD_BOT_TOKEN=your_discord_bot_token
DISCORD_CHANNEL_ID=your_channel_id

# Slack (valgfrit)
SLACK_BOT_TOKEN=your_slack_bot_token
SLACK_CHANNEL_ID=your_channel_id

# n8n
N8N_ENCRYPTION_KEY=a_random_encryption_key
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=choose_a_strong_password
```

### 4. Få API Nøgler

#### OpenRouter
1. Gå til [OpenRouter.ai](https://openrouter.ai/)
2. Opret en konto
3. Naviger til API Keys
4. Opret en ny API key
5. Kopiér nøglen til din `.env` fil

#### Supabase
1. Gå til [Supabase.com](https://supabase.com/)
2. Opret et nyt projekt
3. Under Project Settings → API:
   - Kopiér `URL` til `SUPABASE_URL`
   - Kopiér `anon public` key til `SUPABASE_ANON_KEY`
   - Kopiér `service_role` key til `SUPABASE_SERVICE_KEY`

#### Discord (Valgfrit)
1. Gå til [Discord Developer Portal](https://discord.com/developers/applications)
2. Klik "New Application"
3. Naviger til "Bot" og klik "Add Bot"
4. Kopiér bot token
5. Under OAuth2 → URL Generator:
   - Vælg `bot` scope
   - Vælg permissions: Send Messages, Read Messages
   - Brug URL'en til at invitere botten til din server

### 5. Start Docker Containers

```bash
# Start alle services
docker-compose up -d

# Tjek status
docker-compose ps

# Se logs
docker-compose logs -f n8n
```

### 6. Åbn n8n

1. Åbn din browser
2. Gå til `http://localhost:5678`
3. Log ind med credentials fra `.env` filen
4. Du er nu klar til at bygge workflows!

## 🧪 Verificer Setup

Test at alt virker:

```bash
# Test Docker
docker ps

# Test n8n er kørende
curl http://localhost:5678

# Test Supabase forbindelse (fra uge 3)
# Vi vil teste dette senere når vi når til uge 3
```

## 🔍 Troubleshooting

### Docker starter ikke
- Sørg for Docker Desktop kører (Windows/Mac)
- Tjek at Docker service kører: `sudo systemctl status docker` (Linux)

### Port 5678 er optaget
- Stop andre services der bruger porten
- Eller ændr porten i `docker-compose.yml`

### n8n kan ikke tilgås
- Tjek at containeren kører: `docker ps`
- Se logs for fejl: `docker-compose logs n8n`
- Prøv at genstarte: `docker-compose restart n8n`

### API nøgler virker ikke
- Verificer at du har kopieret dem korrekt til `.env`
- Tjek at der ikke er mellemrum før/efter nøglerne
- Genstart containers efter ændringer: `docker-compose restart`

## 📚 Næste Skridt

Nu hvor dit miljø er sat op, er du klar til [Uge 1: Personlighed & Prompts](uge1-personlighed-prompts/README.md)!

## 💡 Tips

- Gem dine API nøgler sikkert - del dem aldrig offentligt
- Commit aldrig `.env` filen til Git
- Tag backup af dine n8n workflows regelmæssigt
- Brug Docker Desktop's GUI til nem management (Windows/Mac)

## 🆘 Brug for Hjælp?

- Tjek [FAQ](FAQ.md)
- Spørg i Discord community
- Kontakt kursuslederen
