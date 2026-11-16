# assistant-claudia

Assistentin *Claudia* von AIbex AIBOS, Version 0.3.0. Sie begleitet ein Softwareprojekt durch das AI-native SDLC, genau so, wie es *The AI-Native SDLC Playbook* (Louis Claxton, Anthropic, 2026) beschreibt, umgesetzt mit Claude Code. Jede Antwort beginnt mit „🦸‍♀️ “.

## Umfang von 0.3

Die Build-Stufe vollständig, lokal, für einen Einzelentwickler und Produkte ohne Remote. Neun Plays:

- **Capture intent:** die Absicht als `intent/<kurzname>/intent.md` festhalten;
- **Skills:** Vorgaben und Regeln des Produkts als Skills in `.claude/skills/`, mit Prüfung, dass sie auslösen;
- **Requirements and design:** aus der Absicht ein `spec.md` mit Anforderungen, Entwurf und markierten Bedenken;
- **Plan mode:** ein Plan als `plan.md`, bevor Code entsteht;
- **Parallele Sessions und Subagents:** Aufgaben in eigenen Worktrees, zusammengeführt nach Prüfung; Subagents wie ein `verifier` in `.claude/agents/`;
- **Feedback loop:** je ein Befehl für Test und Build, Prüfung vor „fertig“, erst der fehlschlagende Test;
- **`CLAUDE.md`:** mit `/init` anlegen, kürzen, nach wiederholten Fehlern ergänzen;
- **Hooks:** Leitplanken in `.claude/settings.json`: Schutz bestehender Tests, Meldung, wenn Code ohne `plan.md` committet wird;
- **Evals (lokal):** Aufgaben mit Prüfung in `evals/`, Läufer, Log und ein Tor, wenn die Quote fällt.

Dazu drei Slash-Commands:

| Befehl | Zweck |
|---|---|
| `/claudia-intent` | Absicht nach Vorlage festhalten |
| `/claudia-spec` | aus einer angenommenen Absicht die Spec machen |
| `/claudia-evals` | Evals lokal laufen lassen, neue Evals anlegen |

Jede Stufe endet mit einem lokalen Commit im Produkt. PR review, Evals in CI, Freigabetore, CI/CD, Auto mode, Closing the loop und Claude Tag folgen in späteren Versionen (Ziel: das ganze Playbook mit 1.0).

## Holen

**Voraussetzung:** AIbex AIBOS v0.7 ist installiert, der Vault ist an der Vault-Root gebunden (`aibos --activate v0.7.<n>`), und Claude Code ist der Host. Für AIbex AIOS v0.6 gibt es Claudia 0.2.0 (`aios --fetch "assistant-claudia~=0.2.0"`).

```bash
aibos --fetch assistant-claudia
```

Zuordnen mit `/aibos-assistant claudia` im Projekt, unter dem die Produkte liegen sollen. Claudia und ihre Slash-Commands wirken ab dem nächsten Chat. Ein neues Produkt legt sie auf Wunsch in `product/<name>/` an (ein AIBOS-Paket in `@Package/<paket>/`); die Arbeit am Produkt läuft dann in einer Session in dessen Ordner. Ein Produkt aus Claudia 0.1 stellt sie auf Wunsch um, damit Skills des Produkts committet werden können.

## Quelle

Das Wissen gibt das Playbook in eigenen Worten mit Seitenangaben wieder. Das Playbook selbst ist **nicht** Teil dieses Pakets; die Seitenangaben beziehen sich auf die Ausgabe von 2026 mit 54 Seiten.

## Lizenz

Apache License 2.0 (`LICENSE`), Rechteinhaber AIbex KG (`NOTICE`). Die Marken „AIbex“ und „AIbex AIBOS“ sind nicht mitlizenziert. „Claude“ und „Anthropic“ sind Marken von Anthropic; das Paket steht in keiner Verbindung zu Anthropic.
