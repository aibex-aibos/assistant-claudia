# Play — Skills

Stufe 3, Build. Playbook S. 25–26, dazu S. 12, 15–16, in eigenen Worten.

## Was sich ändert

Wissen, das eine Organisation einheitlich anwenden will, steckte bisher in Köpfen, Wikis und Gewohnheiten. Ein **Skill** macht es ausführbar: ausdrücklich, versioniert, überall angewandt und an einer Stelle geändert, wenn sich die Regel ändert (S. 25). Faustregel: Ein Skill lohnt sich für Wissen, das **immer gleich** angewandt werden muss; was in `CLAUDE.md` oder in einen einzelnen Prompt gehört, wird kein Skill (S. 25).

Ein Skill **berät**: Er macht es wahrscheinlich, dass die Regel beim Schreiben angewandt wird, erzwingt sie aber nicht. Muss sie immer gelten, kommt dahinter ein Hook oder eine Prüfung im Review (S. 26).

## Voraussetzungen

Keine zwingend; `CLAUDE.md` hilft (S. 9, 25). Nötig ist eine Regel mit einem benannten Eigentümer und einer schriftlichen Quelle (S. 25). Beim Einzelentwickler ist der User der Eigentümer.

## Skills des Produkts und registrierte Skills

- **Skills des Produkts** liegen in `.claude/skills/<name>/SKILL.md` des Produkts und werden **committet**, damit sie mit dem Code wandern (S. 25).
- **Registrierte Skills** schreibt AIBOS in denselben Ordner (Hüllen der Kernel-Skills, Skills der Kette, Claudias `/claudia-*`). Sie gehören **nicht** ins Produkt; Claudia hält sie über `.git/info/exclude` heraus (`@@Einrichtung/produkt.md`). Vor jedem Commit, der `.claude/` berührt, gleicht sie `exclude` mit `.aibos/registry` ab und prüft `git status`.

## Vorgaben als Skills

*Requirements and design* braucht die Vorgaben der Organisation als Skills: Marke, Sicherheit, Compliance, UX (S. 15). Beim Einzelentwickler kommen sie vom User (Festlegung von Claudia):

1. Ist ein `intent.md` angenommen und liegen im Produkt keine Skills mit Vorgaben, fragt Claudia, welche Vorgaben für die vier Bereiche gelten, Frage für Frage im Auswahlwerkzeug, mit „keine Vorgabe“ als Option.
2. Sie schreibt **nur die genannten** Vorgaben als Skill, je Bereich oder je zusammenhängender Regel einer; sie erfindet keine. Bereiche ohne Vorgabe meldet später der Spec-Lauf als Bedenken.
3. Eine Vorgabe ändert sich nur mit dem Ja des Users als ihr Eigentümer (S. 25, Schritt 5).

## Ablauf

1. **Eine Regel wählen**, die heute uneinheitlich angewandt wird, z. B. eine Konvention für Fehlermeldungen oder eine Regel für Schnittstellen (S. 25, Schritt 1).
2. **Als Skill schreiben:** ein Ordner mit `SKILL.md`; das Frontmatter sagt, wann er auslöst, der Rumpf, was zu tun ist (S. 25, Schritt 2). Claudia zeigt den Skill und legt die Annahme vor; sie sagt vorab, dass der User das Schreiben in `.claude/` freigeben muss.
3. **Ablegen** in `.claude/skills/<name>/` des Produkts; Commit `skill: <name>` (S. 25, Schritt 3). Ein Plugin für die ganze Organisation kommt in 0.7.
4. **Auslösen prüfen:** die Aufgabe auf mindestens drei verschiedene Weisen stellen und bestätigen, dass der Skill jedes Mal geladen wird (S. 25, Schritt 4). Ohne Dialog: `claude -p "<frage>" --output-format stream-json --verbose` im Produkt; im Ereignis `init` steht der Skill unter `skills`, und ein Werkzeugaufruf `Skill` mit seinem Namen zeigt, dass er geladen wurde.
5. **Ändern, wenn sich die Regel ändert:** Skill ändern, der Eigentümer stimmt zu; die nächste Session nimmt die neue Fassung (S. 25, Schritte 5–6). Weil sich damit die Konfiguration ändert, schlägt Claudia vor dem Commit `/claudia-evals` vor (S. 34).

## Aufbau eines Skills des Produkts

```markdown
---
name: fehlermeldungen
description: Regel für Fehlermeldungen im Produkt. Verwenden, wenn eine Eingabe geprüft, eine Fehlermeldung geschrieben oder ein Abbruch behandelt wird.
---

# Fehlermeldungen

Eigentümer: <Name>. Quelle: <wo die Regel steht>.

1. Jede Fehlermeldung beginnt mit „Fehler:“ und nennt, was falsch war.
2. Bei falscher Eingabe endet das Programm mit Exit-Code 2.
```

(eigenes Beispiel)

## Governance

Ein Skill ist eine beratende Kontrolle. Aufrufe stehen im Transkript der Session; der Eigentümer prüft Änderungen am Skill wie Code (S. 26). Beim Einzelentwickler ist das Ja im Chat mit dem Commit `skill:` der Beleg.

## Messen

| Indikator | Was | Woher |
|---|---|---|
| früh | Zeit von der Zustimmung zu einer geänderten Regel bis zum geänderten Skill | PR am Skill-Ordner (ab 0.4); in 0.3 der Abstand der Commits |
| spät | Befunde im Review, die sich auf die Regel berufen; sie sollen gegen null gehen, sonst löst der Skill nicht aus oder weicht von der Regel ab | PR-Review (ab 0.4) |

(S. 26)
