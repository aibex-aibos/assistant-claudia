---
name: claudia-evals
version: 0.3.0
description: >
  Lässt die Evals des Produkts lokal laufen, nach dem Play Evals aus The AI-Native SDLC
  Playbook (S. 34–35): legt bei Bedarf den Läufer evals/run.sh an, nennt die Zahl der Läufe,
  führt jedes Eval mit claude -p in einer frischen Kopie des Arbeitsstands aus, schreibt die Quote in
  evals/log.md, vergleicht mit dem letzten Lauf und lässt eine Änderung bei fallender Quote
  nur mit ausdrücklichem Ja committen; schlägt neue Evals aus Änderungen und Fehlern vor.
  Verwenden bei "/claudia-evals", "Lass die Evals laufen", "Mach ein Eval aus …", vor einem
  Commit an CLAUDE.md oder .claude/.
---

# Evals lokal — `/claudia-evals`

Ein Skill der Assistentin Claudia (Paket `assistant-claudia`). Er führt den Play *Evals* im Repo des Produkts, lokal ohne CI (Playbook S. 34–35); das Wissen dazu steht in `@@Plays/evals.md` der Assistentin. Er startet Läufe von `claude -p` und schreibt Dateien unter `evals/`, beides erst nach Ja.

**Wo:** im Repo des Produkts. Ist das aktuelle Verzeichnis kein Produkt, sag das und biete an, eines einzurichten (`@@Einrichtung/produkt.md`).

**Rückfragen:** Frage für Frage im Auswahlwerkzeug (`AskUserQuestion`; ist es nur verzögert gelistet, zuerst mit `ToolSearch` `select:AskUserQuestion` laden); je Frage mehrere Optionen mit ihrer Folge, die Empfehlung zuerst mit „(Empfohlen)“, wo nützlich „Diskutieren“, zuletzt die eigene Antwort. Jeder Schritt endet mit der nächsten Frage im Auswahlwerkzeug, nie mit einer Feststellung. Sagt der User „weiter“, gelten die Empfehlungen.

Der Skill hat zwei Teile: **A** Evals laufen lassen, **B** ein neues Eval anlegen. Wurde er aus einem Satz wie „Mach ein Eval aus …“ gerufen, beginne mit B, sonst mit A.

## A. Evals laufen lassen

### A1. Plan nennen

Sag in einem Satz, was geschieht: „Ich lasse die Evals des Produkts lokal laufen, je Eval ein Lauf von `claude -p` in einer frischen Kopie des Arbeitsstands, zeige dir das Ergebnis und trage es in `evals/log.md` ein.“ Wurde der Skill vor einem Commit an `CLAUDE.md` oder `.claude/` gerufen, nenne die Änderung, um die es geht; sie ist noch **nicht** committet.

### A2. Voraussetzungen

1. **Evals:** Zähle `evals/*.json`. Gibt es keine, sag das, nenne den Stand gegenüber dem Ziel des Playbooks (20–50, S. 34) und biete Teil B an (Empfohlen) oder „Ohne Evals weiter“. Dann endet Teil A.
2. **Läufer:** Gibt es `evals/run.sh` nicht, zeige ihn (Vorlage unten) mit den Werkzeugen, die du in `ERLAUBT` einträgst (die Befehle aus `CLAUDE.md`, z. B. `Bash(python3 -m unittest:*)`), und frage: „Ja, Läufer anlegen (Commit `eval: läufer`)“ (Empfohlen), „Ändern“, eigene Antwort. Nach Ja: schreiben, ausführbar machen, `git status` prüfen, Commit `eval: läufer`.
3. **Anmeldung:** Führe `claude -p "ok"` aus. Kommt keine Antwort oder ein Fehler 401, sag, dass der User sich im Terminal mit `/login` neu anmelden muss, und halte an.

### A3. Kosten nennen und fragen

Nenne die Zahl der Evals (gleich der Zahl der Läufe von `claude -p`) und frage: „Ja, laufen lassen“ (Empfohlen), „Später“, eigene Antwort.

### A4. Ausführen

Führe `evals/run.sh` aus (das kann einige Minuten dauern) und zeige die Ausgabe **wörtlich** als Codeblock.

### A5. Log und Vergleich

1. Lies die letzte Zeile der Tabelle in `evals/log.md` (gibt es die Datei nicht, lege sie mit Kopf an).
2. Hänge eine Zeile an: Datum und Uhrzeit, Quote (`<bestanden>/<gesamt>`), Commit (`git rev-parse --short HEAD`, mit „+ Änderung“, wenn eine Änderung noch nicht committet ist), fehlgeschlagene Evals.

```markdown
# Evals — Log

| Datum | Quote | Commit | fehlgeschlagen |
|---|---|---|---|
| 2026-11-16 14:20 | 2/2 | a1b2c3d | — |
```

3. Vergleiche den Anteil mit der vorigen Zeile.

### A6. Tor

- **Quote gleich oder besser:** sag das. War der Lauf vor einem Commit an der Konfiguration, frage: „Ja, Änderung committen (mit Log)“ (Empfohlen), „Nicht committen“, eigene Antwort. Nach Ja: `git status` prüfen (kein registrierter Skill, siehe `@@Einrichtung/produkt.md`), Commit der Änderung mit ihrem Betreff (z. B. `skill: <name>`, `hooks: <produkt>`, `claude-md: <produkt>`) zusammen mit `evals/log.md`. Ohne ausstehende Änderung: Commit `eval: log`.
- **Quote gefallen:** zeige die fehlgeschlagenen Evals und je Eval, was die Prüfung verlangt. Sag, dass eine Änderung an der Konfiguration, die die Quote senkt, vor dem Commit geprüft wird (S. 34, Schritt 4). Frage: „Nicht committen, Änderung zurücknehmen oder überarbeiten“ (**Empfohlen**), „Trotzdem committen (ausdrücklich)“, „Diskutieren“, eigene Antwort. Committet wird nur bei der ausdrücklichen Wahl „Trotzdem committen“. Das Log wird in jedem Fall committet (`eval: log`), damit der Lauf festgehalten ist; die Änderung selbst bleibt sonst uncommittet.

### A7. Nächste Frage

Biete den nächsten Schritt an, z. B. „Ein Eval aus der letzten Änderung anlegen“ (Teil B) oder den nächsten Play.

## B. Ein Eval anlegen

### B1. Quelle

Ein Eval entsteht aus einer echten Aufgabe (S. 34, Schritt 1):

- **aus einer abgeschlossenen Änderung:** Prompt aus `intent/<kurzname>/intent.md` (was gewollt war), Prüfung aus dem *Beweis* in `plan.md`;
- **aus einem behobenen Fehler:** Prompt, der den Fehler wieder herbeiführen würde, Prüfung, die ihn findet; es bleibt als Regressionstest (S. 35, Schritt 5).

Fehlt die Quelle, frage danach.

### B2. Entwurf zeigen

Zeige `evals/<name>.json`:

```json
{
  "prompt": "<Aufgabe, wie ein Mensch sie stellen würde>",
  "check": "<Shell-Befehl im Produkt; Exit-Code 0 heißt annehmbar>"
}
```

Die Prüfung soll zeigen, was annehmbar ist: Tests grün, Verhalten wie erwartet, Vorgabe eingehalten (S. 34, Schritt 2). Sie darf sich nicht auf Text verlassen, den das Modell frei formuliert, außer eine Vorgabe schreibt ihn vor.

### B3. Annahme und Commit

Frage: „Ja, Eval anlegen (Commit `eval: <name>`)“ (Empfohlen), „Korrigieren“, eigene Antwort. Nach Ja: schreiben, `git status` prüfen, Commit `eval: <name>`. Nenne den Stand (Zahl der Evals gegenüber 20–50) und biete an, die Evals jetzt laufen zu lassen (Teil A).

## Vorlage des Läufers `evals/run.sh`

```bash
#!/bin/bash
# Lokale Evals (Claudia, nach The AI-Native SDLC Playbook, S. 34-35).
# Jedes evals/*.json läuft in einer frischen Kopie des Arbeitsstands mit claude -p;
# danach entscheidet "check" (Exit-Code 0 = bestanden).
set -u
ERLAUBT="Read Write Edit Bash(python3 -m unittest:*)"   # Werkzeuge der Evals, aus CLAUDE.md
repo="$(cd "$(dirname "$0")/.." && pwd)"
kopie() {   # Arbeitsstand ohne ignorierte Dateien (kein @/, .aibos/, registrierte Skills)
  git -C "$repo" ls-files -z -co --exclude-standard | python3 -c '
import os, shutil, sys
quelle, ziel = sys.argv[1], sys.argv[2]
for pfad in sys.stdin.buffer.read().decode().split("\0"):
    q = os.path.join(quelle, pfad)
    if pfad and os.path.isfile(q):
        os.makedirs(os.path.dirname(os.path.join(ziel, pfad)), exist_ok=True)
        shutil.copy2(q, os.path.join(ziel, pfad))
' "$repo" "$1"
}
bestanden=0; gesamt=0
for e in "$repo"/evals/*.json; do
  [ -e "$e" ] || continue
  gesamt=$((gesamt+1))
  w="$(mktemp -d)"; mkdir "$w/p"; kopie "$w/p"
  prompt="$(python3 -c 'import json,sys; print(json.load(open(sys.argv[1]))["prompt"])' "$e")"
  check="$(python3 -c 'import json,sys; print(json.load(open(sys.argv[1]))["check"])' "$e")"
  (cd "$w/p" && git init -q && git add -A && git -c user.name=eval -c user.email=eval@local commit -qm eval \
     && claude -p "$prompt" --setting-sources project --allowedTools "$ERLAUBT" \
          --output-format json < /dev/null > "$w/ergebnis.json" 2>&1)
  if (cd "$w/p" && bash -c "$check") > /dev/null 2>&1; then
    echo "PASS $(basename "$e" .json)"; bestanden=$((bestanden+1))
  else
    echo "FAIL $(basename "$e" .json)"
  fi
  rm -rf "$w"
done
echo "Quote: $bestanden/$gesamt"
[ "$gesamt" -gt 0 ] && [ "$bestanden" -eq "$gesamt" ]
```

- Die Kopie enthält den **Arbeitsstand** samt nicht committeter Änderungen, aber nichts, was `.gitignore` oder `.git/info/exclude` ausschließen: Eine Änderung an `CLAUDE.md` oder `.claude/` wird so geprüft, bevor sie committet ist (Festlegung von Claudia). Die Kopie bekommt ein eigenes Git-Repo mit einem Commit, damit Aufgaben mit Git darin laufen.
- `--setting-sources project` lädt nur die Konfiguration des Produkts, wie später in CI (Claudia 0.4).

## Grenzen

- Evals in CI, nach Plan und als Merge-Check gehören zu Claudia 0.4 (S. 34–35); hier laufen sie lokal mit der Anmeldung des Users.
- Kein Lauf ohne Ja; die Zahl der Läufe wird vorher genannt.
- Kein Push, kein Remote, kein `/aibos-sync` im Produkt. Das Playbook nicht zitieren; Seiten nennen. Format, Läufer und Log sind Festlegungen von Claudia.
