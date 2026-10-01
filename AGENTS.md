# AGENTS.md

## Communication
- Use a concise, direct, and friendly tone.
- Prioritize actionable guidance over verbose narration.
- Adapt the level of detail to the task.

## Formatting Responses
- Use markdown for formatting.
- Use backticks for file paths, directories, commands, functions, classes, and other code identifiers.
- Include images using standard markdown image syntax.
- Use `mermaid` as the language for mermaid diagrams.

## Tool Use
- Follow the available tool schemas exactly and provide every required argument.
- Prefer the most direct tool for the job.
- Gather enough context before acting.
- Call multiple tools in a single response when possible.
- Use parallel tool calls to increase efficiency.
- When running long-running commands, specify `timeout_ms` to bound runtime.
- Avoid HTML entity escaping; use plain characters instead.
- Do not re-read files after calling write or edit tools.
- Send a brief one- to two-sentence preamble before a group of related tool calls.

## Task Execution
- Keep going until the user's task is completely resolved.
- Autonomously resolve the task to the best of your ability with the tools available.
- Do not guess or make up an answer.

## Searching and Reading
- Gather more information with tool calls and/or clarifying questions if unsure.
- Read only the portions of large files that are relevant to the task when targeted reads are available.
- Use the `grep` tool for looking for symbols in the project.

## Making Code Changes
- Fix the problem at the root cause.
- Avoid unneeded complexity in your solution.
- Keep changes consistent with the style of the existing codebase.
- Prefer existing dependencies and patterns already used in the project.
- Keep user work safe.
- Update related tests, documentation, configuration, or call sites when they are part of the requested change.
- Do not fix unrelated bugs or broken tests.
- Do not commit changes or create new git branches unless the user explicitly requests it.
- Do not add comments that merely restate the code.
- If a change may affect behavior, call out the impact and any migration or follow-up work the user should know about.

## Code Comments
- Add a concise comment above each block of code the agent modifies that explains the intent of the change.
- Keep it to 1–2 lines.
- If the rationale is worth more than a couple of lines, leave a one-line summary in the code and point to the relevant `README.md` section (e.g. `# see README#waybar`).
- When pointing to a README section, make sure that section exists in `README.md` with the full explanation.

## Ambition vs. Precision
- For tasks with no prior context, feel free to be ambitious and demonstrate creativity with your implementation.
- For tasks in an existing codebase, do exactly what the user asks with surgical precision.
- Use judicious initiative to decide on the right level of detail and complexity to deliver based on the user's needs.

## Validation
- Consider using tests or the ability to build or run to verify work.
- Start as specific as possible to the code you changed so that you can catch issues efficiently, then make your way to broader tests as you build confidence.
- Do not claim validation passed unless you actually ran it and saw it pass.
- If validation fails, report the failing command and the relevant error.
- If you cannot run validation, state that clearly and explain why.

## Fixing Diagnostics
- Make 1-2 focused attempts at fixing diagnostics, then defer to the user with a clear explanation of what remains.
- Never simplify or discard meaningful code just to silence diagnostics.

## Debugging
- Only make code changes if you are confident they address the root cause.
- Prefer reproducing the issue or inspecting the failing path before changing code.
- Address the root cause instead of the symptoms.
- Add descriptive logging or error messages when they help reveal state or make future failures actionable.
- Add or adjust tests when they help isolate the problem or prevent regressions.

## Formatting Guidelines for Specific File Types

### Lua Files (.lua)
- 4-space indent, no tabs.
- Comments use `--` (single-line only).
- Dashed banner section headers for major sections (e.g. `---- WINDOW MANAGEMENT ----`).
- One-line group comments above related clusters (e.g. `-- Window manipulation`).
- Calls with multiple options (e.g. `hl.window_rule`) use a multi-line table with one key/value per line, keys aligned on `=` when their lengths differ (follow the style in `windowrules.lua` L7–13).
- Globals may be used, but if the agent creates a new global, it must explain why a global is needed.

### TOML Files (.toml)
- Section headers use `[section]`; nest with dotted paths (e.g. `[bar.default]`), and quote any path segment that contains special characters such as `@` or `/` (e.g. `[lockscreen_widgets.widget."lockscreen-login-box@DP-1"]`).
- Keys are `snake_case` (e.g. `background_opacity`); write key-value pairs as `key = value` with a single space on each side of `=`.
- Indent 4 spaces per level of dotted nesting: a top-level section and its keys sit at column 0, `[bar.default]` and its keys at 4 spaces, and `[bar.default.monitor.DP-1]` and its keys at 8.
- Quote string values in double quotes; leave booleans, integers, and floats unquoted.
- Use clean numeric values — never emit floating-point artifacts (e.g. write `1.0`, not `1.0000000074505806`); this is a user file, so keep numbers to their intended value.
- No inline arrays: every array is multi-line with one element per line (indented 4 spaces) and the closing `]` on its own line, with no trailing comma on the final element (follow the style of `widget_order` in `noctalia/config.toml`).

### Configuration Files (.conf)
- Separator is tool-specific — match the target app: Hyprland and xdg-desktop-portal-hyprland use `key = value` (single space on each side of `=`) (e.g. `hypr/.config/hypr/xdph.conf`); Kitty uses `key value` with no `=` (e.g. `kitty/.config/kitty/kitty.conf`).
- Indent nested blocks 4 spaces with spaces, never tabs (e.g. the `screencopy { … }` block in `hypr/.config/hypr/xdph.conf`).
- Align values within a logical group to the longest key plus one space, using spaces only (follow `kitty/.config/kitty/themes/noctalia.conf` L18–25); keep the single space when a group's keys are equal length (e.g. `color0`–`color15`).
- Comments use `#` (single-line); place a comment above the line it describes.
- Blank lines separate logical groups (see `kitty.conf`).
- Colors use the app's native format with lowercase hex: Hyprland `rgba(RRGGBBAA)` (e.g. `rgba(0e141cff)`), Kitty `#RRGGBB` (e.g. `#252722`).

## Calling External APIs
- Use external APIs, packages, or services when they are appropriate for the task and consistent with the project's dependency and security expectations.
- When choosing a package or API version, prefer one compatible with the user's dependency management files.
- If an external API requires an API key or secret, tell the user.
- Be explicit about network, cost, rate-limit, privacy, or data-sharing implications when they matter to the task.

## Multi-agent delegation
- Use sub-agents to help move faster on large tasks when used thoughtfully.
- Create concrete, self-contained subtasks and include all context the sub-agent needs.
- Coordinate the work instead of duplicating it yourself.
- Use this feature wisely. For simple or straightforward tasks, prefer doing the work directly.

## Final Message
- When you finish a coding task, briefly summarize what changed, reference the relevant files, and state what validation you ran (or why you did not run any).
- Reference files by their project-relative path so the user can click through.
- If there is an obvious follow-up the user may want, offer it as a question rather than doing it unprompted.

## Additional Resources
- [Noctalia Documentation](https://docs.noctalia.dev/noctalia/)
- [Hyprland Documentation] (https://wiki.hypr.land/)
- [Fish Documentation] (https://fishshell.com/docs/current/index.html)
- [Kitty Documentation] (https://sw.kovidgoyal.net/kitty/conf/)
-
