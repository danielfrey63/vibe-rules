# SPARC Methodology Prompt for Vibe Rules Development

This prompt is designed to guide the development of vibe-based rules for image generation systems using the SPARC methodology as outlined in the `.roomodes` file. It breaks down the process into modular tasks aligned with specialized roles to ensure secure, maintainable, and testable outputs.

## Core Objectives
- **Define Vibe Characteristics**: Identify visual elements contributing to specific moods or vibes in images.
- **Establish Rule Framework**: Create a set of rules for generating or evaluating images based on vibe criteria.
- **Ensure Scalability**: Develop flexible rules that adapt to various styles and evolving visual trends.

## SPARC Workflow for Vibe Rules
Follow the SPARC methodology to orchestrate complex workflows:

1. **Specification (via `spec-pseudocode`)** [SF]
   - Clarify objectives and scope for vibe rules.
   - Translate requirements into modular pseudocode with TDD anchors for future testing.
   - Ensure no hard-coded values or secrets are included.

2. **Architecture (via `architect`)** [CA]
   - Design a scalable structure for vibe rule application.
   - Define boundaries and integration points using diagrams (e.g., Mermaid).
   - Focus on modularity and extensibility without environment-specific configs.

3. **Implementation (via `code`)** [RP]
   - Write clean, modular code for rule implementation based on pseudocode and architecture.
   - Keep files under 500 lines and use configuration abstractions.
   - Delegate subtasks with `new_task` and finalize with `attempt_completion`.

4. **Testing (via `tdd`)** [TDT]
   - Implement Test-Driven Development by writing failing tests first.
   - Develop minimal code to pass tests, then refactor for clarity.
   - Maintain modularity and ensure test coverage before completion.

5. **Debugging (via `debug`)** [REH]
   - Troubleshoot logic errors or integration issues in rule application.
   - Use logs and traces to isolate bugs without altering env configs.
   - Keep fixes modular and summarize resolutions with `attempt_completion`.

6. **Security Review (via `security-review`)** [SFT]
   - Audit rules for potential security risks or hardcoded values.
   - Recommend mitigations and flag oversized files (>500 lines).
   - Delegate sub-audits with `new_task` and finalize findings.

7. **Documentation (via `docs-writer`)** [SD]
   - Create concise Markdown documentation for vibe rules.
   - Use sections and examples, keeping files under 500 lines.
   - Summarize documentation efforts with `attempt_completion`.

8. **Integration (via `integration`)** [ISA]
   - Merge outputs into a cohesive, production-ready rule set.
   - Verify compatibility and consistency across modules.
   - Use `new_task` for conflict resolution and summarize integrations.

9. **Post-Deployment Monitoring (via `post-deployment-monitoring-mode`)** [PA]
   - Observe rule performance and feedback post-launch.
   - Set up metrics and alerts for unexpected behaviors.
   - Recommend improvements and escalate issues with `new_task`.

10. **Refinement and Optimization (via `refinement-optimization-mode`)** [DRY]
    - Refactor rules for clarity, modularity, and performance.
    - Break down large components and optimize structure.
    - Delegate changes and finalize with `attempt_completion`.

## Guidelines for Development
- **Modularity [AC]**: Keep tasks and files small (<500 lines) for traceability.
- **Security [SFT]**: Avoid hardcoding secrets or environment variables.
- **Workflow [CD]**: Commit changes regularly with semantic messages.
- **Transparency [TR]**: Reference SPARC roles and rules (e.g., [SF], [RP]) in decisions.
- **Completion [CWM]**: Use `attempt_completion` to finalize tasks and summarize outcomes.

This prompt ensures a structured approach to developing vibe rules, leveraging specialized roles and maintaining best practices for modularity and security. Use `new_task` to delegate effectively and keep the process engaging with clear, emoji-supported communication as per SPARC's tone.
