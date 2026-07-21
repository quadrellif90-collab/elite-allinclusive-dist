# 🏖️ Elite Allinclusive Gest

**Il gestionale completo per stabilimenti balneari e strutture ricettive "All Inclusive".**

Elite Allinclusive Gest è un'applicazione desktop (Windows) pensata per gestire
clienti, listini stagionali, tariffari cooperativa, fatturazione e molto altro —
tutto in un'unica interfaccia semplice e veloce.

> 🔒 **Prodotto a pagamento.** L'applicazione si avvia **solo con una chiave di
> licenza valida**. Le chiavi sono emesse dal produttore e verificate online
> (con cache offline dopo la prima attivazione).

---

## ✨ Funzionalità principali

- **Gestione clienti All Inclusive** con calcolo automatico totale / differenza / sconto
- **Listini e periodi stagionali** configurabili (alta/bassa stagione, prezzi per periodo)
- **Tabella prezzi Cooperativa** per il calcolo della differezza rispetto alla tariffa struttura
- **Strutture multiple** (es. più stabilimenti) con moltiplicatori e tariffe proprie
- **Fatturazione** (fatture, note di credito, scadenziario, export XML PA / CSV / PDF)
- **Dashboard e statistiche** per struttura, periodo, mese e settore
- **Calendario**, **assegnazione ombrelloni**, **confronto costi**
- **Backup automatici** e ripristino, export/import Excel
- **Tema chiaro (Spiaggia)** e **scuro (Notte)**
- **Aggiornamenti automatici** direttamente da GitHub (nessun download manuale)

---

## 💾 Installazione

1. Scarica `EliteAllinclusiveGest.exe` dall'ultima [Release](../../releases).
2. Avvia il file. Al primo avvio ti verrà chiesta la **chiave di licenza**.
3. Inserisci la chiave ricevuta all'acquisto e premi **Attiva con chiave**.
4. Completa il **wizard di configurazione iniziale** (cartella backup, tema, periodi, prima struttura).
5. Fatto! I tuoi dati vengono salvati in `%USERPROFILE%\GestionaleAllInclusive\`.

> Non serve installazione: è un singolo file eseguibile. Per la portabilità,
> crea una cartella `data` accanto all'exe e i dati saranno salvati lì.

### Aggiornamenti

Dalla barra laterale premi **🔄 Aggiorna**: l'updater esterno
(`EliteAllinclusiveGestUpdater.exe`) scarica l'ultima versione da GitHub e
sostituisce l'app automaticamente. Nessun download manuale.

---

## 🔑 Licenza

Elite Allinclusive Gest è un **prodotto commerciale con licenza proprietaria**.
Vedi il file [`LICENSE`](LICENSE) per i termini completi.

- Ogni licenza è valida per **un singolo computer / installazione**.
- La chiave viene verificata online al primo avvio (e successivamente in cache
  per funzionare anche offline).
- È vietata la copia, la redistribuzione o la reverse engineering del software.

---

## 📋 Prezzi

| Piano | Prezzo | Dettagli |
|-------|-------|----------|
| **Licenza Standard** | € 149,00 (una tantum) | 1 postazione, aggiornamenti gratuiti 12 mesi |
| **Licenza + Aggiornamenti vita** | € 249,00 (una tantum) | 1 postazione, aggiornamenti a vita |
| **Multi-postazione (5 PC)** | € 499,00 (una tantum) | 5 postazioni, aggiornamenti a vita |
| **Abbonamento Annuale** | € 79,00 / anno | 1 postazione, sempre aggiornata |

*IVA esclusa. Per licenze volume o rivenditori, contatta il produttore.*

---

## 🛠️ Per gli sviluppatori

Il progetto è scritto in Python (Tkinter / CustomTkinter) con database SQLite.

```bash
pip install -r requirements.txt
python main.py          # avvio in modalità sorgente
python generate_keys.py # (solo vendor) genera le chiavi di licenza
```

Build dell'eseguibile:

```bash
pyinstaller Gestionale_AllInclusive.spec --noconfirm   # app
pyinstaller EliteAllinclusiveGestUpdater.spec --noconfirm  # updater
```

---

## 📄 Changelog

### v1.0.0
- Rilascio iniziale pubblico.
- Calcolo clienti, listini/periodi, cooperativa.
- Fatturazione, dashboard, statistiche, calendario, scadenziario.
- Sistema di licenza e aggiornamenti automatici da GitHub.

---

© 2026 Elite Allinclusive Gest — Tutti i diritti riservati.
