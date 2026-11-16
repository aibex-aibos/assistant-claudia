# Play — Parallele Sessions und Subagents

Stufe 3, Build. Playbook S. 27–29, dazu S. 31, in eigenen Worten.

## Was sich ändert

Bisher arbeitete ein Engineer an einer Aufgabe nach der anderen und wartete einen guten Teil des Tages auf Builds, Tests und Reviewer; zwischen Aufgaben zu wechseln ermüdet, darum tut es kaum jemand (S. 28). Jetzt führt **eine Person mehrere Arbeitsströme**:

- Eine **parallele Session** ist eine eigene Instanz von Claude Code für eine eigene Aufgabe in einem eigenen Git-Worktree. Die Sessions wissen nichts voneinander; gemeinsam haben sie nur die Person, die sie steuert (S. 27).
- Ein **Subagent** läuft innerhalb einer Session als Helfer mit eigenem Kontext und eigenen Werkzeugen; er passt für Aufgaben, die immer wiederkehren, etwa zu prüfen, ob die Anwendung läuft (S. 27).

Parallele Sessions erhöhen die Zahl der Aufgaben in Arbeit, Subagents halten jede Session bei ihrer Aufgabe; die Arbeit der Person wird Steuern und Prüfen (S. 28).

## Voraussetzungen

`CLAUDE.md`, weil alle Sessions es lesen; die Feedback-Schleife hilft, weil eine Session, die ihre Arbeit selbst prüft, weniger Aufsicht braucht (S. 28). Nötig sind ein Git-Repo (die Trennung kommt aus den Worktrees) und Rechte, die so eingestellt sind, dass Sessions nicht auf Rückfragen zu sicheren Befehlen warten (S. 28).

## Parallele Sessions — Ablauf

1. **Teilen nach dem Plan:** Claudia teilt die Arbeit nach `plan.md` in Aufgaben, die **verschiedene Dateien** berühren; Aufgaben mit gemeinsamen Dateien laufen nacheinander in einer Session (S. 28, Schritt 1). Die Aufgaben stehen in `plan.md` unter *Aufgaben* mit ihren Dateien.
2. **Ein Worktree je Aufgabe:** ein eigener Checkout auf eigenem Zweig, damit Sessions sich nicht in die Quere kommen (S. 28, Schritt 2). Claudia nennt je Aufgabe den Befehl, im Produkt-Ordner auszuführen:

   ```
   claude --worktree <aufgabe> --setting-sources project
   ```

   Ohne Dialog (z. B. zum Ausprobieren): `claude -p --worktree <aufgabe> --setting-sources project "<auftrag>"`. Claude Code legt den Worktree unter `.claude/worktrees/<aufgabe>` an, auf dem Zweig `worktree-<aufgabe>`.

   **Warum `--setting-sources project`** (Festlegung von Claudia, erprobt in einer Probe): Im Worktree fehlt die lokale Einrichtung von AIBOS (`@/`, `.aibos/`), weil sie nicht committet ist. Der Hook von AIBOS hält den Worktree darum für ein nicht eingerichtetes Projekt und lenkt die Session ab. Mit `--setting-sources project` gelten nur die Einstellungen des Produkts: `CLAUDE.md`, Hooks, Skills und Subagents des Produkts wirken, Claudia selbst wird dort nicht geladen; sie steuert aus der Hauptsession.
3. **Zwei oder drei Sessions** zum Anfang. Die Grenze ist, wie viele Ströme eine Person sorgfältig prüfen kann; mehr Sessions erst, wenn das Prüfen mithält (S. 28, Schritt 3). Claudia nennt die Zahl, bevor etwas startet.
4. **Rechte:** Weil `--setting-sources project` die Einstellungen des Users weglässt, können Rückfragen zu Befehlen kommen. Claudia schlägt Freigaben für die sicheren Befehle des Produkts in `.claude/settings.json` vor (z. B. den Test-Befehl), nach Ja, Commit `hooks: <produkt>`.
5. **Prüfen und zusammenführen** (Festlegung von Claudia): Nach dem Lauf prüft Claudia jeden Zweig: Tests mit wörtlicher Ausgabe, Abgleich mit `plan.md` (nur die Dateien der Aufgabe geändert?). Nach dem Ja des Users führt sie den Zweig lokal in `main` zusammen, mit einem Merge-Commit `merge: <kurzname>/<aufgabe>`, und lässt die Tests danach noch einmal laufen.
6. **Aufräumen** nach Rückfrage: `git worktree remove .claude/worktrees/<aufgabe>`, `git branch -d worktree-<aufgabe>`.

## Subagents — Ablauf

1. **Vorschlag aus Wiederholung** (Festlegung von Claudia): Wiederholt sich eine Aufgabe, schlägt Claudia einen Subagent vor. Der erste Vorschlag ist der `verifier`, sobald die Feedback-Schleife steht. Beispiele des Playbooks sind ein Vereinfacher, der unnötige Komplexität entfernt, ein Prüfer, der die Anwendung startet und das Verhalten prüft, und ein Rechercheur, der den Code erkundet und nur das Ergebnis meldet (S. 29, Schritt 4).
2. **Anlegen** als Markdown in `.claude/agents/<name>.md` mit Name, Beschreibung (wann er gerufen wird) und den Werkzeugen, die er nutzen darf; ins Repo, damit alle ihn haben (S. 29). Claudia zeigt die Datei und sagt vorab, dass das Schreiben in `.claude/` freigegeben werden muss; nach Ja Commit `agent: <name>`.
3. **Nutzen:** Den `verifier` ruft Claudia vor „fertig“ auf. Er **ersetzt die Feedback-Schleife nicht**: Die Schleife läuft während der ganzen Arbeit; der Verifier prüft am Ende einmal mit frischem Kontext, damit das Urteil nicht von den Annahmen gefärbt ist, die den Code hervorgebracht haben (S. 31).

## Aufbau eines Subagents

```markdown
---
name: verifier
description: Prüft vor „fertig“, dass die Änderung läuft. Verwenden, wenn eine Session ihre Arbeit abschließt.
tools: Bash, Read
---
Führe den Test-Befehl aus CLAUDE.md aus und rufe das Programm mit zwei typischen
und einer falschen Eingabe auf. Berichte, was du ausgeführt und gesehen hast und
was nicht zu plan.md passt. Ändere nichts.
```

(eigenes Beispiel)

## Governance

Mehr Sessions heißt mehr Ausgabe; die Kontrollen kommen darum aus der Konfiguration im Repo. Hooks und Rechte dort gelten für alle Sessions, und was eine Session tut, ist protokolliert und der Person zugeordnet, die sie gestartet hat (S. 29). Beim Einzelentwickler sind die Merge-Commits und die geprüften Testausgaben der Beleg.

## Messen

| Indikator | Was | Woher |
|---|---|---|
| früh | gleichzeitige Sessions je Engineer bei gleicher Qualität des Reviews; Anteil des Tages mit Steuern statt Warten | OpenTelemetry (ab 0.7); in 0.3 die zusammengeführten Worktree-Zweige je Änderung |
| spät | zusammengeführte Änderungen je Woche neben der Nacharbeit | PR-Geschichte (ab 0.4); in 0.3 die Commits |

(S. 29)
