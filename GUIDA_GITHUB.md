# Pubblicare il gestionale su GitHub Pages — guida passo passo

Obiettivo: avere il gestionale a un indirizzo fisso tipo `https://tuonome.github.io/asta-mantra/`, così tu e il tuo socio aprite sempre la versione aggiornata e **i dati salvati non si perdono più** (era il problema del file scaricato: ogni copia nuova = percorso diverso = dati azzerati).

Tempo: ~15 minuti la prima volta. Poi ogni aggiornamento è trascinare un file.

---

## 1. Crea il repository

1. Vai su **github.com** → pulsante **New** (o `github.com/new`)
2. **Repository name**: `asta-mantra`
3. Lascia **Public** (necessario per GitHub Pages gratuito — la sicurezza la mettiamo al punto 4)
4. Spunta **Add a README file**
5. **Create repository**

## 2. Carica il gestionale

1. Nel repository appena creato: **Add file → Upload files**
2. Trascina `gestionale-asta-fantacalcio.html`
3. **Importante**: rinominalo in **`index.html`** — così l'indirizzo sarà pulito, senza il nome del file in fondo.
   Puoi farlo prima di caricarlo (rinomina il file sul Mac) oppure dopo, dalla matita di GitHub.
4. In fondo alla pagina: **Commit changes**

## 3. Attiva GitHub Pages

1. Nel repository: **Settings** (in alto a destra)
2. Menu a sinistra: **Pages**
3. Sotto *Build and deployment* → **Source**: `Deploy from a branch`
4. **Branch**: `main`, cartella `/ (root)` → **Save**
5. Aspetta 1-2 minuti, ricarica la pagina: comparirà in alto
   **"Your site is live at https://tuonome.github.io/asta-mantra/"**

Quello è l'indirizzo da salvare tra i preferiti e da mandare al tuo socio.

## 4. Autorizza il dominio su Firebase — PASSAGGIO OBBLIGATORIO

Senza questo, il login non funzionerà dal sito (Firebase blocca i domini non autorizzati).

1. **console.firebase.google.com** → progetto `mantraenna26-27`
2. **Build → Authentication → Settings** (scheda in alto)
3. Sezione **Authorized domains** → **Add domain**
4. Inserisci: **`tuonome.github.io`** (solo il dominio, senza `https://` e senza `/asta-mantra`)
5. Salva

## 5. Blinda la stanza ai vostri due account

Il repository è pubblico, quindi chiunque potrebbe trovare l'indirizzo e crearsi un account. Con queste regole solo i vostri due utenti possono leggere e scrivere.

**Prima ricava i vostri identificativi:**
1. Firebase → **Build → Authentication → Users**
2. Copia la colonna **User UID** delle due righe (tua e del tuo socio): sono stringhe tipo `k3Jd8sPqR2XyZ...`

**Poi imposta le regole:**
1. Firebase → **Realtime Database → Regole**
2. Sostituisci tutto con questo, mettendo i due UID al posto di `UID_PIETRO` e `UID_SOCIO`:

```json
{
  "rules": {
    "rooms": {
      "$room": {
        ".read": "auth != null && (auth.uid === 'UID_PIETRO' || auth.uid === 'UID_SOCIO')",
        ".write": "auth != null && (auth.uid === 'UID_PIETRO' || auth.uid === 'UID_SOCIO')"
      }
    }
  }
}
```

3. **Pubblica**

Da questo momento, anche se qualcuno trovasse il sito e si registrasse, non vedrebbe nulla della vostra asta.

> Se in futuro entra un terzo socio: aggiungi il suo UID nella lista, separato da un altro `|| auth.uid === '...'`.

## 6. Prova finale

1. Apri `https://tuonome.github.io/asta-mantra/`
2. Vai in **Impostazioni → Sincronizzazione condivisa**: configurazione e stanza sono **già compilate**
3. Metti email e password → **Accedi**
4. Chiudi il browser, riapri il link: devi ritrovarti già connesso

---

## Come aggiornare il gestionale in futuro

1. Repository → clicca su **`index.html`** → icona **matita** (Edit) → oppure **Add file → Upload files** e ricarica il nuovo file con lo stesso nome
2. **Commit changes**
3. Dopo ~1 minuto il sito è aggiornato per tutti

Se un aggiornamento rompe qualcosa: scheda **Commits**, apri quello precedente, **Revert**. Torni indietro senza perdere nulla.

---

## Cosa cambia rispetto a prima

| | File scaricato | GitHub Pages |
|---|---|---|
| Versione aggiornata | da riscaricare e ridistribuire | automatica per tutti |
| Dati salvati | persi ad ogni riscaricamento | stabili nel tempo |
| Configurazione Firebase | da incollare a mano | già integrata |
| Da telefono | va trasferito il file | basta aprire il link |
| Errori | si torna indietro a mano | Revert con un clic |

**Cosa non cambia**: Firebase resta necessario per la sincronizzazione in tempo reale (GitHub ospita solo pagine, non fa da database), e il login va comunque fatto una volta per browser.
