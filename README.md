# Links

- [Customize Copilot](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions)
- [Adding Custom Instructions to your Repository](https://docs.github.com/en/copilot/how-tos/configure-custom-instructions-in-your-ide/add-repository-instructions-in-your-ide)
- [Instructions Support](https://docs.github.com/en/copilot/reference/custom-instructions-support#visual-studio-code)
- [Customize agent behavior in Visual Studio Code](https://code.visualstudio.com/docs/agent-customization/overview?chat-surface=agents-window)
- [Copilot Chat Cheat Sheet](https://docs.github.com/en/copilot/reference/chat-cheat-sheet?tool=vscode#slash-commands-1)
- [Markdown Crashkurs](https://github.com/fhuber-arag/markdown-demo/blob/main/README.md)

# Begriffe

- [**Prompt**](https://code.visualstudio.com/docs/agent-customization/prompt-files) - Vorgefertigte und benannte Frage an den KI Chat
- [**Instruction**](https://code.visualstudio.com/docs/agent-customization/custom-instructions) - Textdatei, die beschreibt, *wie* die KI eine Aufgabe bearbeiten soll; z.B. Code Guidelines, Vorlagen für Dokumentation, etc.
- [**Skill**](https://code.visualstudio.com/docs/agent-customization/agent-skills) - Externe Dienste (Tools), die der KI zur Verfügung gestellt werden. Beispiele: Anbindung an Google Mail, Aufruf eines Shell Scripts, Auslesen eines Hardware Sensors, ...
- [**MCP**](https://code.visualstudio.com/docs/agent-customization/mcp-servers) - Model Context Protocol; externer Dienst (Tool), der das MCP verwendet
- [**Hook**](https://code.visualstudio.com/docs/agent-customization/hooks) - Möglichkeit, sich an bestimmte Arbeitsschritte des KI Agenten einzuklinken und dann jeweils noch eigene Schritte auszuführen. Beispiel: Automatisches Formatieren von generiertem Quellcode mit `clang-format`

# Dateien

Dateien relativ zur Projektwurzel (`**` heißt, dass an dieser Stelle eine beliebige Anzahl von Unterverzeichnissen stehen kann):

| Datei(en)                                          | Beschreibung |
|----------------------------------------------------|-------------------|
| `./**/AGENTS.md`[^footnote_agents_md]<br/>`./CLAUDE.md`<br/>`./GEMINI.md` | Always-On Instructions für den gesamten Workspace. Kompatibel mit verschiedenen Tools (GitHub Copilot, Claude Code, Google Antigravity, ...)  |
| `./.github/copilot-instructions.md`                | Custom Instructions für das *gesamte* Repository. **GitHub-spezifisch!** |
| `./.github/instructions/**/<name>.instructions.md` | Selektiv aktive Instructions; Auswahl erfolgt mittels Pfadmuster. **GitHub-spezifisch**! |
| `./.github/prompts/**/<name>.promt.md`             | Vorgefertigter Prompt (Chat oder Agent)
| `./.github/skills/**/SKILL.md`                     | Skill-Beschreibung (ein Unterordner pro Skill mit weiteren Dateien)
| `./.vscode/mcp.json`                               | Auflistung verfügbarer MCP Dienste
| `./.github/hooks/*.json`                           | Hooks

[^footnote_agents_md]: Die Datei kann mehrfach existieren. Es wird im Agent Mode immer diejenige Datei verwendet, die "am nächsten" an der aktuellen Codestelle liegt (kann über Setting `chat.useNestedAgentsMdFiles` aktiviert werden).

:bulb: **Hinweis**: Orte für Custom Instructions können durch das Setting `chat.instructionsFilesLocations` angepasst werden.

:bulb: **Hinweis**: Für die Verwendung auf [GitHub](https://github.com/copilot) gibt es noch *Personal* und *Organization* Instructions, die wir hier einfach mal ignorieren (zumal die ARAG es deaktiviert hat).

# Custom Prompt

Wiederkehrende Aufgaben, die die KI immer wieder erfüllen soll, lassen sich als vorgefertigter Prompt unter `.github/prompts/` ablegen.

Am einfachsten ist es, sich einen solchen Prompt von der KI generieren zu lassen. Dafür steht im KI Chat (im Agent Modus) der Befehl `/create-prompt` zur Verfügung.

Beispiel:

```raw
/create-prompt erstelle ein Inhaltsverzeichnis aus einer Markdown Datei, wobei du nur Überschriften der ersten beiden Ebenen berücksichtigst. Die Ausgabe ist eine Liste im Markdown Format
```

# Custom Instructions

[Link mit mehr Informationen](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions#creating-path-specific-custom-instructions)

Jede GitHub Instructions Datei liegt im Markdown Format vor und kann einen optionalen Meta-Block (YAML Preamble) enthalten, der wie folgt aussieht:

```yaml
---
name: 'C++ Coding Standards'
description: 'Coding conventions for C++ files'
applyTo: "src/**/*.cpp"
---

# Naming Conventions

- Class names are in camel case, e.g. `MyUtilClass`
- Class attributes must have a leading underscore, e.g. `_id`
- All class attributes should be `private` and must only be accessible with getters and setters
    - An attribute `_name` should have a getter `GetName()` and a setter `SetName()`
```

Ob eine Instructions Datei von einem Agenten verwendet wird, entscheidet hauptsächlich der `applyTo` Filter; dies ist ein [Glob](https://code.visualstudio.com/docs/editor/glob-patterns) Muster relativ zur Projektwurzel. Fehlt dieses Pattern, ist die Datei immer aktiv.

Die `description` hilft dem Agenten zusätzlich dabei, zu entscheiden, ob die Instructions auf die gestellte Aufgabe zutreffen.

