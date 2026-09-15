# Jarvis — Persönliches agentisches KI-System

[English](README.md) · [Español](README.es.md) · [Deutsch](README.de.md)

> **Nur-Lese- / Portfolio-Repository.** Dieses Repository dokumentiert die Architektur von Jarvis, einem persönlichen agentischen KI-Assistenten. Es ist nicht das reale Produktionssystem: Es handelt sich um eine öffentliche, bereinigte Version, die Designentscheidungen zeigen soll, nicht zur Ausführung gedacht ist. Die private Implementierung wird in einem separaten Repository gepflegt. Siehe [SECURITY.md](SECURITY.md) für Details zu dem, was veröffentlicht wird und was nicht.

## Inhaltsverzeichnis

- [Was Jarvis ist](#was-jarvis-ist)
- [Welches Problem es löst](#welches-problem-es-löst)
- [Was ich erforsche](#was-ich-erforsche)
- [Architektur auf hoher Ebene](#architektur-auf-hoher-ebene)
- [Zentrale Architekturentscheidungen](#zentrale-architekturentscheidungen)
- [Technische Dokumentation](#technische-dokumentation)
- [Was dieses Repository enthält und nicht enthält](#was-dieses-repository-enthält-und-nicht-enthält)
- [Dokumentationsphilosophie](#dokumentationsphilosophie)
- [Werkzeuge](#werkzeuge)
- [Aktueller Status und Hinweis](#aktueller-status-und-hinweis)

## Was Jarvis ist

Jarvis ist ein persönliches agentisches KI-System, das ich als praktische Lernumgebung aufbaue, um zuverlässige KI-Agenten zu entwerfen: Agenten, die in der Lage sind, Kontext zu behalten, mit persistentem Gedächtnis zu arbeiten, externe Werkzeuge zu nutzen und mehrstufige Arbeitsabläufe ohne ständige Aufsicht auszuführen.

Das Projekt entstand aus einer konkreten Frage:

> **Wie kann ein KI-Assistent mit der Zeit nützlicher werden, ohne sich allein auf immer größere Prompts oder auf unkontrolliertes Modellverhalten zu verlassen?**

## Welches Problem es löst

Die meisten LLM-basierten Assistenten verlieren zwischen Sitzungen den Kontext, unterscheiden nicht zwischen "sich an etwas erinnern" und "es im aktuellen Kontextfenster haben" und übertragen dem Modell Entscheidungen, die eigentlich deterministisch sein sollten (Berechnungen, Validierungen, Berechtigungen). Jarvis untersucht, wie diese Verantwortlichkeiten getrennt werden können: was das Modell durch probabilistisches Denken lösen soll und was deterministischer Code mit expliziten Regeln lösen soll.

## Was ich erforsche

Jarvis dient als aktive Lern- und Experimentierumgebung für:

- persistentes Gedächtnis
- strukturierte Kontextabfrage
- Trennung von Gedächtnis und aktivem Kontext
- Werkzeugausführung
- mehrstufige Arbeitsabläufe
- deterministisches vs. probabilistisches Verhalten
- Berechtigungen und Grenzen des Agenten
- Validierung und Evaluation
- wiederverwendbare Architekturmuster

## Architektur auf hoher Ebene

```
Nutzer
  ↓
Interaktionsschicht
  ↓
Kontextabfrage
  ↓
Gedächtnissystem
  ↓
Reasoning- / Entscheidungsschicht
  ↓
Werkzeugausführung
  ↓
Validierung
  ↓
Ergebnis
```

Die genaue Architektur entwickelt sich weiter, während das System in der Praxis getestet wird. Die vollständigen Details, Schicht für Schicht, finden sich in [`docs/architecture/system-overview.md`](docs/architecture/system-overview.md).

## Zentrale Architekturentscheidungen

Für alle, die die technische Tiefe des Projekts bewerten möchten, sind dies die Entscheidungen, die Jarvis am stärksten prägen:

- **Das Modell ist nicht für jede Garantie im System verantwortlich.** Arithmetik, Geschäftsregeln und Berechtigungsgrenzen werden von deterministischem Code ausgeführt; das LLM schlägt vor, der Code entscheidet. Siehe [`docs/05-deterministic-boundaries.md`](docs/05-deterministic-boundaries.md).
- **Gedächtnis getrennt vom aktiven Kontext.** Etwas zu persistieren bedeutet nicht, dass sich das Modell in jedem Turn daran "erinnert": Es gibt eine explizite Abrufschicht, die entscheidet, was in den Prompt aufgenommen wird und warum. Siehe [`docs/02-memory-architecture.md`](docs/02-memory-architecture.md) und [`docs/03-context-retrieval.md`](docs/03-context-retrieval.md).
- **Geheimnisse erreichen das Modell nie.** Das LLM erhält Zugriff auf eine *Fähigkeit* (ein Werkzeug), nicht auf das Geheimnis, das dieses Werkzeug zur Ausführung benötigt. Siehe den Abschnitt über Geheimnisse in [`docs/04-tool-execution.md`](docs/04-tool-execution.md) und in [SECURITY.md](SECURITY.md).
- **Alles, was entscheidet, vorhersagt oder ausführt, wird validiert, bevor man ihm vertraut.** Evaluationsansätze und explizite Abnahmekriterien, bevor eine neue Fähigkeit als gut befunden wird. Siehe [`docs/06-evaluations.md`](docs/06-evaluations.md).
- **Fehler werden dokumentiert, nicht verborgen.** Jede gelernte Lektion — einschließlich dessen, was nicht funktioniert hat — wird als Grundlage für die nächste Entscheidung festgehalten. Siehe [`docs/07-lessons-learned.md`](docs/07-lessons-learned.md).

## Technische Dokumentation

| Dokument | Inhalt |
|---|---|
| [`docs/01-overview.md`](docs/01-overview.md) | Überblick über die Architektur |
| [`docs/02-memory-architecture.md`](docs/02-memory-architecture.md) | Architektur des persistenten Gedächtnisses |
| [`docs/03-context-retrieval.md`](docs/03-context-retrieval.md) | Strukturierte Kontextabfrage |
| [`docs/04-tool-execution.md`](docs/04-tool-execution.md) | Werkzeugausführung und Umgang mit Geheimnissen |
| [`docs/05-deterministic-boundaries.md`](docs/05-deterministic-boundaries.md) | Grenzen zwischen deterministisch und probabilistisch |
| [`docs/06-evaluations.md`](docs/06-evaluations.md) | Evaluationsmethoden und Abnahmekriterien |
| [`docs/07-lessons-learned.md`](docs/07-lessons-learned.md) | Gelernte Lektionen (lebendes Dokument) |
| [`docs/architecture/system-overview.md`](docs/architecture/system-overview.md) | Vollständige Systemarchitektur, Schicht für Schicht |

## Was dieses Repository enthält und nicht enthält

Dieses öffentliche Repository enthält architektonische Dokumentation, Designentscheidungen, bereinigte Beispiele, Evaluationsansätze, gelernte Lektionen und wiederverwendbare Muster.

Absichtlich enthält es **nicht** persönliche Erinnerungen, private Nutzerdaten, Zugangsdaten oder API-Schlüssel, Produktionskonfiguration, private Prompts oder sensible Protokolle.

Die vollständigen Details darüber, was veröffentlicht wird, was nicht, und warum, stehen in [SECURITY.md](SECURITY.md).

## Dokumentationsphilosophie

Für jede wichtige Komponente versuche ich zu dokumentieren:

1. Das Problem, das sie löst
2. Warum die Komponente existiert
3. Erwogene Alternativen
4. Der gewählte Ansatz
5. Was deterministisch sein muss
6. Was probabilistisch bleiben kann
7. Fehlermodi
8. Tests und Evaluationen
9. Abnahmekriterien
10. Wann das Muster wiederverwendet werden sollte und wann nicht

## Werkzeuge

Das Projekt wird mit Werkzeugen wie den folgenden entwickelt und erforscht:

- Claude Code
- OpenAI Codex
- Git
- GitHub
- Python und andere Hilfswerkzeuge, sofern benötigt

## Aktueller Status und Hinweis

Jarvis befindet sich in aktiver Entwicklung. Das Ziel dieses Repositorys ist nicht, ein fertiges Produkt zu präsentieren, sondern die Architektur, die Entscheidungen, die Experimente, die Fehler und die während des Aufbaus des Systems gelernten Lektionen zu dokumentieren.

Jarvis ist ein persönliches, experimentelles Projekt. In seinem aktuellen Zustand sollte es nicht als produktionsreifer autonomer Agent betrachtet werden, noch sollte dieses Repository als formale Sicherheitsprüfung interpretiert werden (siehe [SECURITY.md](SECURITY.md)).

---

**Autor:** [Engelbert Huber](https://github.com/Eiaell) — weitere Projekte und Kontext auf meinem [GitHub-Profil](https://github.com/Eiaell).
