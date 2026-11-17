# Scripting and extensibility

Automating and extending MeshBuilder: the command registry, macros, the automation protocol, the
Python client, the headless CLI, sandboxed plug-ins, packages and the optional assistant panel.

## Domain objects

- **Command**: a registered operation with a stable identifier, parameter schema, enabled predicate
  and non-interactive run.
- **Macro**: a recorded sequence of commands with parameters.
- **Plug-in**: a sandboxed WebAssembly module with a manifest declaring what it contributes.
- **Package**: an installable bundle of plug-ins, scripts, graph assets, presets and workspaces.

## Commands and macros

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SCRIPT-001 | Command registry | Every user action, including tool operations and editor actions, is a registered command with a documented parameter schema; the registry is browsable in the application and exported as machine-readable reference. | T1 | 4 |
| MB-SCRIPT-002 | Command line and palette | Runs any command by name with parameters from a command line, and finds commands by fuzzy search with shortcuts shown. | T1 | 4 |
| MB-SCRIPT-003 | Command echo | Shows executed commands with their parameters in a log panel, copyable as a Python call or a CLI invocation. | T1 | 4 |
| MB-SCRIPT-004 | Macro recording | Records commands into a macro, edits parameters, and replays it on a new selection; macros are saved as documents or package content. | T2 | 4 |

## Automation

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SCRIPT-005 | Automation protocol | Serves the Arc Automation Protocol over stdio or a local socket: run commands, query and subscribe to document objects, evaluated geometry and selection, and receive change events. Programmatic calls never open dialogs. | T1 | 4 |
| MB-SCRIPT-006 | Python client | The official Python client wraps the protocol with typed functions per command and object type, runs out of process, and works against the desktop application or a headless session. | T1 | 4 |
| MB-SCRIPT-007 | Script editor | Edits and runs Python scripts in a panel connected to the running application, with output, errors linked to lines and run-selection. | T2 | 4 |
| MB-SCRIPT-008 | Script tools and panels | Scripts register tools, menu entries and simple panels (declared as data: fields, buttons, lists) that appear in the native UI. | T2 | 4 |
| MB-SCRIPT-009 | Event hooks | Scripts subscribe to document events (open, save, selection change, frame change, transaction committed) through the protocol. | T2 | 4 |
| MB-SCRIPT-010 | No embedded code in documents | Documents never contain code that runs on open; scripts attached to documents run only after an explicit user action and display their source and origin first. | T1 | 4 |
| MB-SCRIPT-011 | Model Context Protocol endpoint | Exposes the command registry and document queries through a Model Context Protocol endpoint derived from the same registry. | T2 | 4 |

## Headless operation

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SCRIPT-012 | Headless CLI | `meshbuilder-cli` opens documents, runs commands and scripts, evaluates graphs, converts formats, validates meshes, captures viewports with a software or GPU device, and serves the automation protocol, with exit codes and JSON output. | T1 | 4 |
| MB-SCRIPT-013 | Hub jobs | The headless CLI runs as an Arc Hub automation job worker for conversion, validation and publishing tasks. | T2 | 5 |

## Plug-ins and packages

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SCRIPT-014 | WebAssembly plug-ins | Loads plug-ins that contribute commands, modifiers, graph nodes, importers, exporters and manipulators; plug-ins run sandboxed with no ambient file or network access, under memory and time limits, and their document changes apply transactionally. | T2 | 5 |
| MB-SCRIPT-015 | Capability grants | A plug-in manifest declares requested capabilities (read files in a chosen folder, network host, device input); the user grants or denies each, and grants are revocable. | T2 | 5 |
| MB-SCRIPT-016 | Package manager | Installs, updates, disables and removes packages from local files or configured repositories, verifying signatures and showing contents and requested capabilities before install. | T2 | 5 |
| MB-SCRIPT-017 | Plug-in API versioning | Plug-ins declare the API version they target; the application runs plug-ins for any supported older API version and refuses newer ones with a message. | T2 | 5 |

## Assistance

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-SCRIPT-018 | Assistant panel | An optional assistant panel proposes command sequences from natural-language requests using a user-configured provider (including local models), shows a preview of the resulting changes, and applies them only after explicit confirmation. No document content leaves the machine without explicit action. | T3 | 5 |
