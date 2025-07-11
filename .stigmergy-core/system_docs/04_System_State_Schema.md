# Stigmergy System State Schema

This document defines the official schema for the `.ai/state.json` file. This file is the **sacred ledger** of the swarm's activity and the sole source of truth for project state. All agents MUST read from and write to this file in accordance with this schema.

## Root Object

- **`schema_version`** (String): The version of this schema. e.g., "2.0".
- **`project_name`** (String): The user-defined name of the project.
- **`autonomy_mode`** (Enum: "supervised" | "autonomous"): Determines if the swarm proceeds automatically after planning.
- **`project_status`** (Enum): The current high-level status of the project.
  - `NEEDS_BRIEFING`: Awaiting creation of `docs/brief.md`.
  - `NEEDS_PLANNING`: Awaiting creation of PRD, architecture, and project manifest.
  - `READY_FOR_EXECUTION`: Blueprint is complete, swarm can begin building.
  - `EXECUTION_IN_PROGRESS`: Actively working on stories.
  - `EXECUTION_HALTED`: An un-recoverable error occurred.
  - `PROJECT_COMPLETE`: All manifest epics are complete.
- **`system_signal`** (String): The last signal left by an agent, used for inter-agent communication. e.g., "INIT", "STORY_CREATED", "ESCALATION_REQUIRED".
- **`project_manifest`** (Object): The master plan derived from the PRD.
  - **`epics`** (Array of Objects): A list of all project epics.
    - `epic_id` (Number): e.g., 1.
    - `title` (String): The title of the epic.
    - `status` (Enum: "PENDING" | "IN_PROGRESS" | "COMPLETE").
    - `stories` (Array of Objects): A list of stories within the epic.
      - `story_id` (String): e.g., "1.1".
      - `title` (String): The title of the story.
      - `status` (Enum: "PENDING" | "IN_PROGRESS" | "AWAITING_VERIFICATION" | "COMPLETE" | "FAILED").
- **`history`** (Array of Objects): The **immutable ledger** of all system actions. This is append-only.
  - `timestamp` (String): ISO 8601 timestamp.
  - `agent_id` (String): The ID of the acting agent (e.g., "saul", "james").
  - `signal` (String): The signal generated by the agent's action.
  - `summary` (String): A human-readable summary of the action taken.
  - `files_modified` (Array of Strings): A list of file paths created or changed during the action.
- **`issue_log`** (Array of Objects): A log of persistent failures requiring a Responder.
  - `issue_id` (String): A unique ID for the issue.
  - `timestamp` (String): ISO 8601 timestamp when the issue was logged.
  - `status` (Enum: "OPEN" | "IN_PROGRESS" | "RESOLVED" | "FAILED_TO_RESOLVE").
  - `summary` (String): A description of the failure.
  - `history_ref` (Array of Strings): Timestamps referencing the related events in the `history` ledger.
