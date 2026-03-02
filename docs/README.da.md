# CC-Viewer

Claude Code anmodningsovervågningssystem, der i realtid fanger og visualiserer alle API-anmodninger og -svar fra Claude Code (rå tekst, uden censur). Giver udviklere mulighed for at overvåge deres kontekst og gennemgå samt fejlfinde under Vibe Coding.

[English](../README.md) | [繁體中文](./README.zh-TW.md) | [简体中文](./README.zh.md) | [한국어](./README.ko.md) | [日本語](./README.ja.md) | [Deutsch](./README.de.md) | [Español](./README.es.md) | [Français](./README.fr.md) | [Italiano](./README.it.md) | Dansk | [Polski](./README.pl.md) | [Русский](./README.ru.md) | [العربية](./README.ar.md) | [Norsk](./README.no.md) | [Português (Brasil)](./README.pt-BR.md) | [ไทย](./README.th.md) | [Türkçe](./README.tr.md) | [Українська](./README.uk.md)

## Anvendelse

### Installation

```bash
npm install -g cc-viewer
```

### Kørsel og automatisk konfiguration

```bash
ccv
```

Denne kommando registrerer automatisk den lokale Claude Code-installationsmetode (NPM eller Native Install) og tilpasser sig derefter.

- **NPM-installation**: Injicerer automatisk et interceptorscript i Claude Codes `cli.js`.
- **Native Install**: Registrerer automatisk `claude`-binærfilen, konfigurerer en lokal transparent proxy og opsætter en Zsh Shell Hook til automatisk trafikvideresendelses.

### Konfigurationstilsidesættelse (Configuration Override)

Hvis du har brug for at bruge et brugerdefineret API-endpoint (f.eks. en virksomhedsproxy), skal du blot konfigurere det i `~/.claude/settings.json` eller angive miljøvariablen `ANTHROPIC_BASE_URL`. `ccv` genkender det automatisk og videresender anmodninger korrekt.

### Lydløs tilstand (Silent Mode)

Som standard kører `ccv` i lydløs tilstand, når det omslutter `claude`, hvilket sikrer at din terminaloutput forbliver ren og konsistent med den native oplevelse. Alle logfiler fanges i baggrunden og kan ses via `http://localhost:7008`.

Når konfigurationen er fuldført, bruges `claude`-kommandoen som normalt. Besøg `http://localhost:7008` for at se overvågningsgrænsefladen.

### Fejlfinding (Troubleshooting)

Hvis du oplever problemer med at starte, er der en ultimativ fejlfindingsløsning:
Trin 1: Åbn Claude Code i en vilkårlig mappe;
Trin 2: Giv Claude Code følgende instruktion:
```
Jeg har installeret npm-pakken cc-viewer, men efter at have kørt ccv fungerer den stadig ikke korrekt. Undersøg cc-viewers cli.js og findcc.js, og tilpas til den lokale Claude Code-installationsmetode baseret på det specifikke miljø. Begræns ændringerne så vidt muligt til findcc.js.
```
At lade Claude Code selv undersøge fejlen er mere effektivt end at spørge nogen eller læse dokumentation!

Når ovenstående instruktion er udført, opdateres findcc.js. Hvis dit projekt ofte kræver lokal installation, eller hvis forket kode ofte skal løse installationsproblemer, kan du beholde denne fil og kopiere den direkte næste gang. På nuværende tidspunkt bruger mange projekter og virksomheder Claude Code ikke på Mac, men via serverhosting, så forfatteren har udskilt findcc.js for at gøre det nemmere at følge cc-viewer-kildekodeopdateringer fremover.

### Afinstallation

```bash
ccv --uninstall
```

### Tjek version

```bash
ccv --version
```

## Funktioner

### Anmodningsovervågning (rå teksttilstand)
<img width="1500" height="720" alt="image" src="https://github.com/user-attachments/assets/519dd496-68bd-4e76-84d7-2a3d14ae3f61" />
- Fanger i realtid alle API-anmodninger sendt af Claude Code og sikrer, at det er den rå tekst og ikke censurerede logfiler (dette er vigtigt!!!)
- Genkender og markerer automatisk Main Agent- og Sub Agent-anmodninger (undertyper: Bash, Task, Plan, General)
- MainAgent-anmodninger understøtter Body Diff JSON, der viser forskelle fra den forrige MainAgent-anmodning i sammenfoldet visning (viser kun ændrede/nye felter)
- Inline token-forbrugsstatistik for hver anmodning (input/output tokens, cache-oprettelse/-læsning, hit-rate)
- Kompatibel med Claude Code Router (CCR) og andre proxyscenarier — matcher anmodninger via API-stimønster som fallback

### Samtaletilstand

Klik på knappen "Samtaletilstand" øverst til højre for at parse Main Agents komplette samtalehistorik som en chatgrænseflade:
<img width="1500" height="730" alt="image" src="https://github.com/user-attachments/assets/c973f142-748b-403f-b2b7-31a5d81e33e6" />


- Visning af Agent Team understøttes endnu ikke
- Brugermeddelelser er højrejusterede (blå bobler), Main Agent-svar er venstrejusterede (mørke bobler)
- `thinking`-blokke er som standard sammenfoldet, renderet i Markdown, klik for at udvide og se tankeprocessen; understøtter oversættelse med ét klik (funktionen er endnu ikke stabil)
- Brugervalgsmeddelelser (AskUserQuestion) vises i spørgsmål-og-svar-format
- Tovejs tilstandssynkronisering: skifter automatisk til den valgte anmodnings tilsvarende samtale ved skift til samtaletilstand; vender automatisk tilbage til den valgte anmodning ved skift tilbage til rå teksttilstand
- Indstillingspanel: kan skifte standardsammenfoldet tilstand for værktøjsresultater og thinking-blokke
- Mobil chat-gennemse: I mobil CLI-tilstand kan du trykke på knappen "Gennemse chat" i topbjælken for at skubbe en skrivebeskyttet chatvisning ind og gennemse den fulde samtalehistorik på din telefon


### Statistikværktøj

Det svævende "Datastatistik"-panel i header-området:
<img width="1500" height="729" alt="image" src="https://github.com/user-attachments/assets/b23f9a81-fc3d-4937-9700-e70d84e4e5ce" />

- Viser antal cache-oprettelser/-læsninger og cache hit-rate
- Cache-genopbygningsstatistik: viser antal og cache_creation tokens grupperet efter årsag (TTL, system/tools/model-ændringer, beskedafskæring/-ændring, nøgleændring)
- Værktøjsanvendelsesstatistik: viser kaldsfrekvens for hvert værktøj sorteret efter antal kald
- Skill-anvendelsesstatistik: viser kaldsfrekvens for hver Skill sorteret efter antal kald
- Koncepthjælp (?)-ikon: klik for at se indbygget dokumentation for MainAgent, CacheRebuild og hvert værktøj

### Logadministration

Via CC-Viewer-rullemenuen øverst til venstre:
<img width="1200" height="672" alt="image" src="https://github.com/user-attachments/assets/8cf24f5b-9450-4790-b781-0cd074cd3b39" />

- Importer lokale logfiler: gennemse historiske logfiler, grupperet efter projekt, åbner i nyt vindue
- Indlæs lokal JSONL-fil: vælg og indlæs en lokal `.jsonl`-fil direkte (understøtter op til 500MB)
- Gem nuværende log som: download den aktuelle overvågnings-JSONL-logfil
- Flet logfiler: kombiner flere JSONL-logfiler til én session for samlet analyse
- Se bruger-Prompts: udtræk og vis alle brugerinput med tre visningstilstande — Rå teksttilstand (råt indhold), Konteksttilstand (systemtags kan foldes), Teksttilstand (kun ren tekst); slash-kommandoer (`/model`, `/context` osv.) vises som selvstændige poster; kommandorelaterede tags skjules automatisk fra Prompt-indholdet
- Eksporter Prompt til TXT: eksporter brugerprompter (kun tekst, uden systemtags) til en lokal `.txt`-fil

### Flersproget understøttelse

CC-Viewer understøtter 18 sprog og skifter automatisk baseret på systemets sprogindstilling:

简体中文 | English | 繁體中文 | 한국어 | Deutsch | Español | Français | Italiano | Dansk | 日本語 | Polski | Русский | العربية | Norsk | Português (Brasil) | ไทย | Türkçe | Українська

### Automatisk opdatering

CC-Viewer tjekker automatisk for opdateringer ved opstart (højst én gang hver 4. time). Inden for samme hovedversion (f.eks. 1.x.x → 1.y.z) anvendes opdateringer automatisk og træder i kraft ved næste genstart. Ændringer af hovedversion viser kun en notifikation.

Automatisk opdatering følger Claude Codes globale konfiguration i `~/.claude/settings.json`. Hvis Claude Code har deaktiveret automatiske opdateringer (`autoUpdates: false`), springer CC-Viewer også automatiske opdateringer over.

## License

MIT
