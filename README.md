# Daglige-PE-nyheder
Sender nyhederne hver dag kl 9
# Dansk Private Equity – daglig konkurrence-agent

Sender dig hver dag kl. 09:00 en mail med den seneste konkurrencesituation blandt
danske kapitalfonde. Agenten henter de sidste 24 timers nyheder fra Google News
og lader Claude skrive en kort, forretningsorienteret briefing.

## Hvad du får i mailen
- **Overblik** over dagens vigtigste bevægelser
- Grupperet under **Opkøb & transaktioner, Exits & frasalg, Fundraising, Personalia, Marked & makro**
- **"Hvad det betyder for konkurrencen"** – kort vurdering af styrkeforholdet mellem fondene
- Links til alle kilder

---

## Hvad du skal bruge
1. En afsender-mail med SMTP (fx en Gmail-konto + *app-adgangskode*).
2. *(Valgfrit, men anbefalet)* en Anthropic API-nøgle fra https://console.anthropic.com
   – uden den får du en ren nyhedsliste i stedet for en analyseret briefing.

### Gmail app-adgangskode (hvis du bruger Gmail)
Slå 2-trinsbekræftelse til → Google-konto → Sikkerhed → *App passwords* → opret én →
brug den 16-tegns kode som `SMTP_PASS`. (Din normale adgangskode virker ikke.)

---

## Mulighed A – Kør i skyen gratis (anbefalet, ingen maskine tændt)

GitHub kører agenten for dig kl. 09:00 dansk tid – også når din computer er slukket.

1. Lav et **privat** GitHub-repo og læg disse filer i det:
   `dansk_pe_agent.py`, `requirements.txt`, og
   `daily-pe-brief.yml` placeret i mappen `.github/workflows/`.
2. I repoet: **Settings → Secrets and variables → Actions → New repository secret**.
   Opret:
   - `MAIL_TO` – din modtager-mail
   - `SMTP_USER` – din afsender-mail
   - `SMTP_PASS` – app-adgangskoden
   - `ANTHROPIC_API_KEY` – din Claude-nøgle (spring over hvis du ikke vil bruge analyse)
3. Gå til fanen **Actions** og kør workflowet manuelt én gang (*Run workflow*) for at teste.
   Herefter kører den selv hver morgen.

> Bruger du Gmail/Outlook? Så virker det out of the box. Andre udbydere: sæt
> `SMTP_HOST`/`SMTP_PORT` som ekstra secrets.

---

## Mulighed B – Kør lokalt på din egen Mac/Linux

```bash
pip install -r requirements.txt
cp .env.example .env          # udfyld dine værdier
set -a; source .env; set +a   # indlæs miljøvariabler
python dansk_pe_agent.py      # test-kør med det samme
```

Planlæg kl. 09:00 hver dag med cron (`crontab -e`):

```
0 9 * * *  cd /sti/til/mappen && set -a && . ./.env && set +a && /usr/bin/python3 dansk_pe_agent.py >> pe.log 2>&1
```

(Cron bruger maskinens lokale tid, så `0 9` rammer 09:00 dansk tid direkte. Maskinen
skal være tændt på tidspunktet – derfor er Mulighed A ofte nemmere.)

---

## Tilpasning
Åbn `dansk_pe_agent.py` og rediger øverst:
- **`FIRMS`** – listen af fonde der følges (tilføj/fjern frit)
- **`TOPIC_QUERIES`** – bredere emnesøgninger
- **`LOOKBACK_HOURS`** – tidsvindue (default 24)
- **`CLAUDE_MODEL`** – `claude-sonnet-4-6` (default) eller `claude-haiku-4-5-20251001` (billigere)

## Bemærk
- Google News RSS er gratis, men dækningen afhænger af hvad der indekseres. De vigtigste
  danske kilder (KapitalWatch, Børsen, InsideBusiness, Finans) er ofte bag betalingsmur –
  agenten ser overskrift og link, ikke nødvendigvis hele artiklen.
- Vil du have dybere dækning, kan kilderne udvides med fx betalte nyheds-API'er eller
  RSS-feeds du selv abonnerer på. Sig til, så hjælper jeg med det.
