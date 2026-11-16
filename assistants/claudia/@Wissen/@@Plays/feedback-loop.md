# Play — Feedback loop

Stufe 4, Test. Playbook S. 31–33, in eigenen Worten.

## Was sich ändert

Früher kam das Signal, ob Code funktioniert, spät: Minuten später aus der CI, Tage später vom Tester, Wochen später aus dem Betrieb. Schreibt ein Agent den Code, wird der Mensch, der alles nachprüft, zum Engpass. Jetzt bekommt jede Session einen Weg, ihre Arbeit **selbst** zu prüfen: Tests laufen lassen, bauen, bei Oberflächen ein Bild vergleichen. Claude wiederholt, bis die Prüfung besteht; beim Menschen kommt nur an, was sie schon bestanden hat (S. 31).

Die Schleife ist nicht dasselbe wie ein Verifier-Subagent: Die Schleife läuft während der ganzen Arbeit, der Subagent prüft einmal am Ende mit frischem Kontext (S. 31; Subagents kommen in 0.2).

## Voraussetzungen

Ein Einstiegspunkt (S. 9). Nötig sind eine Testsuite und ein Build, die lokal mit **je einem Befehl** laufen; für Oberflächen ein Weg, das Ergebnis zu sehen (Browser oder Screenshot über MCP) (S. 31).

## Ablauf

1. **Ein Befehl.** Braucht die Prüfung heute mehrere Befehle und Wissen über die Umgebung, wird sie in ein Ziel verpackt (z. B. `make test`), das bei einem Fehler ungleich 0 endet.
2. **In `CLAUDE.md` eintragen:** jeden Befehl mit einem Beispiel für eine gesunde Ausgabe (Abschnitt *Befehle*).
3. **Messbares Ziel** setzen, damit Claude ohne Rückfrage prüfen kann, z. B. „alle Tests in `tests/test_tage.py` grün".
4. **Bei Fehlern zuerst der Test:** Claude bildet den Fehler als Test nach, lässt ihn laufen und bestätigt, dass er aus dem erwarteten Grund fehlschlägt; Commit `test: <kurzname>`. Erst dann die Korrektur, **ohne den Test zu ändern**; der Hook für Testdateien sichert das ab. Ein Test, der vor der Korrektur da war und den der Agent nicht umschreiben konnte, beweist, dass der Fehler weg ist.
5. **Bei Oberflächen** ein Sichtvergleich: umsetzen, Bild machen, mit der Vorlage vergleichen, anpassen; zwei oder drei Runden sind normal.
6. **Prüfen gehört zu „fertig":** Die Regel steht im Prüfblock von `CLAUDE.md`: vor „fertig" alle Prüfungen laufen lassen und die **wörtliche Ausgabe** zeigen (nicht nur „6 Tests grün"), als Codeblock, bei jedem Lauf, der zählt: dem fehlschlagenden Test vor einer Korrektur und dem grünen Lauf danach. Hat das Produkt keinen Build (z. B. ein einzelnes Skript), sagt `CLAUDE.md` das ausdrücklich unter *Befehle*.
6a. Nach dem Commit `build:` bzw. `fix:` endet der Schritt mit der nächsten Frage im Auswahlwerkzeug (z. B. „`CLAUDE.md` anlegen (Empfohlen)").
7. **Die Schleife schützen:** Wer Code repariert, darf die Prüfung nicht aufweichen. Ein Hook sperrt Änderungen an Testdateien während einer Korrektur (siehe `hooks.md`); sonst prüft der Review jede Änderung an einem Test.

(S. 32)

## Prüfblock in `CLAUDE.md`

```markdown
## Arbeit prüfen

- Build: `make build` (endet mit „Build ok")
- Test: `make test` (alle grün; keinen fehlschlagenden Test überspringen oder löschen)

Vor „fertig" beide laufen lassen und die Ausgabe zeigen.
Schlägt ein Test fehl, wird der Code geändert, nicht der Test.
```

(eigene Fassung nach S. 33)

## Governance

| Frage | Antwort |
|---|---|
| Was wird erzwungen? | Prüfung vor „fertig" und die Sperre für Testdateien bei Korrekturen, als Hooks, wo es garantiert sein soll |
| Was ist der Beleg? | die wörtliche Ausgabe von Test und Build, die Claude gezeigt hat; der Beleg kommt aus den Werkzeugen |
| Wo steht er? | im Transkript der Session; ab 0.3 auch im Check des PR |
| Wer nimmt ab? | der Code Owner im Review, der sich auf Absicht und Risiko konzentrieren kann |

(S. 33)

## Messen

| Indikator | Was | Woher |
|---|---|---|
| früh | Anteil der Agenten-Änderungen, die die CI im ersten Lauf bestehen | CI (ab 0.3); in 0.1 ersatzweise: Commits `build:`/`fix:` ohne Nacharbeit |
| spät | Review-Zeit je PR; Anteil der Änderungen, die im Betrieb scheitern | PR-Daten, Incident-Tracker (ab 0.3) |

(S. 33)
