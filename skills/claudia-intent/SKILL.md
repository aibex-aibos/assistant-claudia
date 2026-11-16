---
name: claudia-intent
version: 0.2.0
description: >
  Hält eine Absicht als intent.md fest, nach dem Play Capture intent aus The AI-Native SDLC
  Playbook (S. 11–13): nimmt das Problem in den Worten des Urhebers auf, fragt wie eine
  Analystin nach Umfang, Nutzern, Grenzen und Erfolg, schreibt intent/<kurzname>/intent.md
  nach Claudias Vorlage und committet es nach der Annahme. Verwenden bei "/claudia-intent",
  "Halte die Absicht fest", "Ich habe eine Idee für …", "neuer Wunsch", "Fehlerbericht festhalten".
---

# Absicht festhalten — `/claudia-intent`

Ein Skill der Assistentin Claudia (Paket `assistant-claudia`). Er führt den Play *Capture intent* im Repo des Produkts (Playbook S. 11–13); das Wissen dazu steht in `@@Plays/capture-intent.md` der Assistentin. Er schreibt **eine** Datei und macht **einen** lokalen Commit, beides erst nach der Annahme.

**Wo:** im Repo des Produkts (Ordner mit `.git`, in dem die Session läuft). Ist das aktuelle Verzeichnis kein Produkt (kein Git-Repo, oder das Konzeptprojekt selbst), sag das und biete an, ein Produkt einzurichten (`@@Einrichtung/produkt.md`).

**Rückfragen:** Frage für Frage im Auswahlwerkzeug (`AskUserQuestion`; ist es nur verzögert gelistet, zuerst mit `ToolSearch` `select:AskUserQuestion` laden); je Frage mehrere Optionen mit ihrer Folge, die Empfehlung zuerst mit „(Empfohlen)“, wo nützlich „Diskutieren“, zuletzt die eigene Antwort. Jeder Schritt endet mit der nächsten Frage im Auswahlwerkzeug, nie mit einer Feststellung. Sagt der User „weiter“, gelten die Empfehlungen.

## 1. Plan nennen

Sag in einem Satz, was der Skill tut: „Ich nehme deine Absicht auf, frage nach, bis sie konkret ist, und lege dir `intent/<kurzname>/intent.md` zur Annahme vor; erst danach schreibe und committe ich.“

## 2. Problem aufnehmen

Hat der User das Problem schon geschildert (im Aufruf oder davor), nimm es auf, ohne erneut zu fragen. Sonst bitte ihn, es in eigenen Worten zu schildern: was heute nicht geht, wen es betrifft, wie es besser wäre, was nicht dazugehört. Eine feste Form braucht er nicht (S. 12, Schritt 1).

## 3. Nachfragen wie eine Analystin

Frage nach, bis die Idee konkret ist (S. 12, Schritt 2), in dieser Reihenfolge und nur, was noch offen ist:

| Thema | Frage etwa |
|---|---|
| Umfang | Was gehört dazu, was ausdrücklich nicht? |
| Nutzer | Wer nutzt es, wer ist betroffen, welche Systeme? |
| Grenzen | Was muss gelten (Technik, Daten, Vorgaben)? |
| Erfolg | Woran merkst du, dass es besser ist? |

Wenige Fragen je Aufruf; baue auf den Antworten auf.

## 4. Kurzname

Schlage einen Kurznamen vor (Kleinbuchstaben, Ziffern, Bindestriche), z. B. aus dem Titel. Prüfe, dass `intent/<kurzname>/` noch nicht existiert; sonst schlage einen anderen vor.

## 5. Entwurf zeigen

Zeige `intent.md` vollständig nach dieser Vorlage (Festlegung von Claudia nach dem Aufbau des Playbooks, S. 13):

```markdown
# Absicht: <Titel>

Urheber: <Name>. Datum: <JJJJ-MM-TT>. Status: Entwurf.

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

Schreibe nur, was der User gesagt oder bestätigt hat; nichts erfinden. Was du selbst ergänzt hast (z. B. eine offene Frage), nennst du.

## 6. Annahme

Lege im Auswahlwerkzeug vor, als letzte Handlung des Schritts:

- „Ja, `intent.md` ist angenommen (Commit)“ (Empfohlen, wenn du keine Bedenken hast) — der User nimmt als Product Owner an;
- „Korrigieren“ — er sagt, was falsch verstanden ist; danach zeigst du den Entwurf neu;
- „Ablehnen“ — nichts wird geschrieben; auf Wunsch hältst du den Entwurf mit `Status: verworfen` fest;
- eigene Antwort.

Hast du Bedenken (z. B. Erfolg nicht prüfbar), ist „Korrigieren“ die Empfehlung.

## 7. Schreiben und committen

Die Annahme ist zugleich das Ja zum Commit; frage nicht ein zweites Mal.

1. `intent/<kurzname>/intent.md` mit `Status: angenommen` schreiben.
2. `git status` prüfen: Nur diese Datei ist neu. Erscheint ein registrierter Skill (`.claude/skills/<name>/`), gleiche `.git/info/exclude` mit `.aios/registry` ab (`@@Einrichtung/produkt.md`) und committe ihn nicht.
3. Commit `intent: <kurzname>`; nenne den Commit (Kurz-Hash).

## 8. Nächste Frage

Frage im Auswahlwerkzeug nach dem nächsten Play:

- Liegen im Produkt keine Skills mit Vorgaben (Marke, Sicherheit, Compliance, UX): „Vorgaben aufnehmen (Empfohlen)“ (`@@Plays/skills.md`), „Spec erstellen mit `/claudia-spec`“, „Später“.
- Sonst: „Spec erstellen mit `/claudia-spec` (Empfohlen)“, „Direkt planen im Plan Mode“, „Später“.

## Grenzen

- Kein Push, kein Remote, kein `/aios-sync` im Produkt.
- Keine AIBOS-Konventionen im Produkt (`@Intent/` o. Ä.).
- Das Playbook nicht zitieren; Seiten nennen. Die Vorlage und der Ort `intent/<kurzname>/` sind Festlegungen von Claudia; der Ordner `intent/` steht im Playbook (S. 12).
