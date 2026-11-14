# Play — `CLAUDE.md`

Stufe 3, Build. Playbook S. 23–24, in eigenen Worten.

## Was sich ändert

Was ein Neuer im Team wissen müsste — Konventionen, Befehle, Architektur, die häufigsten Fehler — stand bisher in Köpfen und Wikis. Jetzt steht es in einer Datei, die der Agent zu Beginn jeder Session liest, die das ganze Team pflegt und die nach jedem Fehler besser wird (S. 23).

## Voraussetzungen

Keine; ein Einstiegspunkt (S. 9, 23). Nötig sind ein Repo, Claude Code und jemand, der den Code gut kennt; beim Einzelentwickler ist das der User.

## Ablauf

1. **`/init`:** Claudia bittet den User, `/init` als ganzen Prompt im Produkt einzugeben (Festlegung von Claudia: Der Befehl gehört dem User). Claude erzeugt daraus ein erstes `CLAUDE.md`.
2. **Kürzen** auf das, was ein Neuer am ersten Tag braucht: Befehle für Build, Test und Lint, die Konventionen, die wirklich zählen, und was Claude immer wieder falsch macht.
3. **Prüfblock** *Arbeit prüfen* ergänzen (siehe `feedback-loop.md`).
4. **Committen** in der Wurzel des Produkts, damit alle dieselbe Fassung haben und Änderungen wie Code geprüft werden: Commit `claude-md: <produkt>`.
5. **Arbeitsregel:** Macht Claude denselben Fehler zum **zweiten** Mal, kommt die Korrektur in `CLAUDE.md` (Abschnitt *Was Claude falsch macht*), wieder mit Commit `claude-md: …`.
6. **Unter einer Seite** halten: Claude liest die Datei bei jedem Start ganz, und Veraltetes kostet Kontext ohne Nutzen.

(S. 23)

## Aufbau

```markdown
# <Produkt>

## Befehle
- Build: …
- Test: …
- Lint: …

## Konventionen
- …

## Architektur
- …

## Was Claude falsch macht
- …

## Arbeit prüfen
…
```

Steht der Inhalt in einer anderen Sprache als Deutsch, hält `CLAUDE.md` das unter *Konventionen* fest.

## Governance

`CLAUDE.md` ist versioniert; die Anweisungen, nach denen der Agent arbeitet, sind damit prüfbar und nachvollziehbar. Konventionen wirken über die Datei, Änderungen stehen in der Geschichte, und Code Owner nehmen sie im Review an (S. 24). Beim Einzelentwickler zeigt Claudia jede Änderung, bevor sie sie committet.

## Messen

| Indikator | Was | Woher |
|---|---|---|
| früh | wie oft Claude einen Fehler wiederholt, den `CLAUDE.md` hätte verhindern sollen; Korrekturen an der Datei | Git-Geschichte (Commits `claude-md:`) |
| spät | Zeit bis zum ersten gemergten PR eines neuen Teammitglieds | PR-Daten (ab 0.3, im Team) |

(S. 24)
