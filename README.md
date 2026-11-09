# KI-Box Harbor: lokale KI auf einem PC mit 16 GB RAM ohne Grafikkarte

Deutsch · [English](README.en.md)

**KI-Box Harbor ist eine abgespeckte Version von [Harbor](https://github.com/av/harbor), die auf einem schwachen Linux-PC mit 16 GB RAM, ohne GPU und mit älterer CPU einen privaten KI-Chat mit deutscher Dokumentensuche startet.** Sie enthält nur noch zwei Dienste: [Ollama](https://github.com/ollama/ollama) für das Sprachmodell und [Open WebUI](https://github.com/open-webui/open-webui) als Chat-Oberfläche. Alle Daten bleiben auf dem Rechner. Ein Projekt des gemeinnützigen [KI Klub Werder e.V.](https://ki-klub-werder.de) aus Werder (Havel), Teil der [KI-Box](https://github.com/KI-Klub-Werder-e-V/ki-box).

> **Stand 9. November 2026:** Die zusammengesetzte Docker-Compose-Konfiguration ist geprüft. Auf echter 16-GB-Hardware ist der Stack noch nicht gelaufen, und der Installer ist noch nicht vollständig durchgetestet. Erfahrungsberichte sind sehr willkommen.

## Steckbrief

| | |
|---|---|
| **Hardware** | Linux-PC, 16 GB RAM, keine GPU, CPU mit AVX2 (etwa 6 Jahre alt reicht) |
| **Dienste** | Ollama 0.40.15, Open WebUI v0.13.3 (Slim-Image) |
| **Modelle** | Chat: `gemma4:e4b-it-qat` (6,1 GB), Embeddings: `bge-m3` (1,2 GB) |
| **Sprache** | Oberfläche auf Deutsch, Dokumentensuche mit deutschfähigem Embedding |
| **Für wen** | KI-Enthusiasten, die eine Box zu Hause, im Verein oder im kleinen Betrieb betreiben |
| **Basis** | Harbor 0.5.19, von 2.541 auf 67 Dateien reduziert |
| **Lizenz** | Apache-2.0 (wie Harbor); Open WebUI hat eine eigene Lizenz |

## Installation

```bash
curl -fsSL https://raw.githubusercontent.com/KI-Klub-Werder-e-V/ki-box-harbor/main/install.sh | bash
harbor up
```

Beim ersten `harbor up` lädt Ollama rund 7 GB Modelle. Danach ist der Chat im lokalen Netz unter `http://<IP-des-Rechners>:33801` erreichbar.

**Wichtig:** Das erste Konto, das sich registriert, wird Admin. Lege es direkt nach dem Start an. Weitere Konten starten als „ausstehend“ und müssen vom Admin freigeschaltet werden.

## Wichtige Befehle

```bash
harbor up          # starten
harbor down        # stoppen
harbor ps          # Status
harbor logs webui  # Logs der Oberfläche
harbor update      # auf das neueste Release dieses Forks aktualisieren
harbor ollama pull qwen3.5:4b   # weiteres Modell laden
```

Die übrigen Befehle beschreibt die [Harbor-CLI-Referenz](https://github.com/av/harbor/wiki/3.-Harbor-CLI-Reference). Befehle für entfernte Dienste funktionieren in diesem Fork nicht.

## Was gegenüber Harbor geändert ist

- **Entfernt:** Alle Dienste außer Ollama und Open WebUI, Desktop-App, Boost, Dokumentation, Tests, Windows-Installer, GPU-Varianten
- **Standarddienste** `webui` und `ollama` statt `webui` und `llamacpp`
- **Ollama nur lokal:** Port 33821 ist nur auf `127.0.0.1` gebunden, weil die Ollama-API keinen Login hat
- **Kein fester Schlüssel:** Harbor setzt `h@rb0r` als Open-WebUI-Schlüssel. Hier erzeugt Open WebUI beim ersten Start einen zufälligen und speichert ihn dauerhaft
- **Feste Versionen** statt `latest` und `main`
- **Speichersparend:** 8.192 Token Kontext, eine Anfrage zur Zeit, höchstens zwei geladene Modelle
- **Deutsch:** Oberfläche `de-DE`, Embeddings mit `bge-m3` statt dem englischen Standardmodell
- **Datenschutz:** Admins können fremde Chats nicht einsehen, Log-Level `INFO` statt `DEBUG`

Alle Änderungen stehen in [NOTICE](NOTICE).

## Häufige Fragen

### Läuft ein lokales Sprachmodell auf 16 GB RAM ohne Grafikkarte?

Ja, aber langsam. Fremdberichte nennen für einen Intel N100 etwa 6–9 Token pro Sekunde bei einem 8B-Modell. Rechne mit einer Anfrage zur Zeit; weitere warten. Für Enthusiasten und Fragen, bei denen man kurz warten kann, reicht das.

### Wie viel Speicher braucht KI-Box Harbor?

| Posten | Speicher | Grundlage |
|---|---|---|
| Linux und Docker | ca. 1,5 GB | Schätzung |
| Open WebUI (Slim) | ca. 1,5 GB | Schätzung |
| `bge-m3` | 1,2 GB | Modellgröße bei Ollama |
| `gemma4:e4b-it-qat` | 6,1 GB | Modellgröße bei Ollama |
| Reserve für Kontext und Cache | ca. 5,7 GB | Rest |

Wird es knapp, kleinere Modelle nehmen: `gemma4:e2b-it-qat` (4,3 GB) oder `qwen3.5:4b` (3,3 GB).

### Warum ein Fork statt Harbor direkt?

Harbor bringt über 150 Dienste mit und ist für Entwickler-Desktops gebaut. Für eine Box im Netz sind einige Voreinstellungen ungeeignet: ein fester Schlüssel, Ollama ohne Login im ganzen Netz, Open WebUI vom Entwicklungszweig. Der Fork behält Harbors CLI und Update-Weg und ändert nur diese Punkte.

### Wie sichere ich meine Daten?

Konten, Chats und Dokumente liegen in `~/.harbor/services/webui`, Modelle in `~/.ollama`. Stoppe den Stack mit `harbor down` und sichere den ersten Ordner, etwa mit [restic](https://restic.net). Modelle lassen sich neu laden. Ein eingebautes Backup ist in der [KI-Box-Spezifikation](https://github.com/KI-Klub-Werder-e-V/ki-box/blob/main/docs/spec.de.md) geplant.

### Verlassen Daten den Rechner?

Chats und Dokumente nicht. Telemetrie ist im Open-WebUI-Image abgeschaltet. Internet braucht der Rechner für Installation, Modell-Downloads und Updates. Lokaler Betrieb allein macht niemanden DSGVO-konform; wer die Box betreibt, bleibt verantwortlich.

### Ist das Open Source?

Der Fork steht wie Harbor unter Apache-2.0. Open WebUI nutzt eine eigene, nicht OSI-anerkannte Lizenz, die das Entfernen des Brandings oberhalb von 50 Nutzenden einschränkt. Dieser Fork nutzt das offizielle Image und lässt das Branding unverändert.

## Mitmachen

Am meisten hilft ein Erfahrungsbericht als [Issue](https://github.com/KI-Klub-Werder-e-V/ki-box-harbor/issues): CPU-Modell, RAM, Modell, Token pro Sekunde und was nicht funktioniert hat.

## Dank und Lizenz

Basiert auf [Harbor](https://github.com/av/harbor) von av und Mitwirkenden, Apache-2.0. Dieser Fork steht ebenfalls unter [Apache-2.0](LICENSE). Ein Projekt des [KI Klub Werder e.V.](https://ki-klub-werder.de)

*Angaben zuletzt geprüft: 9. November 2026.*
