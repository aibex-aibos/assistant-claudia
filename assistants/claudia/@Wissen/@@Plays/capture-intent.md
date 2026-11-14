# Play — Capture intent

Stufe 1, Plan. Playbook S. 10–13, in eigenen Worten.

## Was sich ändert

Bisher wanderte eine Idee über Backlog, User Stories, Schätzungen und Refinement, bis sie die Entwicklung erreichte, und jeder Schritt entfernte sie ein Stück von dem, was der Urheber meinte. Jetzt schreibt der Urheber seine Absicht selbst fest: Er denkt sie mit Claude durch, und das Ergebnis ist eine kurze Markdown-Datei, ein Vorläufer der Spec in seinen eigenen Worten, die versioniert ist und die nächste Stufe direkt lesen kann (S. 11). Eine Absicht kann auch aus einem Ticket oder einem Alarm im Betrieb kommen; der Ablauf bleibt derselbe (S. 11).

## Voraussetzungen

Keine; ein Einstiegspunkt (S. 9, 12). Nötig sind ein vereinbarter Aufbau für `intent.md` und ein versionierter Ort, für ein einzelnes Produkt der Ordner `intent/` im Repo des Produkts (S. 12).

## Ablauf

1. Der Urheber schildert das Problem in seinen eigenen Worten: was heute nicht geht, wen es betrifft, wie es besser wäre, was nicht dazugehört. Eine feste Form braucht er nicht.
2. Claudia fragt nach, wie eine Analystin: **Umfang, Nutzer, Grenzen, Erfolg**, bis die Idee konkret ist. Wenige Fragen auf einmal; nichts fragen, was schon gesagt ist.
3. Claudia schreibt `intent/<kurzname>/intent.md` nach dem Aufbau unten und zeigt die Datei.
4. Der Urheber korrigiert, was falsch verstanden wurde.
5. Nach seinem Ja (als Product Owner nimmt er die Absicht an): Commit `intent: <kurzname>`. Autor und Zeit stehen damit in der Geschichte (S. 12).

## Aufbau von `intent.md`

```markdown
# Absicht: <Titel>

Urheber: <Name>. Datum: <JJJJ-MM-TT>. Status: Entwurf | angenommen | verworfen.

## Problem
<Was heute nicht geht, und für wen.>

## Angestrebtes Ergebnis
<Woran man merkt, dass es besser ist.>

## Betroffene Nutzer und Systeme
<Wer und was berührt wird.>

## Constraints
<Was gelten muss: Grenzen, Vorgaben, Nicht-Ziele.>

## Offene Fragen
<Was noch zu klären ist.>
```

Beispiel (eigenes): *Absicht: Tagesrechner* — Problem: Wer Fristen ausrechnet, zählt Tage im Kalender und verzählt sich. Ergebnis: Ein Befehl gibt die Zahl der Tage zwischen zwei Daten aus. Nutzer: Einzelperson am Terminal. Constraints: nur Standardbibliothek, Datumsformat ISO 8601. Offene Fragen: Zählt der Endtag mit?

## Governance

Der Beleg ist das committete `intent.md` mit Autor, Zeit und Geschichte. Der Product Owner nimmt die Absicht an oder lehnt sie ab; in einem Team zeigt das ein Merge oder ein geschlossener Review (S. 13). Beim Einzelentwickler ist das Ja im Chat mit dem Commit der Beleg; einen verworfenen Entwurf hält Claudia mit `Status: verworfen` fest, statt ihn zu löschen.

## Messen

| Indikator | Was | Woher |
|---|---|---|
| früh | Zeit vom ersten Gespräch bis zum Commit von `intent.md`; erwartet werden Stunden statt Wochen | Git-Geschichte des Produkts |
| spät | Anteil der Absichten, die in die nächste Stufe gehen statt verworfen zu werden; Änderungen an `intent.md` nach dem ersten Commit der Spec | Git-Geschichte (ab 0.2 mit `spec.md`) |

(S. 13)
