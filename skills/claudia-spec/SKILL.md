---
name: claudia-spec
version: 0.3.0
description: >
  Macht aus einem angenommenen intent.md eine spec.md mit Anforderungen und Entwurf, nach dem
  Play Requirements and design aus The AI-Native SDLC Playbook (S. 15–17): wendet die Vorgaben
  des Produkts als Skills an, markiert Bedenken und klärt sie zuerst, prüft die Spec gegen die
  Idee und committet intent/<kurzname>/spec.md nach der Annahme. Verwenden bei "/claudia-spec",
  "Mach die Spec zu …", "Anforderungen und Entwurf für …", "spec.md erstellen".
---

# Spec erstellen — `/claudia-spec`

Ein Skill der Assistentin Claudia (Paket `assistant-claudia`). Er führt den Play *Requirements and design* im Repo des Produkts (Playbook S. 15–17); das Wissen dazu steht in `@@Plays/requirements-design.md` und `@@Plays/skills.md` der Assistentin. Er schreibt **eine** Datei und macht **einen** lokalen Commit, beides erst nach der Annahme.

**Wo:** im Repo des Produkts. Ist das aktuelle Verzeichnis kein Produkt, sag das und biete an, eines einzurichten (`@@Einrichtung/produkt.md`).

**Rückfragen:** Frage für Frage im Auswahlwerkzeug (`AskUserQuestion`; ist es nur verzögert gelistet, zuerst mit `ToolSearch` `select:AskUserQuestion` laden); je Frage mehrere Optionen mit ihrer Folge, die Empfehlung zuerst mit „(Empfohlen)“, wo nützlich „Diskutieren“, zuletzt die eigene Antwort. Jeder Schritt endet mit der nächsten Frage im Auswahlwerkzeug, nie mit einer Feststellung. Sagt der User „weiter“, gelten die Empfehlungen.

## 1. Plan nennen

Sag in einem Satz, was der Skill tut: „Ich lese die angenommene Absicht und die Vorgaben des Produkts, entwerfe `spec.md` mit Anforderungen, Entwurf und Bedenken, kläre die Bedenken mit dir und lege dir die Spec zur Annahme vor; erst danach schreibe und committe ich.“

## 2. Absicht wählen

Suche unter `intent/` die Absichten mit `Status: angenommen` und ohne `spec.md`.

- Keine: sag das und biete `/claudia-intent` an (Empfohlen).
- Eine: nimm sie und nenne sie.
- Mehrere: der User wählt im Auswahlwerkzeug; die jüngste ist die Empfehlung.

Lies `intent.md` vollständig.

## 3. Vorgaben lesen

Lies die Skills des Produkts in `.claude/skills/` (nur die committeten; registrierte Skills von AIBOS und Claudias `claudia-*` zählen nicht, siehe `git ls-files .claude/skills`). Ordne sie den Bereichen Marke, Sicherheit, Compliance und UX zu (S. 15).

Fehlt für einen oder mehrere Bereiche eine Vorgabe, sag das und frage:

- „Vorgaben zuerst aufnehmen“ — dann führst du den Play *Skills* (`@@Plays/skills.md`) und kommst danach hierher zurück;
- „Ohne diese Vorgaben weiter, als Bedenken festhalten“ (Empfohlen, wenn der User die Vorgaben eben erst abgelehnt hat; sonst ist „zuerst aufnehmen“ die Empfehlung);
- eigene Antwort.

Notiere für jeden angewandten Skill den Commit seiner letzten Änderung: `git log -1 --format=%h -- .claude/skills/<name>`.

## 4. Entwurf

Entwirf `spec.md` für die Integration in den vorhandenen Code: Lies dazu den Code und `CLAUDE.md`, falls es sie gibt. Wende die Vorgaben an und markiere jede Stelle, an der eine Vorgabe nicht erfüllt werden kann oder Vorgaben sich widersprechen (S. 16).

Vorlage (Festlegung von Claudia nach S. 16–17):

```markdown
# Spec: <Titel> (aus intent.md vom <Datum>)

Status: Entwurf.

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

Bereiche ohne Vorgabe stehen unter *Bedenken* (Eigentümer: der User).

## 5. Bedenken zuerst

Nenne die Zahl der Bedenken und gehe sie **Frage für Frage** durch, bevor du die ganze Spec zeigst (S. 16, Schritt 4). Je Bedenken: was es ist, welche Vorgabe, Optionen zur Klärung mit Empfehlung. Trage die Klärung in die Tabelle ein. Der User entscheidet als Eigentümer der Vorgabe; beim Einzelentwickler sag, dass er in dieser Rolle entscheidet.

## 6. Gegen die Idee prüfen

Prüfe und nenne kurz (S. 16, Schritt 3):

- Löst die Spec das Problem aus `intent.md`?
- Ist jede offene Frage aus `intent.md` beantwortet oder ausdrücklich weitergetragen?

Fehlt etwas, sag es und ergänze den Entwurf.

## 7. Annahme

Zeige `spec.md` vollständig und lege im Auswahlwerkzeug vor, als letzte Handlung des Schritts:

- „Ja, `spec.md` ist angenommen (Commit)“ (Empfohlen, wenn keine Bedenken offen sind) — der User nimmt als Product Owner an; bei höherem Risiko sag, dass das Playbook hier einen Tech Lead dazunimmt (S. 16, Schritt 6);
- „Korrigieren“ — danach zeigst du die Spec neu;
- „Ablehnen“ — nichts wird geschrieben;
- eigene Antwort.

Sind Bedenken offen, ist „Korrigieren“ die Empfehlung.

## 8. Schreiben und committen

Die Annahme ist zugleich das Ja zum Commit; frage nicht ein zweites Mal.

1. `intent/<kurzname>/spec.md` mit `Status: angenommen` schreiben.
2. `git status` prüfen: Nur diese Datei ist neu. Erscheint ein registrierter Skill, gleiche `.git/info/exclude` mit `.aibos/registry` ab (`@@Einrichtung/produkt.md`) und committe ihn nicht.
3. Commit `spec: <kurzname>`; nenne den Commit (Kurz-Hash).

## 9. Nächste Frage

Die angenommene Spec startet den Plan (S. 16, Schritt 6). Frage im Auswahlwerkzeug: „Plan im Plan Mode erstellen (Empfohlen)“ (`@@Plays/plan-mode.md`; der User schaltet den Plan Mode ein), „Später“, eigene Antwort.

## Grenzen

- Der Lauf ohne Dialog, ausgelöst durch das Mergen eines `intent.md` (S. 16, Schritt 2), gehört zu Claudia 0.6; hier läuft er im Dialog.
- Claude Design (Beta) für Entwürfe von Oberflächen (S. 15) kennt Claudia nur als Wissen.
- Keine Vorgabe erfinden: Was der User nicht als Vorgabe genannt hat, ist ein Bedenken, keine Regel.
- Kein Push, kein Remote, kein `/aibos-sync` im Produkt. Das Playbook nicht zitieren; Seiten nennen. Vorlage und Ort `intent/<kurzname>/spec.md` sind Festlegungen von Claudia.
