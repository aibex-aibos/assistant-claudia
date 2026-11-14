# Ausblick — Plays nach Claudia 0.1

Diese Plays des Playbooks kann Claudia 0.1 **nicht**. Fragt der User danach, nennt Claudia Play, Seite und Release und bietet nichts an, was sie in 0.1 nicht kann. Die Reihenfolge folgt dem Abhängigkeitsgraph (S. 9).

| Play | Stufe | in eigenen Worten | Seiten | Release |
|---|---|---|---|---|
| Requirements & design | 2 Design | aus einem angenommenen `intent.md` in einer Sitzung ein `spec.md` mit Anforderungen und Entwurf machen; Vorgaben der Organisation (Marke, Sicherheit, Compliance, UX) als Skills anwenden; Bedenken und widersprüchliche Vorgaben markieren | 14–17 | 0.2 |
| Skills | 3 Build | Wissen, das uneinheitlich angewandt wird, als Skill in `.claude/skills/<name>/` festhalten und prüfen, dass er auslöst; einen Ablauf erst von Hand prompten, dann als Slash-Command kodifizieren | 16, 25–26 | 0.2 |
| Parallele Sessions, Subagents | 3 Build | unabhängige Aufgaben in eigenen Worktrees parallel laufen lassen; wiederkehrende Aufgaben als Subagent in `.claude/agents/`, z. B. ein Verifier mit frischem Kontext | 27–29 | 0.2 |
| Evals in CI | 4 Test | 20–50 echte Aufgaben als Evals sammeln, bei jeder Änderung an `CLAUDE.md`, Skills oder Hooks in der CI laufen lassen, aus jedem Incident einen Eval machen | 34–35 | 0.2 |
| PR review | 5 Deploy | PRs nach `REVIEW.md` in mehreren Durchgängen prüfen (Fehler, Sicherheit, Übereinstimmung mit Spec und Plan); wiederholte Befunde in `CLAUDE.md` übernehmen | 37–39 | 0.3 |
| Hooks als Freigabetore | 5 Deploy | jede menschliche Freigabe, die bleiben muss, als Hook ausdrücken (erlauben, fragen, sperren), mit Begründung | 40–43 | 0.3 |
| CI/CD | 5 Deploy | Claude in der Pipeline mit `claude -p`, zuerst lesend, dann schreibend hinter Toren, in einer Sandbox ohne Zugang zur Produktion, mit gestufter Autonomie je Umgebung und geübtem Rollback | 44–46 | 0.3 |
| Closing the loop | 6 Maintain | ein Skript überwacht eine Metrik in Kontrollbändern; eine Verletzung wird diagnostiziert und als neues `intent.md` zurück in den Kreislauf geschrieben | 48–51 | 0.3 |
| Claude Tag | 6 Maintain | Claude als Ersthelfer in einem Kanal für Incidents und Tickets; kleine Korrekturen als PR, größere als `intent.md`; Nachbetrachtung in `lessons/` | 52 | 0.3 |
| Auto mode | 3 Build | nach angenommenem Plan ohne Rückfrage je Bearbeitung umsetzen, sobald die Leitplanken reif sind | 21 | 0.3 |

Dazu kommen in späteren Releases: Teams mit getrennten Rollen und verwalteten Einstellungen (S. 42–43), Produkte mit Remote und Pull Requests, die Messgrößen aus PR-Daten und CI sowie die Dienste Claude Design (S. 15), verwalteter Code Review (S. 37–38), Claude Tag (S. 52) und claude.ai bzw. Cowork für Urheber ohne Git (S. 12).
