# Play — Plan mode

Stufe 3, Build. Playbook S. 19–21, in eigenen Worten.

## Was sich ändert

Früher las ein Entwickler den Entwurf und fing an zu schreiben; wie genau er vorging, stand nirgends, und der erste, der es sah, war der Reviewer am fertigen Diff. Jetzt beginnt die Arbeit mit einem **geschriebenen Plan**, den Claude im Plan Mode erstellt: Dort liest es den Code, ändert aber nichts. Der Entwickler korrigiert den Plan, bevor Code entsteht, und der angenommene Plan wird als `plan.md` committet, damit spätere Stufen dagegen prüfen können (S. 19).

## Voraussetzungen

Ein Einstiegspunkt (S. 9). Hilfreich sind die Absicht (`intent.md`, und wenn es eine gibt, die angenommene `spec.md`) und ein `CLAUDE.md` (S. 19). Nötig ist nur Claude Code mit Zugriff auf das Repo.

## Ablauf

1. Der User schaltet den Plan Mode ein, oder Claudia schlägt den Wechsel vor (Festlegung von Claudia: Der Schalter bleibt beim User).
2. Claudia liest `intent/<kurzname>/intent.md`, `spec.md` (falls vorhanden) und den Code und legt einen Plan vor: welche Dateien sich ändern, in welcher Reihenfolge, welche Tests die Änderung beweisen. Sie zeigt, wo die Arbeit in **Aufgaben mit verschiedenen Dateien** zerfällt, damit sie parallel laufen kann (S. 28; `parallel-subagents.md`).
3. Der User hinterfragt den Plan: Was kann die Änderung kaputtmachen? Welcher Schritt ist am riskantesten? Welche Wege hat Claudia verworfen, und warum? Claudia **beantwortet jede dieser Fragen, bevor der Plan angenommen wird**, auch wenn der User im selben Satz annimmt; die Annahme gilt erst nach ihrer Antwort.
4. Iterieren, bis jemand, der das Gespräch nicht kennt, die Änderung allein nach dem Plan umsetzen könnte.
5. Claudia legt die Annahme im Auswahlwerkzeug vor („Ja, `plan.md` ist angenommen (Commit)" (Empfohlen), „Korrigieren", „Diskutieren", eigene Antwort). Nach dem Ja des Users (als Engineer): `intent/<kurzname>/plan.md` schreiben, Commit `plan: <kurzname>`, dann die Frage „Umsetzen (Empfohlen)" oder „Später".
6. Umsetzen, in einer Session oder parallel in Worktrees (`parallel-subagents.md`). Mit einem guten Plan gelingt das oft in einem Durchgang. Dabei gilt der Play *Feedback loop*.
7. Weicht die Umsetzung vom Plan ab, wird `plan.md` nachgeführt (S. 20). Das Playbook schlägt einen Hook vor, der beides synchron hält (S. 20); Claudia richtet ihn als **Meldung nach dem Commit** ein (`hooks.md`): Ändert ein Commit Code ohne `plan.md`, fragt sie, ob der Plan nachgeführt werden muss, und holt das mit `plan: <kurzname>` nach.

## Aufbau von `plan.md`

```markdown
# Plan: <Titel> (aus intent.md vom <Datum>, spec.md vom <Datum>)

## Dateien
<neue und geänderte Dateien>

## Reihenfolge
1. …

## Risiken
<was brechen kann, Grenzen, Annahmen>

## Beweis
<welche Tests oder Prüfungen zeigen, dass es stimmt>

## Aufgaben
<nur bei paralleler Arbeit: je Aufgabe Name und Dateien; keine Datei in zwei Aufgaben>
```

## Governance

Der Entwurf wird geprüft, solange eine Kursänderung nur eine Textänderung ist. Der Plan Mode erzwingt das selbst: Claude ändert keine Datei, bevor der Plan angenommen ist. Plan, Fassungen und wer ihn angenommen hat, sind festgehalten. Routineänderungen nimmt der Engineer an, riskantere ein Tech Lead (S. 20); beim Einzelentwickler sagt Claudia, wenn eine Änderung riskant wirkt.

## Messen

| Indikator | Was | Woher |
|---|---|---|
| früh | Anteil der Änderungen, die im ersten Durchgang fertig werden; Zeit von der Annahme des Plans bis zum Merge | in 0.3 die Git-Geschichte (Commits nach `plan:`); PR-Daten ab 0.4 |
| spät | Nacharbeit je Änderung; wie oft der fertige Diff noch zu `plan.md` passt | Git-Geschichte |

(S. 21)

## Auto mode

Auto mode (S. 21) gehört nicht zu 0.3, sondern zu 0.5: Claude setzt nach angenommenem Plan ohne Rückfrage je Bearbeitung um. Das setzt reife Leitplanken voraus (`CLAUDE.md`, Skills, Hooks, Tests); siehe `ausblick.md`.
