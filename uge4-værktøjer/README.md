# Uge 4: Værktøjer (HTTP/API/Scraping) 🛠

Velkommen til uge 4! Nu lærer du at give din agent superkræfter ved at forbinde den med omverdenen gennem APIs og værktøjer.

## 🎯 Læringsmål

Efter denne uge kan du:
- Integrere eksterne APIs i din agent
- Implementere web scraping
- Bygge tool-calling funktionalitet
- Håndtere API authentication
- Parse og præsentere data fra APIs

## 📚 Teori

### Hvad er Tool Calling?

Tool calling gør det muligt for din agent at udføre handlinger i den virkelige verden:
- Søge på internettet
- Få vejrdata
- Sende emails
- Oprette calendar events
- Hente priser fra webshops
- Og meget mere!

### Agent + Tools = Superkræfter

En agent med værktøjer kan:
1. **Forstå** hvad brugeren vil
2. **Beslutte** hvilket værktøj der skal bruges
3. **Udføre** API kald
4. **Fortolke** resultatet
5. **Svare** med struktureret information

### Typer af Værktøjer

1. **Weather APIs**: Vejrprognoser
2. **News APIs**: Seneste nyheder
3. **Search APIs**: Google, Bing, etc.
4. **Social Media APIs**: Twitter, Reddit, etc.
5. **Custom APIs**: Din egen backend
6. **Web Scraping**: Hent data direkte fra websites

## 🌐 Populære APIs til Agenter

### 1. OpenWeatherMap (Vejr)
```
GET https://api.openweathermap.org/data/2.5/weather?q=Copenhagen&appid=YOUR_KEY
```

### 2. NewsAPI (Nyheder)
```
GET https://newsapi.org/v2/top-headlines?country=dk&apiKey=YOUR_KEY
```

### 3. REST Countries (Lande information)
```
GET https://restcountries.com/v3.1/name/denmark
```

### 4. JokeAPI (Vittigheder)
```
GET https://v2.jokeapi.dev/joke/Any
```

## 🔨 Implementer Tool Calling

### Metode 1: Function Calling (OpenAI/OpenRouter)

Moderne AI models understøtter function calling:

```json
{
  "model": "openai/gpt-4",
  "messages": [...],
  "tools": [
    {
      "type": "function",
      "function": {
        "name": "get_weather",
        "description": "Get current weather in a location",
        "parameters": {
          "type": "object",
          "properties": {
            "location": {
              "type": "string",
              "description": "City name"
            }
          },
          "required": ["location"]
        }
      }
    }
  ]
}
```

### Metode 2: Intent Detection → Tool Execution

1. Detekter brugerens intent
2. Ekstraher parametre
3. Kald relevant API
4. Format og returner data

**Eksempel Workflow:**
```
User: "Hvad er vejret i København?"
  ↓
Intent Detection: "weather_query"
  ↓
Extract Parameters: location="København"
  ↓
Call Weather API
  ↓
Format Response: "Det er 15°C og delvist skyet i København"
```

## 🕷 Web Scraping

### HTML Node i n8n

n8n har en indbygget HTML ekstraktor:

```
1. HTTP Request til website
2. HTML Extract node
3. Vælg CSS selectors for data du vil have
4. Process data
```

**Eksempel - Scrape DR.dk overskrifter:**
```
URL: https://www.dr.dk/nyheder
Selector: .dre-teaser-title__link
```

### Best Practices for Scraping

✅ Respekter robots.txt
✅ Implementer rate limiting
✅ Cache resultater
✅ Handle errors gracefully
❌ Overload ikke websites
❌ Scrape ikke copyrighted content without permission

## 📝 Opgaver

### Opgave 1: Vejr Agent

Byg en agent der kan svare på vejr-spørgsmål:
- "Hvad er vejret i København?"
- "Skal jeg tage en paraply med i dag?"
- "Bliver det varmt i morgen?"

**Steps:**
1. Tilmeld dig OpenWeatherMap (gratis tier)
2. Detekter når bruger spørger om vejr
3. Ekstraher by-navn
4. Kald Weather API
5. Præsenter data på en venlig måde

### Opgave 2: News Agent

Lav en agent der kan fortælle seneste nyheder:
- "Hvad sker der i verden?"
- "Giv mig dagens top nyheder"
- "Hvad er de seneste nyheder om teknologi?"

### Opgave 3: Multi-tool Agent

Byg en agent med mindst 3 værktøjer:
- Vejr
- Nyheder
- Vitser (JokeAPI)

Agenten skal kunne beslutte hvilket værktøj der skal bruges.

### Opgave 4: Web Scraper

Byg et værktøj der scraper data fra en website:
- Priser fra en webshop
- Overskrifter fra en news site
- Events fra en event kalender

## 🎨 Avanceret: Tool Chain

Kombiner flere værktøjer i én opgave:

**Eksempel: "Planlæg min dag i København"**
1. Hent vejret (Weather API)
2. Find events i dag (Event API/Scraping)
3. Find restauranter (Google Places API)
4. Generer plan baseret på data

## 🔧 Fejlhåndtering

Når APIs fejler:

```javascript
// I en Code node
try {
  const response = await fetch(apiUrl);
  if (!response.ok) {
    return {
      json: {
        error: true,
        message: "API returned error",
        fallback: "Jeg kunne ikke hente data lige nu. Prøv igen senere."
      }
    };
  }
  return { json: await response.json() };
} catch (error) {
  return {
    json: {
      error: true,
      message: error.message,
      fallback: "Noget gik galt. Prøv igen senere."
    }
  };
}
```

## 💡 Best Practices

### API Integration:
✅ Altid gem API keys som environment variables
✅ Implementer retry logic
✅ Cache data hvor muligt
✅ Respekter rate limits
✅ Handle errors gracefully

### Tool Selection:
✅ Vælg rigtige værktøjer til opgaven
✅ Fallback til generel AI hvis intet værktøj passer
✅ Kombiner værktøjer intelligent
✅ Verificer data før præsentation

### DON'T:
❌ Hardcode API keys i workflows
❌ Kald APIs uden rate limiting
❌ Ignorer API errors
❌ Scrape uden at checke robots.txt
❌ Overload external services

## 📖 Eksempel Workflows

### 1. Weather Tool
Se `n8n-workflows/weather-tool.json`

### 2. News Aggregator
Se `n8n-workflows/news-tool.json`

### 3. Multi-tool Agent
Se `n8n-workflows/multi-tool-agent.json`

## 🧪 Test Scenarios

Test din agent med:
1. Simpelt værktøjs-kald
2. Værktøj med parametre
3. Flere værktøjer efter hinanden
4. API fejl simulation
5. Invalid parametre
6. Rate limit håndtering

## ✅ Tjekliste

Før du går videre til Uge 5:
- [ ] Integrere mindst 3 eksterne APIs
- [ ] Implementere tool selection logic
- [ ] Håndtere API errors
- [ ] Parse og formater API responses
- [ ] Forstå authentication (API keys, OAuth)
- [ ] Implementere web scraping (optional)

## ⏭ Næste Uge

I [Uge 5: Multi-step Reasoning](../uge5-reasoning/README.md) lærer du at implementere kompleks beslutningslogik hvor agenten kan tænke i flere trin!

---

**Din agent har nu adgang til hele verden! 🌍✨**
