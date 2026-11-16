# Play — Requirements and design

Stufe 2, Design. Playbook S. 14–17, in eigenen Worten.

## Was sich ändert

Bisher waren Anforderungen und Entwurf zwei Phasen mit zwei Teams: Analysten schrieben die Anforderungen, Designer übersetzten sie zurück in einen Entwurf. Das diente der Verantwortung, war aber langsam und verlustreich. Jetzt fallen beide in **eine Sitzung**: Aus dem angenommenen `intent.md` entsteht ein `spec.md` mit Anforderungen und Entwurf, unter den Vorgaben der Organisation als Skills (Marke, Sicherheit, Compliance, UX), mit **markierten Bedenken** (S. 15). Vorgaben werden angewandt, während die Spec entsteht, statt Wochen später im Review entdeckt (S. 14, 17).

Der Product Owner prüft die Spec, schreibt sie aber nicht (S. 15).

## Voraussetzungen

Ein angenommenes `intent.md` und die Vorgaben als Skills (S. 9, 15). Fehlen Vorgaben, sagt Claudia das und bietet an, sie zuerst aufzunehmen (`skills.md`); sonst gehen die fehlenden Bereiche als Bedenken in die Spec.

## Ablauf (`/claudia-spec`)

1. **Sitzung mit den Skills des Produkts**, das angenommene `intent.md` ist die Eingabe (S. 16, Schritt 1). Gibt es mehrere ohne `spec.md`, wählt der User per Auswahl.
2. **Der Auftrag** zeigt auf `intent.md`, nennt die Grenzen und verlangt markierte Bedenken (S. 16, Schritt 2). Das Playbook will den Lauf erst von Hand, dann als Slash-Command (`/claudia-spec`) und später ausgelöst durch das Mergen des `intent.md` ohne Dialog; Letzteres kommt in Claudia 0.5.
3. **Gegen die Idee prüfen:** Löst die Spec das genannte Problem? Sind die offenen Fragen aus `intent.md` beantwortet oder weitergetragen? (S. 16, Schritt 3)
4. **Bedenken zuerst:** Sie sind die Punkte, die eine Analystin eskaliert hätte. Claudia geht sie Frage für Frage durch; der User klärt jede mit dem Eigentümer der Vorgabe, beim Einzelentwickler mit sich selbst (S. 16, Schritt 4).
5. **Annahme und Commit:** `spec.md` neben `intent.md` (`intent/<kurzname>/spec.md`, Festlegung von Claudia), Commit `spec: <kurzname>`. Das Paar hält fest, was gewollt und was entschieden wurde (S. 16, Schritt 5; S. 17).
6. **Entscheidung über den Build:** Der Mensch entscheidet, ob es weitergeht, bei höherem Risiko mit einem Tech Lead; die angenommene Spec startet *Plan mode* (S. 16, Schritt 6). Claudia bietet ihn danach an.

Für Oberflächen sieht das Playbook vor, aus `intent.md` einen Entwurf in Claude Design (Beta) zu machen und an Claude Code zu übergeben (S. 15). Claudia kennt den Weg, kann ihn aber nicht ausführen; das ist Wissen (Roadmap, F39).

## Aufbau von `spec.md`

Festlegung von Claudia nach S. 16–17:

```markdown
# Spec: <Titel> (aus intent.md vom <Datum>)

Status: Entwurf | angenommen.

## Anforderungen
<was die Lösung leisten muss, prüfbar formuliert>

## Entwurf
<wie die Lösung in den Code passt: Module, Schnittstellen, Daten>

## Angewandte Vorgaben
| Skill | Commit der letzten Änderung | angewandt auf |
|---|---|---|

## Bedenken
| # | Bedenken | Vorgabe / Bereich | Eigentümer | Klärung |
|---|---|---|---|---|

## Offene Fragen aus intent.md
| Frage | beantwortet / weitergetragen |
|---|---|
```

Beispiel (eigenes): *Spec: Einheiten* — Anforderungen: Längen in m, cm, km und Gewichte in g, kg umrechnen; falsche Einheit führt zu Fehlermeldung. Angewandte Vorgabe: `fehlermeldungen` (Commit `a1b2c3d`). Bedenken: Für Sicherheit und Compliance gibt es keine Vorgabe (Eigentümer: User, geklärt: nicht nötig, rein lokales Werkzeug).

## Governance

Die geltende Vorgabe wird gelesen und angewandt, während die Spec entsteht. Spec, der Auftrag und die Fassungen der Skills stehen in der Versionsverwaltung; der Product Owner nimmt die Spec an und leitet Bedenken an die Eigentümer der Vorgaben (S. 17). Die Fassung eines Skills des Produkts hält Claudia als Commit seiner letzten Änderung fest, weil Skills des Produkts keine Version haben.

## Messen

| Indikator | Was | Woher |
|---|---|---|
| früh | Zeit zwischen dem Commit von `intent.md` und dem von `spec.md` für dieselbe Änderung | Git: `intent:` und `spec:` |
| spät | Nacharbeit an den Anforderungen nach Beginn des Builds: Commits an `spec.md` nach dem ersten Commit von `plan.md` | Git: `spec:` nach `plan:` |

(S. 17)
