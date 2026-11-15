# Der Kreislauf

Grundwissen für jede Arbeit mit Claudia. Seitenangaben beziehen sich auf *The AI-Native SDLC Playbook* (Louis Claxton, Anthropic, 2026), im Folgenden „Playbook". Hier steht es in eigenen Worten.

## Warum das SDLC neu gebaut wird

Seit Agenten Code in großer Menge schreiben, ist das Schreiben nicht mehr der Engpass. Langsam ist, was davor und danach kommt: planen, prüfen, ausliefern (S. 3–4). Das Playbook baut darum alle Stufen des Entwicklungsprozesses um die KI herum neu; der Mensch steht über dem Kreislauf, nicht in jedem Schritt (S. 5, 53).

## Sechs Stufen, ein Artefakt je Stufe

| Stufe | endet mit | die nächste Stufe beginnt mit |
|---|---|---|
| 1 Plan | `intent.md` | dem angenommenen `intent.md` |
| 2 Design | `spec.md` | der freigegebenen Spec |
| 3 Build | `plan.md`, Code mit Tests | dem Diff |
| 4 Test | Ausgabe von Test und Build | dem geprüften Diff |
| 5 Deploy | Pull Request mit Befunden aus dem Review | dem gemergten PR |
| 6 Maintain | Befund aus dem Betrieb, als neues `intent.md` | einer neuen Absicht |

Jede Stufe schreibt ihr Artefakt in die Versionsverwaltung, und die nächste liest es (S. 7–8). In den frühen Stufen sind das Markdown-Dateien, weil Mensch und Agent dieselbe Datei lesen und bearbeiten können; ab dem Build ist es Code. Die Kette der Commits ist zugleich der Prüfpfad: wer was wollte, was der Agent daraus gemacht hat, wer es angenommen hat (S. 7).

## Plays und Abhängigkeitsgraph

Ein **Play** ist ein Baustein mit fünf Teilen: was sich ändert, wie man anfängt, die Schritte, Governance und wie man den Erfolg misst (S. 8). Die Stufen sind nicht linear; man kann sie in beliebiger Reihenfolge angehen.

Der **Abhängigkeitsgraph** (S. 9) sagt, in welcher Reihenfolge man Plays einführt. Ein Pfeil von A nach B heißt: A vor B. Gestrichelte Pfeile helfen, sind aber nicht nötig.

- **Einstiegspunkte** (nichts zeigt auf sie): *Capture intent*, `CLAUDE.md`, *Plan mode*, *Feedback loop*, *Hooks*. Das ist der Umfang von Claudia 0.1.
- Danach: *Requirements & design* (nach Capture intent und Skills), *Skills* (nach `CLAUDE.md`), *Subagents* (nach `CLAUDE.md`), *Evals* (nach `CLAUDE.md` und Feedback loop), *PR review* (nach `CLAUDE.md`), *CI/CD* (nach PR review und Hooks), *Closing the loop* (nach Capture intent, PR review, CI/CD und Hooks).

Das Playbook empfiehlt, jeden Schritt zuerst von Hand zu prompten; am Ende soll jedes angenommene Artefakt das nächste Tor auslösen, und der Mensch schaut an den Toren auf das, was der Agent markiert hat (S. 9).

**Folge in einem neuen Produkt.** Das Playbook legt die Folge der Einstiegspunkte **nicht** fest. Claudia schlägt als **ihre eigene Festlegung** vor und sagt das jedes Mal so (nicht „das Playbook empfiehlt"): Capture intent → Plan mode → Umsetzung mit Feedback loop → `CLAUDE.md` → Hooks. In einem **bestehenden** Produkt zuerst `CLAUDE.md`, weil `/init` vorhandenen Code braucht.

## Grundsätze

- **Der Mensch bleibt verantwortlich** für jede Entscheidung, die Urteil braucht; seine Aufmerksamkeit wandert mit den Artefakten, die zu prüfen sind (S. 7, 53).
- **Ein Skill berät, ein Hook erzwingt.** Was ausnahmslos gelten muss, steht nicht nur in einer Anweisung, sondern auch in einem Hook (S. 27).
- **Quelle der Wahrheit:** Für jedes Artefakt ist ein System die Quelle, alles andere hält eine Kopie oder einen Verweis. In Claudia 0.1 ist das Repo des Produkts die Quelle für alle Artefakte (S. 22).

## Rollen beim Einzelentwickler

Das Playbook kennt Urheber, Product Owner, Engineer, Tech Lead, Platform Engineer, Service Owner und Release Manager. In Claudia 0.1 trägt **eine Person alle Rollen**. Claudia sagt an jedem Tor, in welcher Rolle der User entscheidet, z. B. „als Product Owner nimmst du `intent.md` an" oder „als Engineer gibst du den Plan frei".

## Artefakte im Produkt

| Artefakt | Ort | Abschnitte |
|---|---|---|
| `intent.md` | `intent/<kurzname>/intent.md` | Kopf (Urheber, Datum, Status); *Problem*; *Angestrebtes Ergebnis*; *Betroffene Nutzer und Systeme*; *Constraints*; *Offene Fragen* |
| `plan.md` | `intent/<kurzname>/plan.md` | Kopf mit Verweis auf `intent.md`; *Dateien*; *Reihenfolge*; *Risiken*; *Beweis* |
| `CLAUDE.md` | Wurzel des Produkts | *Befehle*; *Konventionen*; *Architektur*; *Was Claude falsch macht*; *Arbeit prüfen* |
| Hooks | `.claude/settings.json`, `.claude/hooks/` | — |

- Dateinamen und Orte sind englisch wie im Playbook, der Inhalt ist deutsch, solange `CLAUDE.md` nichts anderes festhält.
- Den Ordner `intent/` im Repo des Produkts nennt das Playbook (S. 12). Dass `plan.md` daneben liegt, ist eine **Festlegung von Claudia**: So bleibt die Kette einer Änderung beisammen.
- Ab 0.2 kommt `spec.md` in denselben Ordner.

## Commits

Claudia zeigt jedes Artefakt und legt die Annahme im Auswahlwerkzeug vor („Ja, `<datei>` ist angenommen (Commit)" (Empfohlen), „Korrigieren", „Ablehnen", eigene Antwort). Die Annahme ist zugleich das Ja zum lokalen Commit; Claudia fragt nicht ein zweites Mal. Der Betreff hat die Form `<stufe>: <kurzname>`:

| Stufe | wann |
|---|---|
| `setup` | erster Commit eines neuen Produkts |
| `intent` | `intent.md` angenommen |
| `plan` | `plan.md` angenommen |
| `build` | Umsetzung mit grünen Tests |
| `test` | fehlschlagender Test vor einer Korrektur |
| `fix` | Korrektur mit grünen Tests |
| `claude-md` | `CLAUDE.md` angelegt oder ergänzt |
| `hooks` | Hooks eingerichtet oder geändert |

Weicht die Umsetzung vom Plan ab, wird `plan.md` im selben Commit nachgeführt (S. 20).

## Schritte nach außen

Push, Repo anlegen, Tag, Deployment und Pull Request nur nach eigener Rückfrage für jeden Schritt. In 0.1 haben Produkte kein Remote, und Claudia bietet nicht an, eines einzurichten; das kommt mit PR review und CI/CD in 0.3. Im Produkt schlägt Claudia nie `/aios-sync` vor; der Sync von AIBOS würde Sammel-Commits machen und direkt auf den Zweig pushen, und das passt nicht zur Commit-Kette des Playbooks.
