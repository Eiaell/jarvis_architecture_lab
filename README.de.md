# Jarvis — Persönliches agentisches KI-System

[English](README.md) · [Español](README.es.md) · [Deutsch](README.de.md)

> In Entwicklung.

Jarvis ist ein persönliches agentisches KI-System, das ich als praktische Lern- und Entwicklungsumgebung aufbaue. Dabei untersuche ich, wie zuverlässige KI-Agenten Kontext behalten, mit Gedächtnis arbeiten, Werkzeuge verwenden und mehrstufige Arbeitsabläufe ausführen können.

Das Projekt entstand aus einer einfachen Frage:

**Wie kann ein KI-Assistent mit der Zeit nützlicher werden, ohne ausschließlich von immer größeren Prompts oder unkontrolliertem LLM-Verhalten abhängig zu sein?**

## Was ich untersuche

Jarvis dient derzeit als Lern- und Experimentierumgebung für Themen wie:

- persistentes Gedächtnis
- strukturierte Kontextabfrage
- Trennung zwischen Gedächtnis und aktivem Kontext
- Werkzeugausführung
- mehrstufige Workflows
- deterministisches vs. probabilistisches Verhalten
- Berechtigungen und Grenzen von Agenten
- Validierung und Evaluation
- wiederverwendbare Architekturmuster

## Aktueller Stand

Jarvis befindet sich in aktiver Entwicklung.

Ziel dieses Repositories ist nicht, ein fertiges Produkt zu präsentieren.

Stattdessen dokumentiere ich hier die Architektur, Entscheidungen, Experimente, Fehler und Erkenntnisse, die während der Entwicklung entstehen.

## Grundidee

Ein LLM sollte nicht für jede Garantie innerhalb eines agentischen Systems verantwortlich sein.

Einige Aufgaben eignen sich für probabilistisches Schlussfolgern.

Andere benötigen deterministische Software, explizite Validierung und kontrollierte Ausführung.

Ein wichtiger Teil dieses Projekts besteht darin herauszufinden, wo diese Grenze verlaufen sollte.

## Architektur auf hoher Ebene

User  
↓  
Interaction Layer  
↓  
Context Retrieval  
↓  
Memory System  
↓  
Reasoning / Decision Layer  
↓  
Tool Execution  
↓  
Validation  
↓  
Result

Die genaue Architektur entwickelt sich weiter, während das System getestet und verbessert wird.

## Inhalt dieses Repositories

Dieses öffentliche Repository enthält:

- Architekturdokumentation
- Designentscheidungen
- bereinigte Beispiele
- Evaluationsansätze
- gewonnene Erkenntnisse
- wiederverwendbare Muster

Bewusst **nicht enthalten** sind:

- persönliche Erinnerungen
- private Benutzerdaten
- Zugangsdaten oder API-Schlüssel
- Produktionskonfigurationen
- private Prompts
- sensible Logs

## Warum ich Jarvis entwickle

Ich entwickle mein Profil zunehmend in Richtung künstliche Intelligenz, Automatisierung und digitale Systeme.

Anstatt ausschließlich über Kurse zu lernen, nutze ich Jarvis als praktisches Projekt, in dem ich reale Architekturprobleme untersuchen, verschiedene Ansätze testen und dokumentieren kann, was funktioniert und was nicht.

Langfristig möchte ich die Erkenntnisse aus Jarvis in einen wiederverwendbaren Blueprint für persönliche oder domänenspezifische KI-Agenten überführen.

## Werkzeuge

Für Entwicklung und Experimente nutze ich unter anderem:

- Claude Code
- OpenAI Codex
- Git
- GitHub
- Python sowie weitere Werkzeuge, wenn sie sinnvoll sind

## Dokumentationsprinzip

Für jede größere Komponente versuche ich folgende Punkte zu dokumentieren:

1. Welches Problem sie löst
2. Warum die Komponente existiert
3. Welche Alternativen betrachtet wurden
4. Welche Lösung gewählt wurde
5. Was deterministisch sein sollte
6. Was probabilistisch bleiben kann
7. Mögliche Fehlermodi
8. Tests und Evaluationen
9. Akzeptanzkriterien
10. Wann das Muster wiederverwendet werden sollte und wann nicht

## Hinweis

Jarvis ist ein experimentelles persönliches Projekt und sollte in seinem aktuellen Zustand nicht als produktionsreifer autonomer Agent betrachtet werden.
