# assistant-claudia

Assistentin *Claudia* von AIbex AIOS, Version 0.1.0. Sie begleitet ein Softwareprojekt durch das AI-native SDLC, genau so, wie es *The AI-Native SDLC Playbook* (Louis Claxton, Anthropic, 2026) beschreibt, umgesetzt mit Claude Code. Jede Antwort beginnt mit „🦸‍♀️ ".

## Umfang von 0.1

Die fünf Einstiegspunkte des Playbooks, also die Plays ohne Voraussetzung:

- **Capture intent:** die Absicht als `intent/<kurzname>/intent.md` festhalten;
- **Plan mode:** ein Plan als `plan.md`, bevor Code entsteht;
- **Feedback loop:** je ein Befehl für Test und Build, Prüfung vor „fertig", erst der fehlschlagende Test;
- **`CLAUDE.md`:** mit `/init` anlegen, kürzen, nach wiederholten Fehlern ergänzen;
- **Hooks:** Leitplanken in `.claude/settings.json`, z. B. Schutz bestehender Tests.

Jede Stufe endet mit einem lokalen Commit im Produkt. Für einen Einzelentwickler, ohne Remote. Die übrigen Plays (Requirements & design, Skills, Subagents, Evals, PR review, CI/CD, Closing the loop, Claude Tag, Auto mode) folgen in späteren Versionen.

## Holen

**Voraussetzung:** AIbex AIOS v0.6 ist installiert, der Vault ist an der Vault-Root gebunden (`aios --activate v0.6.<n>`), und Claude Code ist der Host.

```bash
aios --fetch assistant-claudia
```

Zuordnen mit `/aios-assistant claudia` im Projekt, unter dem die Produkte liegen sollen. Claudia wirkt ab dem nächsten Chat. Ein neues Produkt legt sie auf Wunsch in `product/<name>/` an (ein AIBOS-Paket in `@Package/<paket>/`); die Arbeit am Produkt läuft dann in einer Session in dessen Ordner.

## Quelle

Das Wissen gibt das Playbook in eigenen Worten mit Seitenangaben wieder. Das Playbook selbst ist **nicht** Teil dieses Pakets; die Seitenangaben beziehen sich auf die Ausgabe von 2026 mit 54 Seiten.

## Lizenz

Apache License 2.0 (`LICENSE`), Rechteinhaber AIbex KG (`NOTICE`). Die Marken „AIbex" und „AIbex AIOS" sind nicht mitlizenziert. „Claude" und „Anthropic" sind Marken von Anthropic; das Paket steht in keiner Verbindung zu Anthropic.
