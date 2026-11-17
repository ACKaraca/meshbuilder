# Customization

Workspaces, keyboard shortcuts, menus, radial menus, toolbars, preferences, accessibility and
localization, with sharing of all configuration as files.

## Domain objects

- **Workspace**: a saved arrangement of docked panels, viewports and the active tool context.
- **Key map**: a set of shortcut bindings per editor context.
- **Menu definition**: a user-editable menu, radial menu or toolbar layout referencing commands.
- **Preferences**: user-level settings stored outside documents.

## Workspaces and layout

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-CUSTOM-001 | Docking workspace | Arranges panels by docking, tabbing, floating and splitting, across multiple windows and monitors; layouts restore on restart. | T1 | 4 |
| MB-CUSTOM-002 | Task workspaces | Ships workspaces for modeling, UV editing, look development, rigging, animation, sculpting, effects and scene assembly; users switch with tabs and save their own. | T1 | 4 |
| MB-CUSTOM-003 | Workspace files | Exports and imports workspaces, key maps and menu definitions as documented text files for sharing within a team or package. | T1 | 4 |
| MB-CUSTOM-004 | Reset to default | Resets any workspace, key map or menu to its shipped state without affecting other customizations. | T1 | 4 |

## Shortcuts and menus

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-CUSTOM-005 | Shortcut editor | Binds keys, key chords, mouse buttons and tablet buttons to any command with parameters, per editor context, with search by command or key and conflict detection. | T1 | 4 |
| MB-CUSTOM-006 | Key map presets | Ships key map presets, including industry-common navigation and selection conventions under neutral names, selectable at first start. | T1 | 4 |
| MB-CUSTOM-007 | Radial menus | Opens context-sensitive radial menus on a held key or mouse gesture, with up to eight directions and sub-menus, editable per context and selection type. | T1 | 4 |
| MB-CUSTOM-008 | Context menus | Right-click menus adapt to the object or component under the cursor and are editable per context. | T1 | 4 |
| MB-CUSTOM-009 | Toolbars and shelves | Creates tabbed toolbars of command buttons (icon, label or both), with drag from the command palette or command log. | T1 | 4 |
| MB-CUSTOM-010 | Menu editing | Adds, removes and reorders main menu entries and creates custom menus referencing commands, scripts and macros. | T2 | 4 |
| MB-CUSTOM-011 | Quick favorites | Collects frequently used commands in a favorites popup opened by one key. | T2 | 4 |

## Preferences and appearance

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-CUSTOM-012 | Preferences | Organizes user settings (interface, navigation, viewport, input devices, files, autosave, memory and cache budgets, renderer connection) with search and per-setting reset. | T1 | 4 |
| MB-CUSTOM-013 | Themes | Provides light, dark and high-contrast themes with editable colors for UI and viewport elements, saved as theme files. | T1 | 4 |
| MB-CUSTOM-014 | Interface scaling | Scales the whole interface independently of the system setting, with crisp rendering at fractional scales. | T1 | 4 |
| MB-CUSTOM-015 | Tablet input | Configures pressure curves, tilt usage and button mappings for pen tablets, used by brushes in all areas. | T1 | 4 |
| MB-CUSTOM-016 | Startup configuration | Applies a studio configuration folder (preferences, key maps, workspaces, packages, color configuration) from an environment variable at startup, with user overrides layered on top. | T2 | 4 |

## Accessibility and localization

| ID | Capability | Behavior | Tier | Phase |
|---|---|---|---|---|
| MB-CUSTOM-017 | Keyboard completeness | Every command and every panel control is reachable and operable by keyboard; focus order is visible. | T1 | 4 |
| MB-CUSTOM-018 | Screen reader names | Every control exposes an accessible name, role and value through platform accessibility interfaces. | T1 | 4 |
| MB-CUSTOM-019 | Localization | All strings come from message catalogues; English and Turkish are complete, right-to-left layout is supported, and the language switches at runtime. | T1 | 4 |
