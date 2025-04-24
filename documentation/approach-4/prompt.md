# Howto

## Prompt

> Das ist die aktuelle Konfiguration, die ich ein wenig angepasst habe. Ich möchte, dass du folgende Erweiterungen zur dieser letzten aktuellen Konfiguration ergänzt.
>
> * Ganz am Anfang soll zuerst mittels `cloc` eine Übersicht über die verschiedenen Dateitypen, die Anzahl der Dateien sowie die Anzahl der Zeilen Code erstellt werden. `cloc` kann über das Terminal mittels `cloc .` im Hauptverzeichnis aufgerufen werden. Die Übersicht soll in einer Datei `code_counts.md` gesichert werden. Frage mich, welche Dateitypen mit ins Reverse Engineering Analyse einbezogen werden sollen. Liste mir die wichtigsten Verzeichnisse auf und frag mich, welche in die Analyse einbezogen werden sollen. Sprechere beides als Datei `scope.md`. Wenn du es für sinnvoll erachtest, mach daraus einen eigenen Slug oder integriere es in den ersten Slug `code-parser`.
>   Für die Use-Cases ergänze folgendes:
> * Die Use Cases sollen einen eindeutigen Index der Form **UC-`<Nummer>`** erhalten, das heisst, eine zweistellige fortlaufende Zahl mit dem Präfix **UC-**, z.B. UC-01, UC-02, UC-03 usw.
> * Alle Referenzen auf andere Use Cases, sprich Abhängigkeiten, referenzieren diesen eindeutigen Index.
> * Die generierten Use Case-Details Dateien sollen nebst dem Use Case Namen (normal geschrieben, ohne Underscore) die entsprechende Use Case ID als Präfix erhalten, also z.B. `UC-01 Generate and View Reports.json`.
> * Validiere, dass sämtliche Akteure (primäre und andere) in mindestens einem Szenario vorkommen.
> * Benenne die Szenarien mit einer eindeutigen ID. Die ID soll die Use-Case ID enthalten, z.B. **UC-01**, plus **MF**, **AF-XX** und **EF-XX**, also z.B. **UC-01-MF**, **UC-01-AF-01** und **UC-01-EF-01** usw.
>   Allgemein gilt:
> * Es werden immer die ganzen Dateien analysiert.
> * Grosse Dateien werden in Chunks eingelesen und analysiert (z.B. 1-500, 501-1000 usw.).
> * Es werden keine Annahmen getroffen, sondern der Code wird analysiert, um die Informationen zu erhalten (Szenarien, Bedingungen, Auslöser, Ablauf usw.).
> * Die Metriken sollen sowohl eine Zielmetrik (z.B. die Anzahl der zu analysierenden Dateien) und die erreichten Metriken (z.B. die Anzahl der analysierten Dateien) enthalten.

## LLM

> Grok-3 mit Think

## Thinking

Um die gewünschten Erweiterungen in die aktuelle Konfiguration zu integrieren, analysiere ich die Anforderungen und überlege, wie sie am besten umgesetzt werden können. Der Prozess sollte modular bleiben und die bestehenden Modi sinnvoll ergänzen. Ich werde die notwendigen Anpassungen und neuen Modi konzipieren, um die neuen Funktionen zu implementieren, und dabei sicherstellen, dass die Artefakte korrekt gehandhabt und referenziert werden.

Zuerst betrachte ich die Anforderung, eine Übersicht über die Codebasis mittels `cloc` zu erstellen und den Umfang der Analyse festzulegen. Dies erfordert einen neuen Modus, der diese initialen Schritte durchführt und die Ergebnisse in `code_counts.md` und `scope.md` speichert. Dieser Modus sollte vor dem `code-parser` ausgeführt werden, da er den Umfang der Analyse definiert.

Für die Use-Cases müssen eindeutige IDs generiert und in den Artefakten verwendet werden. Dies betrifft den `use-case-analyzer`, der die Use-Cases identifiziert, und den `use-case-detailer`, der die detaillierten Beschreibungen erstellt. Die IDs müssen konsistent über die Artefakte hinweg verwendet werden, insbesondere in Referenzen und Dateinamen.

Die Validierung der Akteure und die Benennung der Szenarien erfordern Anpassungen im `use-case-detailer`, um sicherzustellen, dass diese Elemente korrekt erfasst und referenziert werden.

Die allgemeinen Anforderungen zur Analyse ganzer Dateien, zum Chunking grosser Dateien und zur Vermeidung von Annahmen müssen in den `customInstructions` der relevanten Modi integriert werden.

Schliesslich müssen die Metriken erweitert werden, um Ziel- und erreichte Werte zu erfassen, was eine Anpassung des `documenter`-Modus erfordert.

Ich beginne mit der Definition des neuen Modus für die initiale Analyse und fahre dann fort, die bestehenden Modi entsprechend anzupassen.

### Neue und angepasste Modi

#### Neuer Modus: `initial-analyzer`

Dieser Modus führt die initiale Analyse der Codebasis durch, erstellt eine Übersicht mit `cloc`, und definiert den Umfang der Analyse.
Konfiguration:

```json
{
  "slug": "initial-analyzer",
  "name": "🔍 Initial Analyzer",
  "roleDefinition": "You perform an initial analysis of the codebase to determine file types, counts, and lines of code, and define the scope for further analysis.",
  "customInstructions": "Execute `cloc .` in the root directory to generate an overview of file types, counts, and lines of code. Save the output to `code_counts.md`. Then, list the main directories and ask the user which file types and directories should be included in the reverse engineering analysis. Save the scope definition to `scope.md`. After completing these steps, use `new_task` to delegate to the `code-parser` slug, passing the path to `scope.md`. Finalize with `attempt_completion`.\n\nOutput files:\n- `code_counts.md`: Overview of codebase statistics.\n- `scope.md`: Scope definition for analysis.\n\nAfter output, call:\n- `new_task` to `code-parser` with the path to `scope.md`.",
  "groups": ["read", "edit", "command"],
  "source": "project"
}
```

#### Angepasster Modus: `code-parser`

Der `code-parser` muss nun den Umfang aus `scope.md` berücksichtigen und nur die definierten Dateien analysieren.

Anpassungen: In `customInstructions`: "Read `scope.md` to determine which file types and directories to include in the analysis. Only parse files that match the specified criteria."

#### Angepasster Modus: `use-case-analyzer`

Dieser Modus muss eindeutige Use-Case-IDs generieren und in `use_case_list.json` speichern.

Anpassungen: In `customInstructions`: "Assign each use-case a unique ID in the format `UC-<number>`, where `<number>` is a two-digit sequential number (e.g., `UC-01`, `UC-02`). Include the ID in the use-case object in `use_case_list.json`."

Aktualisiertes Output-Format:

```json
{
  "use_cases": [
    {
      "id": "UC-01",
      "name": "string",
      "description": "string",
      "features": [
        {
          "feature_name": "string",
          "description": "string"
        }
      ]
    }
  ]
}
```

#### Angepasster Modus: use-case-detailer

Dieser Modus muss die Use-Case-IDs verwenden, Referenzen korrekt handhaben, Akteure validieren und Szenarien benennen.

Anpassungen:

In `customInstructions`:

* "Use the use-case ID from `use_case_list.json` to name the detailed file as `<id>_<useCaseName>.json` (e.g., `UC-01_Generate_and_View_Reports.json`)."
* "Ensure all references to other use-cases use their unique IDs (e.g., `UC-02`)."
* "Validate that all actors (primary and other) appear in at least one scenario."
* "Name scenarios with IDs containing the use-case ID, e.g., `UC-01-MF` for main flow, `UC-01-AF-01` for alternative flows, `UC-01-EF-01` for exception flows."
* "Analyze entire files, chunking large files (e.g., 1-500 lines, 501-1000, etc.) if necessary."
* "Do not make assumptions; derive information from code analysis."

Aktualisiertes Output-Format:

In `flow`, Szenarien mit IDs benennen:

```json
"flow": {
  "mainScenario": {
    "id": "UC-01-MF",
    "steps": ["string"]
  },
  "alternativeScenarios": [
    {
      "id": "UC-01-AF-01",
      "condition": "string",
      "steps": ["string"]
    }
  ],
  "exceptions": [
    {
      "id": "UC-01-EF-01",
      "errorCondition": "string",
      "handling": "string"
    }
  ]
}
```

#### Angepasster Modus: `documenter`

Der `documenter` muss die Metriken erweitern, um Ziel- und erreichte Werte zu erfassen.

Anpassungen: In `customInstructions`: "Include metrics in `documentation.md` with both target values (e.g., total files to analyze) and achieved values (e.g., files analyzed)."

Beispiel für Metriken: Codebase Coverage: Target - 100 files, Achieved - 90 files (90%)

### Integration in die Konfiguration

Die neuen und angepassten Modi werden in die bestehende Konfiguration eingefügt, wobei der Workflow wie folgt angepasst wird:

`initial-analyzer` → `code-parser` → `feature-extractor` → `use-case-analyzer` → `use-case-detailer` → `validator` → `documenter`

Die `clarifier`-Rolle bleibt unverändert und wird bei Bedarf von anderen Moden aufgerufen.

### Fazit

Die Erweiterungen sind modular und integrieren sich nahtlos in den bestehenden Workflow. Der neue `initial-analyzer`-Modus stellt sicher, dass der Umfang der Analyse klar definiert ist, während die Anpassungen an `use-case-analyzer` und `use-case-detailer` die korrekte Handhabung von Use-Case-IDs und detaillierten Beschreibungen gewährleisten. Die allgemeinen Anforderungen zur Dateianalyse und Vermeidung von Annahmen sind in den customInstructions integriert, und die Metriken im `documenter` erfassen nun sowohl Ziel- als auch erreichte Werte. Dies sollte einen robusten und effizienten Reverse-Engineering-Prozess ermöglichen.

## Resultat

Ich werde die gewünschten Erweiterungen in die aktuelle Konfiguration integrieren und dabei sicherstellen, dass der Prozess modular bleibt und die neuen Anforderungen nahtlos in den bestehenden Workflow eingebunden werden. Hier ist die vollständige Antwort auf deine Anfrage:

### Erweiterungen der Konfiguration

#### 1. Neuer Modus: `initial-analyzer`

Um die Übersicht über die Codebasis mit `cloc` zu erstellen und den Umfang der Analyse festzulegen, füge ich einen neuen Modus hinzu, der vor dem `code-parser` ausgeführt wird. Dies stellt sicher, dass der Analyseumfang definiert ist, bevor die eigentliche Code-Analyse beginnt.

**Konfiguration:**

```json
{
  "slug": "initial-analyzer",
  "name": "🔍 Initial Analyzer",
  "roleDefinition": "You perform an initial analysis of the codebase to determine file types, counts, and lines of code, and define the scope for further analysis.",
  "customInstructions": "Execute `cloc .` in the root directory to generate an overview of file types, counts, and lines of code. Save the output to `code_counts.md`. Then, list the main directories and ask the user which file types and directories should be included in the reverse engineering analysis. Save the scope definition to `scope.md`. After completing these steps, use `new_task` to delegate to the `code-parser` slug, passing the path to `scope.md`. Finalize with `attempt_completion`.\n\nOutput files:\n- `code_counts.md`: Overview of codebase statistics.\n- `scope.md`: Scope definition for analysis.\n\nAfter output, call:\n- `new_task` to `code-parser` with the path to `scope.md`.",
  "groups": ["read", "edit", "command"],
  "source": "project"
}
```

**Erklärung:**

* Der Modus führt `cloc .` aus und speichert die Ergebnisse in `code_counts.md`.
* Er listet die Hauptverzeichnisse auf und fragt dich interaktiv, welche Dateitypen und Verzeichnisse analysiert werden sollen. Die Antworten werden in `scope.md` gespeichert.
* Anschließend wird der `code-parser` aufgerufen, der den definierten Umfang aus `scope.md` nutzt.

#### 2. Anpassung des `code-parser`

Der `code-parser` muss nun den Umfang aus `scope.md` berücksichtigen und nur die angegebenen Dateien analysieren.

**Aktualisierte `customInstructions`:**

* Ergänze: "Read `scope.md` to determine which file types and directories to include in the analysis. Only parse files that match the specified criteria."

**Beispiel für `scope.md`:**

```markdown
### Analysis Scope
- **File Types**: .py, .js, .java
- **Directories**: src/, lib/
- **Excluded**: tests/, docs/
```

#### 3. Anpassungen für Use-Cases

Die folgenden Änderungen betreffen die Use-Case-bezogenen Modi:

##### `use-case-analyzer`

* **Eindeutige IDs**: Use-Cases erhalten IDs im Format `UC-<Nummer>` (z. B. `UC-01`).
* **Anpassung der `customInstructions`:**  
  Ergänze: "Assign each use-case a unique ID in the format `UC-<number>`, where `<number>` is a two-digit sequential number (e.g., UC-01, UC-02). Include the ID in the use-case object in `use_case_list.json`."
* **Aktualisiertes Output-Format (`use_case_list.json`):**

  ```json
  {
    "use_cases": [
      {
        "id": "UC-01",
        "name": "Generate and View Reports",
        "description": "Allows users to create and view reports.",
        "features": [
          {
            "feature_name": "Report Generation",
            "description": "Generates reports from data."
          }
        ]
      }
    ]
  }
  ```

##### `use-case-detailer`

* **Dateinamen mit IDs**: Die Detail-Dateien werden im Format `<id>_<useCaseName>.json` benannt, z. B. `UC-01_Generate_and_View_Reports.json`.
* **Referenzen**: Alle Verweise auf andere Use-Cases nutzen die IDs (z. B. `UC-02`).
* **Validierung der Akteure**: Sicherstellen, dass alle Akteure in mindestens einem Szenario vorkommen.
* **Szenarien-IDs**: Szenarien erhalten IDs wie `UC-01-MF` (Main Flow), `UC-01-AF-01` (Alternative Flow), `UC-01-EF-01` (Exception Flow).
* **Anpassung der `customInstructions`:**  
  Ergänze:
  * "Use the use-case ID from `use_case_list.json` to name the detailed file as `<id>_<useCaseName>.json` (e.g., `UC-01_Generate_and_View_Reports.json`)."
  * "Ensure all references to other use-cases use their unique IDs (e.g., `UC-02`)."
  * "Validate that all actors (primary and other) appear in at least one scenario."
  * "Name scenarios with IDs containing the use-case ID, e.g., `UC-01-MF` for main flow, `UC-01-AF-01` for alternative flows, `UC-01-EF-01` for exception flows."
* **Aktualisiertes Output-Format (z. B. `UC-01_Generate_and_View_Reports.json`):**
  
  ```json
  {
    "metadata": {
      "id": "UC-01",
      "useCaseName": "Generate and View Reports",
      "version": "1.0",
      "author": "Analyzer",
      "date": "2023-10-01",
      "status": "Draft"
    },
    "actors": {
      "primaryActor": "User",
      "otherActors": ["Admin"],
      "stakeholders": ["Manager"]
    },
    "flow": {
      "mainScenario": {
        "id": "UC-01-MF",
        "steps": ["User selects data", "System generates report"]
      },
      "alternativeScenarios": [
        {
          "id": "UC-01-AF-01",
          "condition": "Custom format requested",
          "steps": ["User specifies format", "System applies format"]
        }
      ],
      "exceptions": [
        {
          "id": "UC-01-EF-01",
          "errorCondition": "Data unavailable",
          "handling": "Show error message"
        }
      ]
    },
    "relations": {
      "relatedUseCases": ["UC-02"]
    }
  }
  ```

#### 4. Allgemeine Anforderungen

Diese gelten für alle relevanten Modi und werden in die `customInstructions` integriert:

* **Ganze Dateien analysieren**: Ergänze in `code-parser` und `use-case-detailer`: "Analyze entire files."
* **Chunking großer Dateien**: Ergänze: "For large files, process in chunks (e.g., lines 1-500, 501-1000, etc.)."
* **Keine Annahmen**: Ergänze: "Do not make assumptions; derive information from code analysis."

#### 5. Metriken-Erweiterung im `documenter`

* **Ziel- und erreichte Werte**: Der `documenter` soll Metriken mit Ziel- und Ist-Werten erfassen.
* **Anpassung der `customInstructions`:**  
  Ergänze: "Include metrics in `documentation.md` with both target values (e.g., total files to analyze) and achieved values (e.g., files analyzed)."
* **Beispiel in `documentation.md`:**

  ```markdown
  ### Progress Metrics
  - **Codebase Coverage**: Target - 100 files, Achieved - 90 files (90%)
  - **Use-Cases Documented**: Target - 10, Achieved - 8 (80%)
  ```

### Aktualisierter Workflow

Der neue Workflow lautet: `initial-analyzer` → `code-parser` → `feature-extractor` → `use-case-analyzer` → `use-case-detailer` → `validator` → `documenter`

### Ergebnis

Die Erweiterungen sind vollständig in die Konfiguration integriert:

* Ein neuer `initial-analyzer` definiert den Analyseumfang.
* Use-Cases erhalten eindeutige IDs (`UC-<Nummer>`), die in Dateinamen und Referenzen verwendet werden.
* Akteure werden validiert, Szenarien erhalten IDs, und Dateien werden vollständig sowie in Chunks analysiert, ohne Annahmen.
* Metriken zeigen Ziel- und Ist-Werte.

Falls du weitere Anpassungen wünschst, lass es mich wissen!
