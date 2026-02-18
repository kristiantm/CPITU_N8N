# Contributing til CPITU_N8N Kurset

Tak fordi du overvejer at bidrage! Dette dokument forklarer hvordan du kan hjælpe med at forbedre kurset.

## 📋 Måder at Bidrage På

### 1. Rapporter Fejl
- Fandt du en fejl i koden?
- Er dokumentationen uklar?
- Virker noget ikke som forventet?

→ Åbn et [GitHub Issue](https://github.com/kristiantm/CPITU_N8N/issues)

### 2. Forbedr Dokumentation
- Ret stavefejl
- Tilføj klarhed
- Oversæt til andre sprog
- Tilføj eksempler

### 3. Tilføj Eksempler
- Nye workflow eksempler
- Use cases
- Best practices
- Real-world projekter

### 4. Opret Nye Opgaver
- Ekstra øvelser
- Udfordrende projekter
- Alternative løsninger

## 🔧 Hvordan Man Bidrager

### Setup Development Environment

```bash
# Fork repo på GitHub først

# Clone dit fork
git clone https://github.com/DIT-BRUGERNAVN/CPITU_N8N.git
cd CPITU_N8N

# Tilføj upstream
git remote add upstream https://github.com/kristiantm/CPITU_N8N.git

# Opret en branch
git checkout -b feature/mit-bidrag
```

### Lav Dine Ændringer

1. Følg eksisterende fil struktur
2. Hold stil konsistent
3. Test dine ændringer grundigt
4. Opdater relevant dokumentation

### Test Dine Ændringer

Hvis du tilføjer workflows:
- Test dem i n8n
- Verificer alle nodes virker
- Check for fejl
- Test med forskellige inputs

Hvis du opdaterer dokumentation:
- Check for stavefejl
- Verificer links virker
- Test code eksempler

### Submit Pull Request

```bash
# Commit dine ændringer
git add .
git commit -m "Beskrivende commit message"

# Push til dit fork
git push origin feature/mit-bidrag
```

Gå til GitHub og opret en Pull Request til main repository.

## 📝 Contribution Guidelines

### Dokumentation

- Skriv på dansk (da) eller engelsk (en)
- Brug clear, simpelt sprog
- Inkluder eksempler hvor relevant
- Følg eksisterende format

### Code/Workflows

- Test grundigt før submission
- Kommenter komplekse dele
- Følg best practices
- Inkluder README for nye workflows

### Commit Messages

Brug beskrivende commit messages:

```
✅ God: "Add weather API integration example to Week 4"
✅ God: "Fix typo in Week 2 README"
✅ God: "Update Supabase setup instructions"

❌ Dårlig: "fix"
❌ Dårlig: "update"
❌ Dårlig: "changes"
```

### Pull Request Template

```markdown
## Beskrivelse
[Beskriv hvad din PR gør]

## Type Ændring
- [ ] Bug fix
- [ ] Ny feature
- [ ] Dokumentation opdatering
- [ ] Workflow eksempel

## Hvordan Testes Det
[Beskriv hvordan man tester ændringerne]

## Checklist
- [ ] Koden er testet
- [ ] Dokumentation er opdateret
- [ ] Commits er beskrivende
- [ ] Følger style guidelines
```

## 🎯 Bidrag Vi Søger

### High Priority
- 🐛 Bug fixes
- 📚 Dokumentation forbedringer
- 🌍 Oversættelser
- ✅ Test coverage

### Medium Priority
- 💡 Nye workflow eksempler
- 🎨 UI/UX forbedringer
- 📊 Dashboard eksempler
- 🔐 Security forbedringer

### Low Priority
- 🎉 Nye features
- 🔧 Refactoring
- ⚡ Performance optimizations

## 💬 Kommunikation

### Hvor Kan Du Spørge?

- **GitHub Issues**: Bugs, feature requests
- **GitHub Discussions**: Generelle spørgsmål
- **Discord**: Realtids hjælp og diskussion

### Code Review Process

1. Du submitter PR
2. Maintainer reviewer inden 7 dage
3. Feedback gives hvis nødvendigt
4. Du opdaterer baseret på feedback
5. PR merges når approved

## 🏆 Anerkendelse

Alle contributors bliver listet i:
- README.md contributors sektion
- Release notes
- Årlig contributors shoutout

## 📜 Code of Conduct

### Vores Standards

✅ **DO:**
- Vær respektfuld og inkluderende
- Vær konstruktiv i feedback
- Hjælp andre
- Fokuser på hvad er bedst for community

❌ **DON'T:**
- Brug uhøfligt eller nedladende sprog
- Troll eller provoke
- Deler personlig information
- Spam

### Rapporter Issues

Hvis du oplever upassende opførsel, kontakt maintainers privat.

## 🔒 Sikkerhed

Hvis du finder en security vulnerability:
1. **Undlad** at åbne et public issue
2. Send email til [maintainer email]
3. Inkluder detaljer om vulnerability
4. Vi responderer inden 48 timer

## 📄 Licens

Ved at bidrage accepterer du at dine bidrag bliver licenseret under samme licens som projektet (MIT License).

## ❓ Spørgsmål?

Har du spørgsmål om bidrag?
- Åbn en GitHub Discussion
- Spørg i Discord
- Tag maintainers i en comment

---

**Tak for at hjælpe med at gøre dette kursus bedre! 🙏**

Vi værdsætter alle bidrag, store som små!
