# Bokning IdéVerket

Webbsida för bokning av butiksplatser, avtal och mässtillfällen för UF-företag. Sidan körs som GitHub Pages direkt från detta repo.

- **Alla kan titta.** Sidan är publik och visar alltid den senaste bokningen.
- **Bara de med redigeringslösenord kan ändra.** Lösenordet delas ut av den som ansvarar för bokningen.
- **Datan ligger i repot.** Allt sparas i `data.json`. Varje ändring blir en commit, så historiken går att se och återställa under *Commits* på GitHub.

## Filer

| Fil | Innehåll |
| --- | --- |
| `index.html` | Själva sidan (ingen byggprocess, ett enda HTML-dokument) |
| `data.json` | Företag, bokningar, mässtillfällen och butikskod |
| `auth.json` | Krypterad GitHub-token som låses upp med redigeringslösenordet. Skapas av sidan under *Inställningar*. |
| `logo.webp` | Logotyp |

## Komma igång (görs en gång av ägaren)

1. **Slå på GitHub Pages.** Gå till *Settings → Pages* i repot. Under *Build and deployment* väljer du *Source: Deploy from a branch*, gren `main`, mapp `/ (root)`. Spara. Efter någon minut finns sidan på `https://fjellandermedia.github.io/ideverket/`.
2. **Skapa en GitHub-token** som sidan får skriva med. Gå till *Settings (ditt konto) → Developer settings → Personal access tokens → Fine-grained tokens → Generate new token*.
   - *Repository access:* Only select repositories → `ideverket`.
   - *Permissions → Repository permissions → Contents:* **Read and write**. Inget annat.
   - Välj en giltighetstid. När den går ut skapar du en ny token och sätter om lösenordet (steg 3).
   - Kopiera token, den visas bara en gång.
3. **Sätt redigeringslösenordet.** Öppna sidan, gå till *Inställningar → Redigeringslösenord → Sätt eller byt lösenord*. Klistra in token, välj ett lösenord (minst 12 tecken, gärna en fras) och spara. Sidan krypterar token med lösenordet och lägger resultatet i `auth.json` i repot.
4. **Dela lösenordet** med dem som ska kunna ändra i bokningen. De loggar in via knappen *Logga in för att redigera*.

## Så fungerar lagringen

- Besökare läser `data.json` direkt från repot via `raw.githubusercontent.com` (uppdateras inom några minuter) med GitHub Pages som reserv.
- Inloggade läser och skriver via GitHub:s API med den upplåsta token. Varje sparning blir en commit på `main` med ett beskrivande meddelande, till exempel `Boka vecka 46 plats 2: Din Röst UF`.
- Sidan hämtar nya ändringar var 60:e sekund och när fliken blir aktiv igen. Om två personer sparar samtidigt upptäcks det, sidan laddar om den senaste versionen och ber den ena göra om sin ändring.
- Ingen bokningsdata lagras i webbläsaren. Det enda som kan sparas lokalt är inloggningen, och bara om man kryssar i *Kom ihåg mig på den här enheten*.

## Så fungerar skyddet

- `auth.json` innehåller token krypterad med AES-256-GCM. Nyckeln härleds ur lösenordet med PBKDF2-SHA256 (600 000 varv). Token finns aldrig i klartext i repot.
- Filen är publik, så någon kan i teorin gissa lösenord offline. Därför ska lösenordet vara långt. En mening på några ord räcker gott.
- **Byta lösenord:** *Inställningar → Sätt eller byt lösenord*. Det kräver att du har själva token, så den som bara har lösenordet kan inte byta det.
- **Stänga ute alla direkt:** återkalla token under *Developer settings* på GitHub. Då slutar det gamla lösenordet fungera omedelbart. Skapa en ny token och sätt ett nytt lösenord.
- Token ger bara skrivrätt till innehållet i detta repo, inget annat på ditt GitHub-konto.

## Ändra repo eller gren

Högst upp i `<script>` i `index.html` finns `CONFIG` med `owner`, `repo`, `branch` och filnamn. Ändra där om sidan flyttas.
