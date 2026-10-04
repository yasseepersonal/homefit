# HomeFit Vibe

Losse app (geen server nodig). Werkt offline zodra je hem één keer online hebt geopend.

## Publiceren op GitHub Pages
1. Maak op github.com een nieuwe repository (bv. `homefit`).
2. Upload alle bestanden uit deze map (`index.html`, `sw.js`, `manifest.webmanifest`, `icon-192.png`, `icon-512.png`).
3. Ga naar Settings → Pages → Source: *Deploy from a branch* → `main` / `(root)` → Save.
4. Na ± 1 minuut staat je app op `https://JOUWNAAM.github.io/homefit/`.

## Op je gsm
Open de link, daarna: iPhone (Safari) deelknop → *Zet op beginscherm*; Android (Chrome) menu → *App installeren*. Open hem één keer met internet; daarna werkt hij ook offline.

## Nieuwe workouts toevoegen
**Via een Claude-chat (gratis):** plak in een chat de transcriptie van de video en deze prompt, kopieer het JSON-antwoord en plak het in de app bij *Workout toevoegen*:

> Bouw deze YouTube-workout zo exact mogelijk na (volgorde, tijden/herhalingen, rondes, rust; schrijf rondes uit). Ik heb alleen een mat en 2 dumbbells van 3 kg. Kies per oefening een illustratie-id ("lib") uit: plank, taps, inchworm, bicycle, bridge, legraise, birddog, kicks, lunge, rdl, squat, goblet, calf, press, row, front, tri. Antwoord ALLEEN met JSON: {"title":"..","desc":"1 zin","note":"afwijkingen","defs":{"a":{"lib":"id","n":"naam","s":"beginpositie","f":"uitvoering","db":0,"uni":0}},"seq":[{"e":"a","sec":40},{"rest":15},{"e":"a","reps":12}]}

**Direct in de app:** vul bij *Instellingen* je Anthropic API-key in (alleen op je telefoon opgeslagen; stel een bestedingslimiet in bij Anthropic). Daarna werkt *Maak met AI*.

## Sync tussen apparaten met Supabase (optioneel)
1. Maak een gratis project op supabase.com en voer dit uit in de *SQL editor*:
```sql
create table hf_state(code text primary key, data jsonb not null, updated_at timestamptz default now());
alter table hf_state enable row level security;
create function hf_get(p_code text) returns jsonb language sql security definer set search_path=public as $$ select data from hf_state where code=p_code $$;
create function hf_set(p_code text, p_data jsonb) returns void language sql security definer set search_path=public as $$ insert into hf_state(code,data) values(p_code,p_data) on conflict(code) do update set data=excluded.data, updated_at=now() $$;
grant execute on function hf_get(text), hf_set(text,jsonb) to anon;
```
2. Kopieer *Project URL* en de *publishable key* (sb_publishable_..., Settings → API Keys; nooit de secret key) naar *Instellingen* in de app, druk op *Maak sync-code* en sla op.
3. Zet op je andere apparaten dezelfde URL, key en sync-code. De sync-code is je "wachtwoord": deel hem niet.

## Back-up
*Instellingen → Download* bewaart al je workouts en sessies als bestand; *Laad bestand* zet ze terug.
