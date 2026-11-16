# Play — Hooks als Leitplanken

Stufe 3, Build (im Graph unter Deploy eingeordnet). Playbook S. 27, dazu S. 20, 32, in eigenen Worten.

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
- **Keine Freigabe durch Menschen im Build:** Ein Hook, der einen Menschen fragt, setzt ihn zurück auf den kritischen Pfad aller parallelen Sessions; solche Hooks gehören zu den Toren in Stufe 5 (S. 27; in Claudia ab 0.4).
- **Begründen:** Jede Sperre sagt, warum sie sperrt (Festlegung von Claudia nach dem Muster der Tore, S. 40–43).

## Der Hook für Testdateien

Der Feedback loop braucht Schutz: Ein Agent, der Code repariert, darf die Prüfung nicht aufweichen (S. 32). In Claudia gilt (Festlegung von Claudia):

- **gesperrt:** Änderungen an **bestehenden** Testdateien (Write oder Edit auf eine Datei, die es schon gibt). Wo die Tests liegen, nimmt Claudia aus dem Produkt (`tests/`, `test_*.py`, `*_test.go`, …) und schreibt es in das Skript und in den Plan;
- **erlaubt:** neue Testdateien, damit der fehlschlagende Test vor einer Korrektur geschrieben werden kann.

So kann ein Test, der vor der Korrektur da war, nicht umgeschrieben werden. Will der User selbst einen Test ändern, tut er das von Hand oder nimmt den Hook bewusst vorübergehend heraus (Commit `hooks: …`).

Beispiel (eigenes, Python, ohne Abhängigkeiten). `MUSTER` nennt, wo das Produkt seine Tests ablegt; passe es an.

Der Hook prüft **zwei Wege**: das Bearbeiten einer Datei (`Write`, `Edit`) und Befehle in der Shell (`Bash`). Ein Hook nur auf `Write|Edit` ließe `rm`, `mv`, `sed -i` oder eine Umleitung `>` auf einen Test durch.

`.claude/settings.json`:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit|Bash",
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
import fnmatch, json, os, re, shlex, sys

MUSTER = ["tests/*", "test_*.py", "*/test_*.py"]   # Ablage der Tests im Produkt
AENDERND = re.compile(r"(^|[\s;&|])(rm|mv|cp|truncate|tee|git\s+rm|git\s+mv|git\s+checkout|sed\s+-i|perl\s+-i)(\s|$)")

daten = json.load(sys.stdin)
wurzel = os.environ.get("CLAUDE_PROJECT_DIR", os.getcwd())
eingabe = daten.get("tool_input") or {}

def bestehender_test(pfad):
    pfad = pfad.strip("'\"")                       # Ziel in Anführungszeichen, z. B. > "test_a.py"
    voll = pfad if os.path.isabs(pfad) else os.path.join(wurzel, pfad)
    relativ = os.path.relpath(voll, wurzel)
    return os.path.isfile(voll) and any(fnmatch.fnmatch(relativ, m) for m in MUSTER), relativ

def sperren(relativ):
    print(f"Gesperrt: {relativ} ist ein bestehender Test. "
          "Ändere den Code, nicht den Test (Feedback loop). "
          "Neue Testdateien sind erlaubt.", file=sys.stderr)
    sys.exit(2)

if daten.get("tool_name") in ("Write", "Edit"):
    ist_test, relativ = bestehender_test(eingabe.get("file_path", ""))
    if ist_test:
        sperren(relativ)
elif daten.get("tool_name") == "Bash":
    befehl = re.sub(r"\d*>&\d+", " ", eingabe.get("command", ""))   # 2>&1 ist keine Umleitung in eine Datei
    ziele = re.findall(r">>?\s*([^\s;&|]+)", befehl)                # Ziele von > und >>
    if AENDERND.search(befehl):
        try:
            ziele += shlex.split(re.sub(r">>?\s*[^\s;&|]+", " ", befehl))
        except ValueError:
            ziele += befehl.split()
    for wort in ziele:
        ist_test, relativ = bestehender_test(wort)
        if ist_test:
            sperren(relativ)
sys.exit(0)
```

Lesende Befehle (`cat`, `python3 -m unittest`, `git diff`) bleiben erlaubt; gesperrt wird nur, wenn ein ändernder Befehl einen bestehenden Test nennt. `cp <test> <kopie>` wird mitgesperrt, obwohl es den Test nur liest; das ist bewusst vorsichtig. Das ist eine Leitplanke, kein vollständiger Schutz: Wer Befehle verschachtelt (z. B. in einem Skript), kommt daran vorbei. Den Rest fangen Review und CI ab (ab 0.4).

Exit-Code 2 sperrt die Handlung, und die Meldung auf stderr geht als Begründung an Claude.

## Der Hook für `plan.md`

Das Playbook schlägt vor, `plan.md` und Umsetzung mit einem Hook synchron zu halten (S. 20); woran er eine Abweichung erkennt, sagt es nicht. Claudias Hook (Festlegung von Claudia) **meldet nach einem Commit** und **sperrt nichts**:

- Er läuft als `PostToolUse` auf `Bash`, wenn der Befehl `git commit` enthält.
- Er nimmt die jüngste `intent/<kurzname>/plan.md` im Git-Log, den neuen Commit eingeschlossen.
- Ändert der Commit Code, also Dateien außerhalb von `intent/`, `evals/` und `.claude/` und außer `CLAUDE.md` und `.gitignore`, aber nicht diese `plan.md`, meldet er Claude: „Commit ändert Code von `<kurzname>` ohne `plan.md`; prüfen, ob der Plan nachgeführt werden muss.“
- Merge-Commits (Zusammenführen paralleler Zweige) und einen Commit, den er schon geprüft hat, übergeht er; ohne `plan.md` im Produkt schweigt er.

Claudia fragt nach der Meldung, ob der Plan nachgeführt werden muss, und holt das mit `plan: <kurzname>` nach.

Eintrag in `.claude/settings.json` (neben dem Hook für Testdateien unter `PreToolUse`):

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          { "type": "command", "command": "python3 \"$CLAUDE_PROJECT_DIR/.claude/hooks/plan_melden.py\"" }
        ]
      }
    ]
  }
}
```

`.claude/hooks/plan_melden.py`:

```python
import json, os, re, subprocess, sys

# Meldet nach einem Commit, wenn Code einer Änderung ohne ihre plan.md committet wurde.
# Sperrt nichts (PostToolUse). Festlegung von Claudia nach Playbook S. 20.
AUSSER = ("intent/", "evals/", ".claude/")       # kein Code der Änderung
AUSSER_DATEIEN = ("CLAUDE.md", ".gitignore")

daten = json.load(sys.stdin)
befehl = (daten.get("tool_input") or {}).get("command", "")
if daten.get("tool_name") != "Bash" or not re.search(r"\bgit\b(\s+-[Cc]\s+\S+)*\s+commit\b", befehl):
    sys.exit(0)

wurzel = os.environ.get("CLAUDE_PROJECT_DIR", os.getcwd())
def git(*args):
    r = subprocess.run(["git", "-C", wurzel, *args], capture_output=True, text=True)
    return r.stdout.strip() if r.returncode == 0 else None

kopf = git("rev-parse", "HEAD")
if not kopf:
    sys.exit(0)
merker = os.path.join(git("rev-parse", "--git-dir") or ".git", "claudia-plan-hook")
merker = merker if os.path.isabs(merker) else os.path.join(wurzel, merker)
try:
    if open(merker).read().strip() == kopf:      # dieser Commit ist schon geprüft (z. B. Commit fehlgeschlagen)
        sys.exit(0)
except OSError:
    pass
open(merker, "w").write(kopf)

if len((git("rev-list", "--parents", "-n", "1", "HEAD") or "").split()) > 2:
    sys.exit(0)                                  # Merge-Commit: wird beim Zusammenführen geprüft

geaendert = (git("diff-tree", "--root", "--no-commit-id", "--name-only", "-r", "HEAD") or "").splitlines()
plan = (git("log", "-1", "--format=", "--name-only", "--", "intent/*/plan.md") or "").splitlines()
if not plan:
    sys.exit(0)                                  # noch kein plan.md im Produkt
plan = plan[0]
code = [d for d in geaendert if not d.startswith(AUSSER) and d not in AUSSER_DATEIEN]
if code and plan not in geaendert:
    kurzname = plan.split("/")[1]
    print(f"Commit ändert Code von `{kurzname}` ohne `plan.md` ({', '.join(code[:3])}); "
          "prüfen, ob der Plan nachgeführt werden muss.", file=sys.stderr)
    sys.exit(2)
sys.exit(0)
```

Exit-Code 2 bei `PostToolUse` gibt die Meldung auf stderr an Claude weiter; der Commit ist da schon geschehen. Den zuletzt geprüften Commit merkt sich das Skript in `.git/claudia-plan-hook` (nicht committet).

## Geprüft

Beide Skripte sind vor dem Schreiben mit Fällen geprüft (Lehre aus Claudia 0.1): das Skript für Testdateien mit 20 Fällen (`Write`, `Edit`, `Read`; `rm`, `mv`, `sed -i`, `git rm`, `>`, `>>`, `>` ohne Leerzeichen, Ziel in Anführungszeichen, Pfad mit Leerzeichen, Kette mit `&&`; lesende Befehle, neue Tests, Code), das Skript für `plan.md` mit 19 Fällen (Code mit und ohne `plan.md`, nur `intent/`, `evals/`, `.claude/`, `CLAUDE.md`, `.gitignore`; ohne `plan.md`; `git -C`, Kette, andere Befehle und Werkzeuge; wiederholter Aufruf; Merge; zwei Änderungen; erster Commit). Prüft Claudia ein Skript im Produkt, ruft sie es mit **absolutem Pfad** auf: Ein relativer Pfad aus dem falschen Ordner lässt Python mit Exit-Code 2 enden und sieht aus wie eine Sperre.

## Ablauf

1. Mit dem User klären, was ausnahmslos gelten muss (mindestens: bestehende Tests schützen; dazu der Hook für `plan.md`), Frage für Frage im Auswahlwerkzeug.
2. Plan zeigen: Datei, Matcher, Skript, was gesperrt und was erlaubt ist. **Vorab sagen:** Claude Code schützt `.claude/`; der User muss das Schreiben freigeben, wenn der Host fragt, oder die Dateien selbst anlegen. Dann das Ja im Auswahlwerkzeug.
3. Nach Ja anlegen und prüfen, ohne den Hook in der eigenen Session auszulösen: jedes Skript mit absolutem Pfad und einem JSON auf stdin (`tool_name`, `tool_input`); für Testdateien einen bestehenden Test (Exit 2), einen neuen Test und eine Code-Datei (Exit 0) und in der Shell `rm <bestehender Test>` (Exit 2), `python3 -m unittest` (Exit 0); für `plan.md` einen Befehl ohne `git commit` (Exit 0).
4. Weil Hooks zur Konfiguration gehören, schlägt Claudia vor dem Commit `/claudia-evals` vor (`evals.md`; gibt es noch keine Evals, sagt sie das). Dann Commit `hooks: <produkt>` und die nächste Frage im Auswahlwerkzeug.

## Was ein Hook nicht leistet

Hooks binden den **Agenten** in einer Session, nicht Menschen: Wer selbst im Editor oder Terminal einen Test ändert, an dem geht kein Hook vorbei. Projekt-Hooks liegen im Repo und lassen sich ändern; man sieht es in der Geschichte. Unabschaltbar sind nur Hooks in den verwalteten Einstellungen der Organisation (S. 40, 42–43). Vor Änderungen durch Menschen schützen PR-Review, Branch Protection und CI (S. 37–46, ab 0.4). Sag das, wenn der User Hooks als Schutz vor dem Team versteht.

## Governance und Messen

Hooks sind versioniert in `.claude/settings.json` und damit prüfbar wie Code. Das Playbook nennt für die Leitplanken im Build keine eigenen Indikatoren; Claudia zählt die gesperrten Versuche, die im Transkript stehen, und sagt, dass das ihre eigene Messung ist.
