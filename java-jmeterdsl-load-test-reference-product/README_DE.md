# JMeter Java DSL Performance Test

Dieses Projekt stellt Demo-Performance-Tests als Referenz für Ihr Axon Ivy Projekt bereit und verwendet **JMeter Java DSL**, einen modernen Java-basierten Ansatz für Performance-Tests. Es bietet eine Fluent-API zur programmatischen Erstellung von JMeter-Testplänen und ersetzt XML-basierte Testpläne durch reinen Java-Code.

**Vorteile:**
- **Typsicherheit**: Validierung der Testpläne zur Kompilierzeit
- **IDE-Unterstützung**: Vollständige Debugging-Funktionen
- **Wartbarkeit**: Einfaches Refactoring und Code-Wiederverwendung
- **Versionskontrolle**: Reiner Java-Code statt XML-Dateien
- **Integration**: Nahtlose Integration mit JUnit und Test-Frameworks

## Demo

### DemoTest

Ein einfacher Smoke-Test zur Überprüfung der externen Website-Verfügbarkeit:
- Sendet HTTP-GET-Anfragen an `axonivy.com` und `market.axonivy.com`
- Validiert HTTP-200-Antwortcodes und erwartete Seiteninhalte
- Generiert JTL-Ergebnisdateien und HTML-Berichte

### PerformancePortalTest

Ein vollständiger Portal-Durchlauftest, der eine echte Benutzersitzung simuliert:
1. **Anmeldung** — Authentifizierung mit Zugangsdaten aus einer CSV-Datei
2. **Portal-Startseite** — Navigation zur Startseite
3. **Prozesse** — Öffnen der Prozessliste
4. **Aufgabenliste** — Navigation zum Aufgabenlisten-Dashboard
5. **Fallliste** — Navigation zum Falllisten-Dashboard
6. **Abmeldung** — Beenden der Sitzung

### PerformancePortalTestReviewInGui

Derselbe Portal-Durchlauf wie oben, öffnet jedoch die **JMeter-GUI** (`resultsTreeVisualizer`) zur visuellen Fehlersuche und Analyse von Anfragen/Antworten während der lokalen Entwicklung.

## Setup

### Schnellstart
1. Repository klonen
2. Einfachen Demo-Test ausführen:
```bash
mvn clean test -Dtest=DemoTest
```
3. Für `PerformancePortalTest`:
   - Code mit Axon Ivy Designer öffnen
   - Portal vom Axon Ivy Market installieren (Version muss mit Designer kompatibel sein)
   - Benutzer-Zugangsdaten in `one_user.csv` aktualisieren
   - Ausführen: `mvn clean test -Dtest=PerformancePortalTest`

### Konfiguration

Konfigurieren Sie Ihren Test über `resources/test.properties`:

```
@variables.yaml@
```
