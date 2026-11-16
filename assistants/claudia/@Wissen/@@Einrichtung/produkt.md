# Ein Produkt einrichten

Wie Claudia ein neues Produkt anlegt, damit sie in einer Session im Produkt geladen wird und die Plays dort wirken, und wie sie ein Produkt aus Claudia 0.1 umstellt.

## Warum so

AIbex AIBOS lädt einen Assistenten nur in einem Ordner mit `@/` (Spec 02, 03 von AIBOS v0.7). `CLAUDE.md`, Hooks, Skills und Plan Mode wirken dagegen nur im Ordner der Session. Darum ist das Repo des Produkts zugleich ein AIBOS-Projekt, aber **nur lokal**: Die Dateien von AIBOS werden nie committet.

## Ort

| Produkt | Ort |
|---|---|
| AIBOS-Paket | `<projekt>/@Package/<paket>/` |
| jede andere Software | `<projekt>/product/<produkt>/` |

`<projekt>` ist das AIBOS-Projekt, dem Claudia zugeordnet ist (dort, wo der User mit ihr spricht). Das Produkt erbt Claudia von ihm; einen eigenen Eintrag in `<produkt>/.aibos/config.yaml` braucht es nicht. Steht `/product/` bzw. `/@Package/` noch nicht in der `.gitignore` von `<projekt>`, schlägt Claudia vor, die Zeile einzutragen und dort nur sie zu committen; so gerät das Produkt-Repo nicht in das Repo des Projekts.

## Trennung der Skills

In `.claude/skills/` des Produkts liegen zwei Arten von Skills:

- **Skills des Produkts** (Vorgaben, eigene Regeln) werden committet; das Playbook legt sie dort ab (S. 25).
- **Registrierte Skills** schreibt AIBOS dorthin: Hüllen der Kernel-Skills, Skills der Kette, Claudias `/claudia-*`. Sie gehören nicht ins Produkt.

Darum steht `.claude/skills/` **nicht** in der `.gitignore` des Produkts. Stattdessen trägt Claudia die registrierten Skills lokal in `<produkt>/.git/info/exclude` ein; diese Datei wird nie committet, im Produkt bleibt keine Spur von AIBOS. Die Namen kommen aus `<produkt>/.aibos/registry` (Abschnitt `registered:`, je Name eine Zeile):

```
# AIBOS: registrierte Skills (Claudia, aus .aibos/registry)
/.claude/skills/aibos-assistant/
/.claude/skills/claudia-intent/
…
```

Claudia gleicht `exclude` mit `registry` ab, wenn sie im Produkt einen Skill anlegt und vor jedem Commit, der `.claude/` berührt (AIBOS registriert beim Start einer Session neu und kann Namen hinzufügen). Vor jedem Commit prüft sie mit `git status`, dass kein registrierter Skill mitkommt. Prüfen lässt sich die Trennung mit `git check-ignore -v .claude/skills/<name>/SKILL.md`: Für einen registrierten Skill nennt es die Zeile in `.git/info/exclude`, für einen Skill des Produkts nichts.

## Ablauf

1. **Plan zeigen:** Ordner, Befehle, Inhalt der `.gitignore`, die Trennung der Skills, die Ausgabe von `aibos --init "<pfad>" --accept-name --plan` (gibt es den Ordner noch nicht, nach dem Anlegen) und der Hinweis: Das Produkt hat in 0.3 **kein Remote**, und im Produkt wird kein `/aibos-sync` vorgeschlagen. Dann das Ja im Auswahlwerkzeug („Ja, anlegen (Empfohlen)“, „Ändern“, eigene Antwort).
2. **Nach Ja:**
   - Ordner anlegen, `git init -b main`.
   - `.gitignore` schreiben, mindestens:
     ```
     /@/
     /.aibos/
     /.agents/
     .DS_Store
     ```
   - Steht die Sprache schon fest, ihre üblichen Einträge dazu (bei Python `__pycache__/`, `*.pyc`); sonst spätestens mit dem Commit `build:`.
   - Commit `setup: <produkt>`.
3. **`aibos --init "<pfad>" --accept-name --yes`** ausführen. Ohne `--yes` zeigt `aibos` nur den Plan und endet mit Exit 1; beantworte eine Rückfrage von `aibos` **nie** per Pipe (`echo Y |`). Rufe `aibos` über den Weiterleiter auf: mit dem absoluten Pfad aus dem Block `<aibos-kernel-skills>` deines Master-Kontexts, als einzelnen Befehl ohne `cd`, `;`, `&&`, `|` oder Umleitung. `--accept-name` behält den Namen des Produkts; er muss nicht der Namensregel von AIBOS folgen. `aibos --init` ergänzt die `.gitignore` und committet sie („AIBOS: Projekt ergänzt“); nenne diesen Commit.
4. **Skills trennen:** `aibos --init` schreibt auch die Zeile `.claude/skills/` in die `.gitignore`. Claudia entfernt **nur diese Zeile**, füllt `.git/info/exclude` aus `.aibos/registry` und prüft `git status`; Commit `setup: skills`. Die übrigen Zeilen von `aibos --init` bleiben.
5. **Prüfen:** `aibos --context --assignment "<pfad>"` meldet `claudia`; `git -C "<pfad>" status` ist sauber; `git -C "<pfad>" ls-files @ .aibos .claude` ist leer; `git -C "<pfad>" check-ignore -v .claude/skills/claudia-intent/SKILL.md` nennt `.git/info/exclude`.
6. **Melden:** Die Arbeit am Produkt geht in einer **neuen Session im Produkt-Ordner** weiter. Dort schlägt Claudia den ersten Play vor (siehe `kreislauf.md`, *Folge in einem neuen Produkt*). Der Schritt endet mit einer Frage im Auswahlwerkzeug, z. B. „Ich öffne eine neue Session im Produkt (Empfohlen)“, „Noch etwas im Projekt erledigen“.

## Umstellung eines Produkts aus Claudia 0.1

Produkte aus 0.1 haben `/.claude/skills/` in der `.gitignore`; Skills des Produkts würden darum nicht committet. Findet Claudia diese Zeile, schlägt sie Schritt 4 vor: Plan zeigen (Zeile entfernen, Namen nach `.git/info/exclude`), Ja im Auswahlwerkzeug, `git status` prüfen, Commit `setup: skills`.

## Bekannte Grenzen

- Ein frischer Klon des Produkts auf einem anderen Rechner ist wieder ein nicht eingerichteter Ordner; dort müssen Schritte 3 und 4 neu laufen.
- Ein Worktree des Produkts (parallele Sessions) hat ebenfalls keine lokale Einrichtung; darum startet er mit `--setting-sources project` (`parallel-subagents.md`).
- Bekäme das Produkt ein Remote, würde `aibos --workspace sync` es aufnehmen und Sammel-Commits direkt auf den Zweig pushen. Darum gilt in 0.3: kein Remote.
- `/aibos-integrity` meldet im Produkt den Hinweis, dass `@/` keine getrackte Datei hat. Das ist gewollt.
