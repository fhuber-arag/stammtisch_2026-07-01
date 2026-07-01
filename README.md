# Links

- [Awesome GitHub Copilot](https://awesome-copilot.github.com/) - Community-Sammlung von vorgefertigen Prompts, Skills, etc.
- [Customize Copilot](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions)
- [Adding Custom Instructions to your Repository](https://docs.github.com/en/copilot/how-tos/configure-custom-instructions-in-your-ide/add-repository-instructions-in-your-ide)
- [Instructions Support](https://docs.github.com/en/copilot/reference/custom-instructions-support#visual-studio-code)
- [Customize agent behavior in Visual Studio Code](https://code.visualstudio.com/docs/agent-customization/overview?chat-surface=agents-window)
- [Copilot Chat Cheat Sheet](https://docs.github.com/en/copilot/reference/chat-cheat-sheet?tool=vscode#slash-commands-1) (leider unvollständig :angry:)
- [Markdown Crashkurs](https://github.com/fhuber-arag/markdown-demo/blob/main/README.md)

# Visual Studio Code für Remote Entwicklung mit Agents einrichten

Öffnet eure **remote** `settings.json` Datei (sollte unter `~/.vscode-server/data/Machine/` liegen) und stellt sicher, dass ihr die folgenden drei Zeilen habt:

```json
{
   // Mehr Zeilen ...
  "http.proxy"           : "http://proxy.de.arag.net:8080",
  "http.proxyStrictSSL"  : false,
  // ...eventuell noch mehr Zeilen ...
}
```

Öffnet dann eure **lokale** `settings.json` (im Projekt in `git/kranken/.vscode/`) und sucht nach den folgenden beiden Zeilen und **löscht** sie gegenenfalls:

```json
{
    // ...
    "GitHub.copilot"      : ["ui", "workspace"],
    "GitHub.copilot.chat" : ["ui", "workspace" ]
    // ...
}
```

Danach solltet ihr Visual Studio Code neu starten, damit die Änderungen greifen. Es ist eventuell möglich, dass der VSCode Remote Server Prozess noch eine Weile nachläuft, nachdem ihr VS Code geschlossen habt. Im Zweifel überprüft ihr das mit

```bash
ps x | grep vscode-server
```

falls noch ein Prozess läuft, einfack `kill`-en :sunglasses:.

# Begriffe

- [**Instruction**](https://code.visualstudio.com/docs/agent-customization/custom-instructions) - Textdatei, die beschreibt, *wie* die KI eine Aufgabe bearbeiten soll; z.B. Code Guidelines, Vorlagen für Dokumentation, etc.
- [**Prompt**](https://code.visualstudio.com/docs/agent-customization/prompt-files) - Vorgefertigte und benannte Frage an den KI Chat
- [**Skill**](https://code.visualstudio.com/docs/agent-customization/agent-skills) - Quasi eine Weiterentwicklung von *Prompts* für komplexere Aufgaben; ein Skill lebt in einem eigenen Unterverzeichnis und kann neben der Markdown Datei noch weitere Ressourcen wie PDFs, Skripte, Beispiele etc enthalten
- [**MCP**](https://code.visualstudio.com/docs/agent-customization/mcp-servers) - Model Context Protocol; externer Dienst (Tool), der das MCP verwendet
- [**Hook**](https://code.visualstudio.com/docs/agent-customization/hooks) - Möglichkeit, sich an bestimmte Arbeitsschritte des KI Agenten einzuklinken und dann jeweils noch eigene Schritte auszuführen. Beispiel: Automatisches Formatieren von generiertem Quellcode mit `clang-format`

# Dateien

Dateien relativ zur Projektwurzel (`**` heißt, dass an dieser Stelle eine beliebige Anzahl von Unterverzeichnissen stehen kann):

| Datei(en)                                          | Beschreibung      |
|----------------------------------------------------|-------------------|
| `./**/AGENTS.md`[^footnote_agents_md]<br/>`./CLAUDE.md`<br/>`./GEMINI.md` | Always-On Instructions für den gesamten Workspace. Kompatibel mit verschiedenen Tools (GitHub Copilot, Claude Code, Google Antigravity, ...)  |
| `./.github/copilot-instructions.md`                | Always-On Instructions für das *gesamte* Repository.<br/>:exclamation:**GitHub-spezifisch!** |
| `./.github/instructions/**/<name>.instructions.md` | Selektiv aktive Instructions; Auswahl erfolgt mittels Pfadmuster.<br/>:exclamation: **GitHub-spezifisch!** |
| `./.github/prompts/**/<name>.prompt.md`            | Vorgefertigter Prompt (Chat oder Agent)<br/>:exclamation:**GitHub-spezifisch!** |
| `./.github/skills/**/SKILL.md`                     | Skill-Beschreibung (ein Unterordner pro Skill mit weiteren Dateien).<br/>:white_check_mark: [Offener Standard](https://agentskills.io/) |
| `./.vscode/mcp.json`                               | Auflistung verfügbarer MCP Dienste.<br/>:white_check_mark: [MCP ist ein offener Standard](https://modelcontextprotocol.io/) |
| `./.github/hooks/*.json`                           | Hooks<br/>:exclamation:**GitHub-spezifisch!**|

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

Die KI wählt dann einen geeigneten Namen für den Prompt und erstellt eine Datei `.github/prompts/mein-prompt.prompt.md`. Ab jetzt kann dieser vorgefertigte Prompt im KI Chatfanster (im Agent Mode!) durch Eingabe von `/mein-prompt`, gefolgt von etwaigen Parametern und Zusatzinformationen, verwendet werden. In der Regel muss man die erzeugte Markdown Datei jedoch noch anpassen.

# Custom Instructions

[Link mit mehr Informationen](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/add-custom-instructions/add-repository-instructions#creating-path-specific-custom-instructions)

*Instructions* geben dem KI Agenten allgemeine Informationen mit auf den Weg, die entweder für das gesamte Repository oder nur für bestimmte Unterbereiche oder Dateitypen gelten können:

- `./.github/copilot-instructions.md` - Allgemeine Anweisungen an den Agenten, die das gesamte Repository betreffen. Kann nur einmal unter exakt diesem Namen existieren. Diese Datei ist äquivalent zu `AGENTS.md`, `CLAUDE.md` etc, die auch oft anzutreffen sind.
- `./.github/instructions/<name>.instructions.md` - Selektiv gültige Anweisungen, deren Geltungsbereich über einen Filter innerhalb der Datei angegeben wird; es können beliebig viele Dateien dieses Typs existieren

Jede Instruction Datei kann einen optionalen JSON-Preamble Header mit Metainformationen enthalten. Dieser ist zwischen Dreifach-Bindestrichen `---` eingefasst.

Beispiel `cpp.instructions.md`:

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

*Aktiv* heißt hier, dass der Inhalt der Markdown-Datei, abzüglich der Präambel, mit in den Kontext des KI Modells "hineingefüttert" wird. Es ist dabei möglich, dass mehrere Markdown Dateien hineingegeben werden (z.B. wenn eine `AGENTS.md` und gleichzeitig eine oder mehrere `xxx.instructions.md` Dateien mit passendem Pattern existieren).