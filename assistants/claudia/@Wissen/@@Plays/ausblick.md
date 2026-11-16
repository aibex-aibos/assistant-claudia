# Ausblick — Releases nach Claudia 0.2

Diese Teile des Playbooks kann Claudia 0.2 **nicht**. Fragt der User danach, nennt Claudia Play, Seite und Release und bietet nichts an, was sie in 0.2 nicht kann; was lokal geht, bietet sie an. Ziel ist Claudia 1.0 mit dem ganzen Playbook: ausführbar und erprobt, wo ein Einzelentwickler es kann, sonst als Wissen mit Vorlage und Seitenverweis. Die Reihenfolge folgt dem Abhängigkeitsgraph (S. 9) und dem Umfeld, das ein Play braucht.

| Release | Thema | Plays und Teile | Seiten |
|---|---|---|---|
| 0.3 | Git-Host und Review | Produkte mit Remote; *PR review*: PRs nach `REVIEW.md` in Durchgängen (Fehler, Sicherheit, Übereinstimmung mit Spec und Plan), Befunde nach Schwere, Korrekturen auf `@claude`, wiederholte Befunde in `CLAUDE.md`; *Evals in CI* mit API-Schlüssel und Merge-Check; *Hooks als Freigabetore*: erlauben, fragen, sperren, mit Begründung; Branch Protection; Messgrößen aus PRs | 34–35, 37–41 |
| 0.4 | Pipeline und Autonomie | *CI/CD*: `claude -p` in der Pipeline, zuerst lesend (Triage, Changelog), dann schreibend hinter den Toren, in einer Sandbox ohne Zugang zur Produktion, Deployment als MCP-Werkzeug, Autonomie je Umgebung, geübter Rollback; *Auto mode*: nach angenommenem Plan ohne Rückfrage je Bearbeitung, sobald die Leitplanken reif sind | 21, 44–46 |
| 0.5 | Betrieb und geschlossener Kreislauf | *Closing the loop*: ein deterministisches Skript überwacht eine Metrik in Kontrollbändern (`bands.yaml`, Stufen 1σ, 2σ, 3σ), der Befund wird als neues `intent.md` zurückgeschrieben, aus jedem behobenen Incident wird ein Eval; *Claude Tag*: Ersthelfer in einem Kanal, kleine Korrekturen als PR, größere als `intent.md`, Nachbetrachtung in `lessons/`; Auslöser zwischen den Stufen ohne Dialog (z. B. Spec-Lauf beim Mergen von `intent.md`) | 8–9, 16, 48–52 |
| 0.6 | Team und Organisation | Rollen über den Einzelentwickler hinaus; Urheber ohne Git über claude.ai oder Cowork mit Connector; Quelle der Wahrheit mit bestehenden Systemen wie Jira; verwaltete Einstellungen; Verteilung von Skills und Hooks über Plugins; Messung über OpenTelemetry, Analytics, DORA | 12, 22, 29, 42–43, 46, 54 |
| 1.0 | Abdeckung bestätigt | keine neuen Plays: Seite für Seite geprüft, ein Durchlauf des ganzen Kreislaufs an einem Produkt | 1–54 |

Teile, die eine Organisation oder einen Unternehmensdienst voraussetzen (z. B. verwaltete Einstellungen über MDM, Bedrock, Vertex, Foundry, Compliance API, Claude Design, verwalteter Code Review), deckt Claudia als Wissen mit Vorlage und Seitenverweis ab und sagt, dass sie nicht erprobt sind.
