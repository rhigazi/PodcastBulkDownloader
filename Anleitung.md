# Podcast Bulk Downloader - Anleitung (JSON-Verarbeitung)

Diese Anleitung erklärt, wie Sie das Tool so einrichten und verwenden, dass es automatisch Ihre Lieblings-Podcasts aus einer JSON-Datei (`my_podcasts.json`) durchsucht und die MP3-Dateien organisiert herunterlädt.

---

## 1. Konfiguration der `my_podcasts.json`

Das Tool liest die Podcasts aus einer JSON-Datei. Sie können die Datei im Hauptverzeichnis des Projekts anlegen oder bearbeiten.

### Standard-Struktur (Beispiel)

```json
{
  "podcasts": [
    {
      "title": "Chat with Trader",
      "url": "https://feeds.megaphone.fm/CWT9205947236"
    },
    {
      "title": "Lanz und Precht",
      "url": "https://podcasts.apple.com/de/podcast/lanz-precht/id1582828457"
    }
  ]
}
```

### Erweiterte Optionen pro Podcast (Optional)

Sie können für jeden Podcast individuelle Einstellungen festlegen, die die globalen Einstellungen überschreiben:

- **`last_n` / `last`**: Lädt nur die letzten $N$ Episoden herunter (z. B. `3`).
- **`last_days` / `days`**: Lädt nur Episoden herunter, die in den letzten $X$ Tagen veröffentlicht wurden.
- **`prefix`**: Fügt ein Datums- oder Uhrzeit-Präfix zum Dateinamen hinzu (Mögliche Werte: `"NO_PREFIX"`, `"DATE"`, `"DATE_TIME"`).
- **`overwrite`**: Falls auf `true` gesetzt, werden bereits heruntergeladene Episoden erneut heruntergeladen und überschrieben. Standardmäßig ist dies deaktiviert (`false`), um Zeit und Datenvolumen zu sparen.

**Beispiel mit erweiterten Optionen:**

```json
{
  "podcasts": [
    {
      "title": "Chat with Trader",
      "url": "https://feeds.megaphone.fm/CWT9205947236",
      "last_n": 5,
      "prefix": "DATE"
    },
    {
      "title": "Lanz und Precht",
      "url": "https://podcasts.apple.com/de/podcast/lanz-precht/id1582828457",
      "days": 7
    }
  ]
}
```

---

## 2. Automatische Features des Tools

1. **Unterordner pro Podcast:**
   Das Tool erstellt im MP3-Zielverzeichnis automatisch für jeden Podcast einen eigenen Unterordner basierend auf dem `title` (z. B. `mp3/Chat with Trader/` und `mp3/Lanz und Precht/`).

2. **Unterstützung von Apple Podcasts URLs:**
   Sollte eine URL von Apple Podcasts stammen (z. B. `https://podcasts.apple.com/...`), fragt das Tool automatisch die offizielle Apple iTunes-API ab, ermittelt den echten RSS-Feed im Hintergrund und lädt die Episoden direkt von dort herunter.

3. **Duplikatsprüfung (Schonendes Herunterladen):**
   Standardmäßig überspringt das Tool bereits heruntergeladene Dateien. Nur neue Episoden werden geladen.

---

## 3. Ausführen des Tools

Stellen Sie sicher, dass Sie sich im Hauptverzeichnis des Projekts befinden und Ihre virtuelle Umgebung aktiviert ist.

### Einfacher Start (Standard)

Um die Podcasts aus der Datei `my_podcasts.json` im Standard-Ordner `mp3/` zu speichern, führen Sie folgenden Befehl aus:

```bash
python -m src.bulk_downloader --json my_podcasts.json
```

### Erweiterte Startoptionen (Kombination mit CLI-Parametern)

Sie können globale Standardwerte über die Befehlszeile definieren, welche für alle Podcasts gelten, die in der JSON-Datei keine eigenen spezifischen Einstellungen hinterlegt haben:

- **Anderen Zielordner wählen (`-f` / `--folder`):**
  ```bash
  python -m src.bulk_downloader --json my_podcasts.json -f "/Pfad/zu/Ihrem/Musik/Ordner"
  ```

- **Nur die Episoden der letzten X Tage herunterladen (`--days`):**
  ```bash
  python -m src.bulk_downloader --json my_podcasts.json --days 14
  ```
  *(Lädt nur Episoden der letzten 14 Tage herunter)*

- **Nur die letzten N Episoden herunterladen (`-l` / `--last`):**
  ```bash
  python -m src.bulk_downloader --json my_podcasts.json --last 3
  ```

- **Dateinamen mit Datum präfixieren (`--prefix`):**
  ```bash
  python -m src.bulk_downloader --json my_podcasts.json --prefix DATE
  ```
  *(Dateiname wird zu: `YYYY-MM-DD Episodentitel.mp3`)*

- **Bereits heruntergeladene Dateien überschreiben (`--overwrite`):**
  ```bash
  python -m src.bulk_downloader --json my_podcasts.json --overwrite
  ```

---

## 4. Regelmäßige Ausführung einrichten (Automatisierung)

Um die Kanäle vollautomatisch regelmäßig nach neuen Podcasts zu durchsuchen, können Sie einen Cronjob (Linux/macOS) oder die Aufgabenplanung (Windows) einrichten.

### Unter Linux / macOS (mit `cron`):

1. Öffnen Sie die Crontab:
   ```bash
   crontab -e
   ```
2. Fügen Sie eine Zeile hinzu, um das Tool täglich (z. B. um 02:00 Uhr nachts) auszuführen:
   ```text
   0 2 * * * cd /pfad/zu/PodcastBulkDownloader && /pfad/zu/python -m src.bulk_downloader --json my_podcasts.json >> downloader.log 2>&1
   ```

### Unter Windows (mit der Aufgabenplanung):

1. Öffnen Sie die **Aufgabenplanung**.
2. Erstellen Sie eine neue **Einfache Aufgabe**.
3. Wählen Sie den Trigger **Täglich** und stellen Sie die Uhrzeit ein.
4. Als Aktion wählen Sie **Programm starten**:
   - **Programm/Skript:** `python.exe` (oder der Pfad zu Ihrem Python in der venv)
   - **Argumente hinzufügen:** `-m src.bulk_downloader --json my_podcasts.json`
   - **Starten in:** Der absolute Pfad zu Ihrem `PodcastBulkDownloader` Ordner.
