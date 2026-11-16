# Play — Evals

Stufe 4, Test. Playbook S. 34–35, dazu S. 50, in eigenen Worten. In Claudia 0.2 **lokal**; Evals in CI kommen in 0.3.

## Was sich ändert

Evals sind die KI-native Form der Qualitätsprüfung an Stufengrenzen: eine Sammlung von Aufgaben, die immer dann läuft, wenn sich die Konfiguration des Agenten ändert. Wird ein neues Modell eingesetzt oder ein Prompt umgeschrieben, sagen die Evals, ob der Agent die Arbeit noch so gut macht wie vorher (S. 34). Die Sammlung lebt: Fälle, die nichts mehr unterscheiden, fallen weg, neue kommen aus dem Betrieb dazu (S. 34). Manche Teams lassen Evals nach Plan statt bei jeder Änderung laufen (S. 34).

## Voraussetzungen

`CLAUDE.md` und die Feedback-Schleife (S. 9, 34). Für CI nennt das Playbook eine CI, die Claude Code ohne Dialog ausführen kann, und einen API-Schlüssel mit Budget (S. 34); in 0.2 laufen die Evals lokal mit der Anmeldung des Users. Vor dem ersten Lauf prüft Claudia, dass `claude -p "ok"` antwortet.

## Format (Festlegung von Claudia)

Je Eval eine Datei `evals/<name>.json`:

```json
{
  "prompt": "Füge die Umrechnung von Millimetern hinzu und einen Test dazu. Führe danach die Tests aus.",
  "check": "python3 -m einheiten 5 mm m | grep -q '^0.005' && python3 -m unittest discover -s tests -q"
}
```

- `prompt`: die Aufgabe, wie ein Mensch sie stellen würde;
- `check`: ein Shell-Befehl im Produkt, der mit Exit-Code 0 endet, wenn das Ergebnis annehmbar ist: Tests grün, Verhalten wie erwartet, Vorgabe eingehalten (S. 34, Schritt 2).

(eigenes Beispiel)

Der **Läufer** `evals/run.sh` führt jedes Eval in einer frischen Kopie des Arbeitsstands aus (mit nicht committeten Änderungen, ohne ignorierte Dateien), damit eine Änderung an der Konfiguration vor ihrem Commit geprüft wird, ruft dort `claude -p` mit `--setting-sources project` auf (nur die Konfiguration des Produkts, wie später in CI), prüft mit `check`, gibt `PASS`/`FAIL` je Eval und die Quote aus und endet ungleich 0, wenn ein Eval fehlschlägt. Seine Vorlage steht im Skill `/claudia-evals`; Claudia legt ihn beim ersten Lauf nach Ja an (Commit `eval: läufer`).

Jeder Lauf kommt als Zeile in `evals/log.md`: Datum, Quote, Commit, fehlgeschlagene Evals. So lassen sich Läufe über die Zeit vergleichen (S. 35).

## Ablauf

1. **Aufgaben sammeln:** Das Playbook nennt 20 bis 50 echte Aufgaben aus der jüngsten Arbeit mit ihrem angenommenen Ergebnis (S. 34, Schritt 1). In 0.2 entstehen sie **aus echten Änderungen** (Festlegung von Claudia): Nach jeder abgeschlossenen Änderung schlägt Claudia ein Eval vor, mit dem Prompt aus `intent.md` und der Prüfung aus dem *Beweis* in `plan.md`; nach jeder Korrektur eines Fehlers eines zu diesem Fehler, das als Regressionstest bleibt (S. 35, Schritt 5; S. 50, Schritt 7). Eine Mindestzahl gibt es in 0.2 nicht; Claudia nennt den Stand gegenüber 20–50. Commit `eval: <name>`.
2. **Als Eval schreiben:** Prompt und Prüfung (S. 34, Schritt 2).
3. **Laufen lassen bei Änderungen der Konfiguration:** Berührt eine Änderung `CLAUDE.md` oder `.claude/` (Skills, Hooks, Agents), schlägt Claudia **vor dem Commit** `/claudia-evals` vor, weil diese Konfiguration den Agenten steuert und dieselben Regressionstests verdient wie Code (S. 34, Schritt 3). Bei anderen Commits nicht. Den Lauf nach Plan gibt es mit CI in 0.3.
4. **Tor bei fallender Quote:** Fällt die Quote gegenüber dem letzten Lauf im Log, zeigt Claudia die Fehlschläge; die Änderung wird nur nach **ausdrücklichem** Ja committet, empfohlen ist „Nicht committen“ (S. 34, Schritt 4). Das Log kommt mit diesem Commit, sonst mit `eval: log`.

## Kosten

Jedes Eval ist ein Lauf von `claude -p`. Claudia nennt die Zahl der Evals vor dem Start und startet erst nach Ja.

## Governance

Evals geben der Qualitätsprüfung ein Tor, das mit der Ausgabe der Agenten Schritt hält. In CI ist die Quote ein Merge-Check, die Läufe sind protokolliert, und das Team, dem die Konfiguration gehört, gibt Änderungen frei (S. 35). In 0.2 sind `evals/log.md` und das ausdrückliche Ja vor einem Commit trotz fallender Quote der Beleg.

## Messen

| Indikator | Was | Woher |
|---|---|---|
| früh | Quote über die Zeit; wie lange es dauert, bis aus einem Fehler im Betrieb ein Eval wird | `evals/log.md`; Git: `fix:` bis `eval:` |
| spät | in CI gefundene Regressionen gegenüber denen im Betrieb | CI und Incident-Tracker (ab 0.3) |

(S. 35)
