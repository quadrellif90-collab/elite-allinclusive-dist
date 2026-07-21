# 🏭 Procedura Softwarehouse — Build, Licenza, Distribuzione GitHub

Guida operativa **copia-incolla** per pacchettizzare, licenziare e distribuire un
software desktop Python come farebbe una softwarehouse. Funziona per QUALSIASI
progetto (non solo questo): cambi solo i nomi e i percorsi nei punti contrassegnati
da `<<CAMBIA>>`.

Flusso finale:
1. Sorgenti in repo **privato** (`<nome>-gest`)
2. File di distribuzione + EXE in repo **pubblico** (`<nome>-dist`)
3. App che parte SOLO con chiave (verifica da `licenses.json` pubblico)
4. Updater che scarica l'EXE dal repo pubblico (senza token)
5. 100 chiavi generate, le prime nel file `KEYS_PRIVATE.txt` solo tuo

---

## 0. Prerequisiti (una tantum, su questa macchina)
```bat
pip install pyinstaller customtkinter pandas openpyxl fastapi uvicorn pydantic pillow requests pytest
gh auth login            :: autenticati con il tuo account GitHub (scope: repo)
python --version        :: serve Python 3.9+
```
Verifica che `gh` sia pronto:
```bat
gh auth status
```

---

## 1. Prepara il progetto (nel suo root, es. C:\Dev\MioProgetto)
Crea/verifica questi file (modello già presente in questo repo, copiali e rinomina):

- `appinfo.py`            → cartelle dati utente (`get_data_dir()`)
- `models/license.py`     → sistema licenza (legge `licenses.json` da repo pubblico)
- `updater.py`            → updater esterno (legge `update.json` da repo pubblico)
- `generate_keys.py`      → genera 100 chiavi
- `<<NOME>>.spec`         → PyInstaller spec dell'app (onefile, console=False)
- `<<NOME>>Updater.spec`  → PyInstaller spec dell'updater
- `.gitignore`            → deve contenere:
  ```
  KEYS_PRIVATE.txt
  license.key
  license_cache.json
  licenses.json
  version.txt
  .trial_start
  dist/
  build/
  __pycache__/
  *.db
  ```

> Negli script `license.py` e `updater.py` sostituisci:
> `quadrellif90-collab/elite-allinclusive-dist` → `<<TUO_ACCOUNT>>/<<NOME>>-dist`

---

## 2. Build degli eseguibili
```bat
cd C:\Dev\MioProgetto
pyinstaller <<NOME>>.spec --noconfirm
pyinstaller <<NOME>>Updater.spec --noconfirm
```
Gli EXE appaiono in `dist\`: `<<NOME>>.exe` e `<<NOME>>Updater.exe`.

---

## 3. Verifica (test automatici + avvio)
```bat
:: Test unitari se presenti
python -m pytest -q

:: Verifica sintassi di tutti i sorgenti
for /r %%f in (*.py) do python -m py_compile "%%f"

:: Test avvio updater (deve contattare GitHub senza crash)
dist\<<NOME>>Updater.exe --check
```

---

## 4. Genera le chiavi di licenza (con metadati)
```bat
python generate_keys.py                  :: 100 chiavi, scadenza illimitata
python generate_keys.py --days 365       :: scadenza a 1 anno da oggi
python generate_keys.py --csv clienti.csv :: associa nomi/clienti da CSV
```
Il CSV opzionale ha colonne: `cliente,tipo,scadenza,note`
(tipo: standard|vita|abbonamento; scadenza: YYYY-MM-DD o vuoto=illimitata).

Crea:
- `licenses.json`      → da pubblicare su GitHub (elenco chiavi + metadati: cliente/tipo/scadenza)
- `KEYS_PRIVATE.txt`   → **SOLO TUO** (chiave, cliente, tipo, scadenza, firma), non committare mai

L'app verifica la chiave automaticamente su GitHub all'avvio e legge
cliente/tipo/scadenza. Se scaduta → non si avvia.

---

## 5. Crea i due repo GitHub
```bat
:: Repo PRIVATO: solo sorgenti
gh repo create <<NOME>>-gest --private --description "<<NOME>> - sorgenti"

:: Repo PUBBLICO: distribuzione (licenses.json, update.json, EXE)
gh repo create <<NOME>>-dist --public --description "<<NOME>> - file distribuzione pubblici"
```

---

## 6. Crea update.json (nel root del progetto)
```json
{
  "product": "<<NOME>>",
  "version": "v1.0.0",
  "notes": "Rilascio iniziale.",
  "windows": {
    "exe": "<<NOME>>.exe",
    "updater": "<<NOME>>Updater.exe",
    "release_url": "https://github.com/<<TUO_ACCOUNT>>/<<NOME>>-dist/releases/download/v1.0.0/<<NOME>>.exe",
    "updater_url": "https://github.com/<<TUO_ACCOUNT>>/<<NOME>>-dist/releases/download/v1.0.0/<<NOME>>Updater.exe"
  }
}
```

Formati file licenza:
- `licenses.json` (pubblico): `{"product": "...", "licenses": [{"key","cliente","tipo","scadenza","note"}]}`
- `license.key` (locale, dopo attivazione): `{"key","cliente","tipo","scadenza","activated"}`

---

## 7. Pubblica i file di distribuzione nel repo PUBBLICO
```bat
mkdir C:\Dev\dist_public
copy licenses.json C:\Dev\dist_public\
copy update.json    C:\Dev\dist_public\
cd C:\Dev\dist_public
git init
git config user.name "<<TUO_NOME>>"
git config user.email "<<TUA_EMAIL>>"
git remote add origin https://github.com/<<TUO_ACCOUNT>>/<<NOME>>-dist.git
git add -A
git commit -m "Distribuzione pubblica: licenses.json, update.json"
git branch -M main
git push -u origin main
```

---

## 8. Crea la Release PUBBLICA con gli EXE
```bat
cd C:\Dev\dist_public
gh release create v1.0.0 ^
  --title "<<NOME>> v1.0.0" ^
  --notes "Rilascio iniziale pubblico." ^
  --repo <<TUO_ACCOUNT>>/<<NOME>>-dist ^
  "C:\Dev\MioProgetto\dist\<<NOME>>.exe" ^
  "C:\Dev\MioProgetto\dist\<<NOME>>Updater.exe"
```
Nota: la release nel repo pubblico è scaricabile da chiunque abbia l'URL
(serve perché l'updater non usa token).

---

## 9. Push dei sorgenti nel repo PRIVATO
```bat
cd C:\Dev\MioProgetto
git init
git config user.name "<<TUO_NOME>>"
git config user.email "<<TUA_EMAIL>>"
git add -A
git add -f licenses.json update.json     :: servono nel repo anche se git-ignorati
git commit -m "<<NOME>> v1.0.0 - licenza, auto-aggiornamenti, 100 chiavi"
git branch -M main
git remote add origin https://github.com/<<TUO_ACCOUNT>>/<<NOME>>-gest.git
git push -u origin main
```

---

## 10. Verifica finale (end-to-end)
```bat
:: L'updater vede la nuova versione?
dist\<<NOME>>Updater.exe --check
:: Risposta attesa: "Aggiornamento disponibile" se version.txt e' vecchia

:: L'app si attiva con una chiave reale?
python -c "from models.license import activate_key, get_status; print(activate_key('PRIMA_CHIAVE_DA_KEYS_PRIVATE')); print(get_status())"
```

---

## 11. Aggiornamenti futuri (ciclo softwarehouse)
Ogni nuova versione (es. `v1.1.0`):
1. Modifica il codice, alza la versione in `version.txt` e `update.json`
2. `pyinstaller <<NOME>>.spec --noconfirm` e `<<NOME>>Updater.spec --noconfirm`
3. `cd C:\Dev\dist_public && git pull && copy ..\MioProgetto\update.json . && git add -A && git commit -m "v1.1.0" && git push`
4. `gh release create v1.1.0 --repo <<TUO_ACCOUNT>>/<<NOME>>-dist dist\<<NOME>>.exe dist\<<NOME>>Updater.exe`
5. `cd C:\Dev\MioProgetto && git add -A && git commit -m "v1.1.0" && git push`
6. I clienti premono 🔄 Aggiorna e l'app si sostituisce da sola.

---

## 🔑 Note di sicurezza
- `KEYS_PRIVATE.txt` resta SOLO su questa macchina. Mai committato, mai inviato.
- `licenses.json` è pubblico: chiunque può leggerlo, ma serve la CHIAVE per attivare.
- Il controllo licenza usa HMAC (offuscato): è un deterrent, non crittografia militarе.
  Per anti-crack avanzato servi un sistema di attivazione online con server tuo.
- Gli EXE nel repo pubblico sono scaricabili da tutti: è normale per un updater;
  la protezione è la LICENZA, non l'oscurità del file.

## 📋 File riservati (NON consegnare al cliente)
- `KEYS_PRIVATE.txt`  → elenco chiavi + firme (solo tuo)
- `license.key`       → cache attivazione della tua macchina (locale)
- `license_cache.json`→ cache licenze (locale)

## 📋 File da consegnare al cliente
- Link alla Release `v1.0.0` nel repo `<<NOME>>-dist`
- La sua CHIAVE personale (una riga di `KEYS_PRIVATE.txt`)
