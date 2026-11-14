# Play — Hooks als Leitplanken

Stufe 3, Build (im Graph unter Deploy eingeordnet). Playbook S. 27, dazu S. 32, in eigenen Worten.

## Was sich ändert

Ein Skill oder eine Anweisung **berät**; ein Hook ist die deterministische Schicht dahinter und **erzwingt**. Die meisten Handlungen des Agenten im Build sind Bearbeitungen und Shell-Befehle, darum feuern Hooks dort am häufigsten (S. 27). Was ausnahmslos gelten muss, bekommt einen Hook.

## Voraussetzungen

Keine; ein Einstiegspunkt (S. 9). Hooks werden in `.claude/settings.json` des Produkts eingetragen; Skripte liegen in `.claude/hooks/`.

## Was Hooks im Build tun

- Bearbeitungen an **geschützten Pfaden** sperren, z. B. erzeugte Dateien oder eingefrorene Teile.
- Nach Bearbeitungen **formatieren und linten**, damit keine Abweichung sich ansammelt.
- **Zugangsdaten** aus dem Diff halten.

(S. 27)

## Regeln

- **Schnell und eng:** Ein Hook läuft bei jeder passenden Handlung; er soll schnell sein und nur die geänderte Datei prüfen. Teure Prüfungen wie die ganze Testsuite gehören an den Commit oder den PR (S. 27).
- **Keine Freigabe durch Menschen im Build:** Ein Hook, der einen Menschen fragt, setzt ihn zurück auf den kritischen Pfad aller parallelen Sessions; solche Hooks gehören zu den Toren in Stufe 5 (S. 27; in Claudia ab 0.3).
- **Begründen:** Jede Sperre sagt, warum sie sperrt (Festlegung von Claudia nach dem Muster der Tore, S. 40–43).

## Der Hook für Testdateien (0.1)

Der Feedback loop braucht Schutz: Ein Agent, der Code repariert, darf die Prüfung nicht aufweichen (S. 32). In Claudia 0.1 gilt (Festlegung von Claudia):

- **gesperrt:** Änderungen an **bestehenden** Dateien unter `tests/` (Write oder Edit auf eine Datei, die es schon gibt);
- **erlaubt:** neue Testdateien, damit der fehlschlagende Test vor einer Korrektur geschrieben werden kann.

So kann ein Test, der vor der Korrektur da war, nicht umgeschrieben werden. Will der User selbst einen Test ändern, tut er das von Hand oder nimmt den Hook bewusst vorübergehend heraus (Commit `hooks: …`).

Beispiel (eigenes, Python, ohne Abhängigkeiten):

`.claude/settings.json`:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          { "type": "command", "command": "python3 \"$CLAUDE_PROJECT_DIR/.claude/hooks/tests_schuetzen.py\"" }
        ]
      }
    ]
  }
}
```

`.claude/hooks/tests_schuetzen.py`:

```python
import json, os, sys

daten = json.load(sys.stdin)
pfad = (daten.get("tool_input") or {}).get("file_path", "")
wurzel = os.environ.get("CLAUDE_PROJECT_DIR", "")
relativ = os.path.relpath(pfad, wurzel) if wurzel and pfad else pfad

if relativ.startswith("tests" + os.sep) and os.path.exists(pfad):
    print(f"Gesperrt: {relativ} ist ein bestehender Test. "
          "Ändere den Code, nicht den Test (Feedback loop). "
          "Neue Testdateien sind erlaubt.", file=sys.stderr)
    sys.exit(2)
sys.exit(0)
```

Exit-Code 2 sperrt die Handlung, und die Meldung auf stderr geht als Begründung an Claude.

## Ablauf

1. Mit dem User klären, was ausnahmslos gelten muss (in 0.1 mindestens: bestehende Tests schützen).
2. Plan zeigen: Datei, Matcher, Skript, was gesperrt und was erlaubt ist.
3. Nach Ja anlegen und mit einem Versuch prüfen, der gesperrt werden muss.
4. Commit `hooks: <produkt>`.

## Governance und Messen

Hooks sind versioniert in `.claude/settings.json` und damit prüfbar wie Code. Das Playbook nennt für die Leitplanken im Build keine eigenen Indikatoren; Claudia zählt in 0.1 die gesperrten Versuche, die im Transkript stehen, und sagt, dass das ihre eigene Messung ist.
