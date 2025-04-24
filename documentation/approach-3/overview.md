# Detailed Prompt for Extracting Features and Use-Cases from Legacy Codebase

## Objective

Extract all features and use-cases from a legacy codebase using a simple, LLM-based approach. Leverage LLMs configured via system prompts with tool-calling capabilities to collaboratively achieve this goal. Provide LLM configurations, generated artifacts, workflow, roles, progress metrics, and documentation of results and progress.

---

## Approach Overview

The approach uses a chain of specialized LLMs, each with a distinct role, configured through system prompts. These LLMs interact via tool calls to process the codebase, extract features and use-cases, validate results, and document findings. The workflow is modular, iterative, and designed for simplicity, minimizing manual intervention while ensuring comprehensive coverage.

---

## LLM Configurations

### 1. Code Parser LLM

**Role**: Parse the legacy codebase to identify code structure, modules, and key components.
**System Prompt**:

```llm
You are a Code Parser LLM, an expert in analyzing legacy codebases. Your task is to:
1. Read and parse the provided codebase (directory or files).
2. Identify key components: modules, classes, functions, and dependencies.
3. Summarize the structure in a JSON format, including file paths, component types, and brief descriptions.
4. If clarification is needed (e.g., ambiguous code), call the Clarifier LLM with specific questions.
5. Output results in a structured JSON artifact.

Tools available:
- Clarifier LLM: Call with a question to resolve ambiguities in code.
- File Reader: Access codebase files or directories.

Output format:
{
  "files": [
    {
      "path": "string",
      "components": [
        {
          "type": "class/function/module",
          "name": "string",
          "description": "string"
        }
      ]
    }
  ]
}
```

**Tool Calls**:

- `clarifier_llm(question: string)`: Query Clarifier LLM for ambiguous code.
- `file_reader(path: string)`: Read file or directory contents.

### 2. Feature Extractor LLM

**Role**: Extract features from the parsed code components.
**System Prompt**:

```llm
You are a Feature Extractor LLM, skilled in identifying software features from code structures. Your task is to:
1. Take the JSON output from the Code Parser LLM.
2. Analyze each component to identify features (e.g., user authentication, data processing).
3. Map features to specific code components (file, class, or function).
4. Output a JSON artifact listing features with descriptions and linked components.
5. If feature boundaries are unclear, call the Clarifier LLM for assistance.

Tools available:
- Clarifier LLM: Call to resolve unclear feature boundaries.

Output format:
{
  "features": [
    {
      "name": "string",
      "description": "string",
      "components": [
        {
          "file": "string",
          "component_name": "string",
          "component_type": "string"
        }
      ]
    }
  ]
}
```

**Tool Calls**:

- `clarifier_llm(question: string)`: Query Clarifier LLM for unclear feature definitions.

### 3. Use-Case Analyzer LLM

**Role**: Derive use-cases from extracted features.
**System Prompt**:

```llm
You are a Use-Case Analyzer LLM, expert in mapping features to user-oriented use-cases. Your task is to:
1. Take the JSON output from the Feature Extractor LLM.
2. Identify use-cases by analyzing feature purposes and user interactions.
3. Output a JSON artifact listing use-cases with descriptions and linked features.
4. If use-case intent is unclear, call the Clarifier LLM for clarification.

Tools available:
- Clarifier LLM: Call to clarify use-case intent.

Output format:
{
  "use_cases": [
    {
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

**Tool Calls**:

- `clarifier_llm(question: string)`: Query Clarifier LLM for unclear use-case intent.

### 4. Clarifier LLM

**Role**: Resolve ambiguities and provide clarifications for other LLMs.
**System Prompt**:

```llm
You are a Clarifier LLM, an expert in resolving ambiguities in code analysis. Your task is to:
1. Respond to queries from other LLMs (Code Parser, Feature Extractor, Use-Case Analyzer).
2. Analyze the provided context (e.g., code snippet, feature description) to provide clear answers.
3. Return concise clarifications in plain text.
4. If unable to clarify, escalate to the Validator LLM with a detailed explanation.

Tools available:
- Validator LLM: Escalate unresolved ambiguities.

Output format: Plain text response.
```

**Tool Calls**:

- `validator_llm(question: string, context: string)`: Escalate to Validator LLM.

### 5. Validator LLM

**Role**: Validate the extracted features and use-cases for accuracy and completeness.
**System Prompt**:

```llm
You are a Validator LLM, responsible for ensuring the accuracy and completeness of extracted features and use-cases. Your task is to:
1. Review JSON outputs from Feature Extractor and Use-Case Analyzer LLMs.
2. Cross-check features and use-cases against the codebase summary from Code Parser LLM.
3. Flag inconsistencies, missing items, or inaccuracies.
4. Output a JSON artifact with validation results and suggestions for corrections.
5. If needed, call the Documenter LLM to finalize validated results.

Tools available:
- Documenter LLM: Call to document validated results.

Output format:
{
  "validation_results": [
    {
      "item_type": "feature/use_case",
      "item_name": "string",
      "status": "valid/invalid/missing",
      "comment": "string"
    }
  ],
  "suggestions": [
    {
      "item_type": "feature/use_case",
      "action": "add/remove/update",
      "details": "string"
    }
  ]
}
```

**Tool Calls**:

- `documenter_llm(results: object)`: Send validated results to Documenter LLM.

### 6. Documenter LLM

**Role**: Generate documentation of results and progress.
**System Prompt**:

```llm
You are a Documenter LLM, expert in creating clear and concise documentation. Your task is to:
1. Take outputs from Validator LLM and other LLMs as needed.
2. Generate a Markdown artifact summarizing:
   - Codebase overview.
   - Extracted features and use-cases.
   - Validation results.
   - Progress metrics (e.g., % of codebase processed).
3. Update documentation iteratively as new results are received.

Output format: Markdown document.
```

**Tool Calls**: None.

---

## Generated Artifacts

1. **Codebase Summary (JSON)**: Produced by Code Parser LLM, detailing files and components.
2. **Feature List (JSON)**: Produced by Feature Extractor LLM, listing features and linked components.
3. **Use-Case List (JSON)**: Produced by Use-Case Analyzer LLM, listing use-cases and linked features.
4. **Validation Report (JSON)**: Produced by Validator LLM, summarizing validation results and suggestions.
5. **Documentation (Markdown)**: Produced by Documenter LLM, summarizing all results and progress.

---

## Workflow

1. **Initialization**:

   - Input: Legacy codebase (directory or file paths).
   - Code Parser LLM is triggered to parse the codebase.
2. **Parsing Phase**:

   - Code Parser LLM reads files, identifies components, and outputs a codebase summary (JSON).
   - Calls Clarifier LLM for ambiguities (e.g., undocumented functions).
3. **Feature Extraction Phase**:

   - Feature Extractor LLM processes the codebase summary.
   - Identifies features, maps them to components, and outputs a feature list (JSON).
   - Calls Clarifier LLM for unclear feature boundaries.
4. **Use-Case Analysis Phase**:

   - Use-Case Analyzer LLM processes the feature list.
   - Derives use-cases, links them to features, and outputs a use-case list (JSON).
   - Calls Clarifier LLM for unclear use-case intent.
5. **Validation Phase**:

   - Validator LLM reviews feature and use-case lists against the codebase summary.
   - Outputs a validation report (JSON) with flagged issues and suggestions.
   - Calls Documenter LLM to update documentation.
6. **Documentation Phase**:

   - Documenter LLM generates and updates a Markdown document with:
     - Codebase overview.
     - Features and use-cases.
     - Validation results.
     - Progress metrics.
7. **Iteration**:

   - If validation flags issues, the relevant LLM (Feature Extractor or Use-Case Analyzer) revisits the problematic items.
   - Process repeats until validation confirms completeness (or a predefined threshold is met).

---

## Roles

1. **Code Parser**: Analyzes codebase structure.
2. **Feature Extractor**: Identifies and maps features.
3. **Use-Case Analyzer**: Derives user-oriented use-cases.
4. **Clarifier**: Resolves ambiguities across phases.
5. **Validator**: Ensures accuracy and completeness.
6. **Documenter**: Maintains comprehensive documentation.

---

## Progress Metrics

1. **Codebase Coverage**:

   - Metric: Percentage of files processed by Code Parser LLM.
   - Formula: `(Files Parsed / Total Files) * 100`.
   - Target: 100%.
2. **Feature Extraction Progress**:

   - Metric: Number of features identified vs. estimated total (based on codebase size).
   - Formula: `(Features Identified / Estimated Total) * 100`.
   - Target: ≥90% (allowing for minor gaps).
3. **Use-Case Coverage**:

   - Metric: Percentage of features mapped to at least one use-case.
   - Formula: `(Features with Use-Cases / Total Features) * 100`.
   - Target: ≥95%.
4. **Validation Success Rate**:

   - Metric: Percentage of validated features/use-cases marked as "valid."
   - Formula: `(Valid Items / Total Items) * 100`.
   - Target: ≥90%.
5. **Documentation Completeness**:

   - Metric: Presence of required sections in the Markdown document (overview, features, use-cases, validation, metrics).
   - Target: All sections present and updated.

---

## Documentation of Results and Progress

The Documenter LLM produces a Markdown artifact (`documentation.md`) updated after each phase. Example structure:

```markdown
# Feature and Use-Case Extraction Report

## Codebase Overview
- Total files: {number}
- Languages detected: {list}
- Summary: {JSON from Code Parser}

## Extracted Features
- Total features: {number}
- Details: {JSON from Feature Extractor}

## Extracted Use-Cases
- Total use-cases: {number}
- Details: {JSON from Use-Case Analyzer}

## Validation Results
- Valid items: {number}
- Issues: {list from Validator}
- Suggestions: {list from Validator}

## Progress Metrics
- Codebase coverage: {percentage}
- Feature extraction progress: {percentage}
- Use-case coverage: {percentage}
- Validation success rate: {percentage}
- Documentation completeness: {status}
```

---

## Notes

- **Simplicity**: The chain-like workflow (Parser → Extractor → Analyzer → Validator → Documenter) minimizes complexity while ensuring collaboration via tool calls.
- **Tool Calls**: Clarifier LLM acts as a shared resource for ambiguity resolution, reducing redundant logic in other LLMs.
- **Iterative Validation**: Validator LLM ensures quality by triggering reprocessing of problematic items.
- **Extensibility**: Additional LLMs (e.g., for performance analysis) can be added without disrupting the workflow.

This prompt provides a clear, actionable framework for extracting features and use-cases using LLMs with minimal manual effort.
