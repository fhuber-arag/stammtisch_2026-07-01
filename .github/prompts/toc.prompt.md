---
description: "Erstellt ein Inhaltsverzeichnis aus einer Markdown-Datei mit Überschriften der ersten beiden Ebenen (H1, H2)"
name: "Inhaltsverzeichnis erstellen"
argument-hint: "Pfad zur Markdown-Datei (optional, Standard: aktuelle Datei)"
---

Erstelle ein Inhaltsverzeichnis für die angegebene Markdown-Datei.

## Regeln

- Berücksichtige **nur** Überschriften der Ebene 1 (`#`) und Ebene 2 (`##`)
- Überschriften tieferer Ebenen (`###`, `####`, usw.) werden ignoriert
- H1-Überschriften sind Einträge der obersten Listenebene
- H2-Überschriften werden als eingerückte Untereinträge (4 Leerzeichen) der vorherigen H1-Überschrift dargestellt
- Jeder Eintrag ist ein Markdown-Link, der auf den entsprechenden Anker in der Datei zeigt
- Anker werden aus dem Überschriftentext gebildet: Kleinbuchstaben, Leerzeichen durch `-` ersetzen, Sonderzeichen entfernen

## Ausgabeformat

Die Ausgabe ist ausschließlich eine Markdown-Liste, ohne zusätzliche Erklärungen oder Codeblöcke.

Beispiel:

- [Einleitung](#einleitung)
    - [Hintergrund](#hintergrund)
    - [Ziele](#ziele)
- [Hauptteil](#hauptteil)
    - [Abschnitt A](#abschnitt-a)
- [Fazit](#fazit)

## Eingabe

$args

Falls kein Argument angegeben wurde, verwende die aktuell geöffnete oder im Kontext referenzierte Markdown-Datei.
