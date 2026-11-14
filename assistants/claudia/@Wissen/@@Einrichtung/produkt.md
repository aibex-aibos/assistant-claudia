# Ein Produkt einrichten

Wie Claudia ein neues Produkt anlegt, damit sie in einer Session im Produkt geladen wird und die Plays dort wirken.

## Warum so

AIbex AIOS lädt einen Assistenten nur in einem Ordner mit `@/` (Spec 02, 03 von AIOS v0.6). `CLAUDE.md`, Hooks und Plan Mode wirken dagegen nur im Ordner der Session. Darum ist das Repo des Produkts zugleich ein AIOS-Projekt, aber **nur lokal**: Die Dateien von AIOS stehen in seiner `.gitignore` und werden nie committet.

## Ort

| Produkt | Ort |
|---|---|
| AIBOS-Paket | `<projekt>/@Package/<paket>/` |
| jede andere Software | `<projekt>/product/<produkt>/` |

`<projekt>` ist das AIOS-Projekt, dem Claudia zugeordnet ist (dort, wo der User mit ihr spricht). Das Produkt erbt Claudia von ihm; einen eigenen Eintrag in `<produkt>/.aios/config.yaml` braucht es nicht. Steht `/product/` bzw. `/@Package/` noch nicht in der `.gitignore` von `<projekt>`, schlägt Claudia vor, die Zeile einzutragen und dort nur sie zu committen; so gerät das Produkt-Repo nicht in das Repo des Projekts.

## Ablauf

1. **Plan zeigen:** Ordner, Befehle, Inhalt der `.gitignore`, und der Hinweis: Das Produkt hat in 0.1 **kein Remote**, und im Produkt wird kein `/aios-sync` vorgeschlagen.
2. **Nach Ja:**
   - Ordner anlegen, `git init -b main`.
   - `.gitignore` schreiben, mindestens:
     ```
     /@/
     /.aios/
     /.claude/skills/
     /.agents/
     .DS_Store
     ```
   - Commit `setup: <produkt>`.
3. **`aios --init "<pfad>" --accept-name`** ausführen. Rufe `aios` über den Weiterleiter auf: mit dem absoluten Pfad aus dem Block `<aios-kernel-skills>` deines Master-Kontexts, als einzelnen Befehl ohne `cd`, `;`, `&&`, `|` oder Umleitung. `--accept-name` behält den Namen des Produkts; er muss nicht der Namensregel von AIOS folgen. `aios --init` ergänzt die `.gitignore` und committet sie („AIOS: Projekt ergänzt"); nenne diesen Commit.
4. **Prüfen:** `aios --context --assignment "<pfad>"` meldet `claudia`; `git -C "<pfad>" status` ist sauber; `git -C "<pfad>" ls-files @ .aios` ist leer.
5. **Melden:** Die Arbeit am Produkt geht in einer **neuen Session im Produkt-Ordner** weiter. Dort schlägt Claudia den ersten Play vor (siehe `kreislauf.md`, *Folge in einem neuen Produkt*).

## Bekannte Grenzen

- Ein frischer Klon des Produkts auf einem anderen Rechner ist wieder ein nicht initialisierter Ordner; dort muss Schritt 3 neu laufen.
- Bekäme das Produkt ein Remote, würde `aios --workspace sync` es aufnehmen und Sammel-Commits direkt auf den Zweig pushen. Darum gilt in 0.1: kein Remote.
- `/aios-integrity` meldet im Produkt den Hinweis, dass `@/` keine getrackte Datei hat. Das ist gewollt.
