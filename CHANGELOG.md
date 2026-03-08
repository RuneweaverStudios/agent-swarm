# Changelog

## v1.8.0 (S-tier upgrade)

- **Renamed** `FridayRouter` class to `AgentSwarmRouter` (backward-compat alias retained)
- **Removed** redundant COMPLEX tier (merged into QUALITY, which covers the same keywords and models)
- **Expanded** `_meta.json` with full fields, commands, tiers, license, homepage, 9 tags
- **Moved** changelog from README.md to standalone CHANGELOG.md
- **Added** section docstrings to `AgentSwarmRouter` class in router.py
- **Bumped** version to 1.8.0

## v1.7.8 (Workspace / prompt-injection rejection)

- **Prompt-injection mitigation**: Router now **rejects** (raises `ValueError`) task strings containing `<script>`, `javascript:`, or event-handler patterns (e.g. `onclick=`). Addresses code-insights warning; no longer delegates this protection solely to downstream LLMs. Skill synced to workspace at `workspace/skills/agent-swarm`.

## v1.7.7 (Security warnings / trust alignment)

**Credentials and trust:**
- **OPENCLAW_HOME**: Explicit everywhere that it is optional (not required). `_meta.json` lists it in `optionalEnv` only; platform config states unset defaults to `~/.openclaw`. SKILL.md and README state "not required" and reference metadata.
- **openclaw.json**: Platform config and docs now ask installers to verify they are comfortable granting read access; clarified that only `tools.exec.host` and `tools.exec.node` are used. SKILL.md "Before installing" and README requirements updated.

**Safe execution:**
- Commands/CLI sections in SKILL.md and README explicitly state that bash examples are for manual/CLI use only and that programmatic use must use subprocess with list arguments (no shell interpolation).

## v1.7.6 (VirusTotal / trust review fixes)

**Metadata and documentation alignment:**
- **OPENCLAW_HOME**: Declared optional in `_meta.json` (`optionalEnv`) to match SKILL.md and README; removed from required `env` list. Defaults to `~/.openclaw` when unset.
- **Safe execution**: SKILL.md and README now show subprocess-with-list-args as the primary way to invoke the router from code; CLI examples with quoted user message are labeled for manual/CLI use only to reduce command-injection risk from operator error.

## v1.7.5 (Credential declarations)

**Metadata and documentation improvements:**
- Declared required vs optional env and platform config in `_meta.json`; OPENCLAW_HOME later clarified as optional in v1.7.6
- Added `requires.platform.openclaw.config` declarations for OpenRouter API key and openclaw.json access
- Added `fileAccess` section in `_meta.json` documenting read/write access
- Enhanced requirements documentation in SKILL.md and README.md
- Explicitly documented that only `tools.exec.host` and `tools.exec.node` are accessed from openclaw.json
- Clarified that OpenRouter API key must be configured in OpenClaw platform (not provided by skill)

## v1.7.4 (Security clarification)

**Documentation improvements:**
- Clarified "saves tokens" phrase to explicitly mean API cost savings (not token storage)
- Replaced hard-coded absolute paths with relative paths in examples
- Added explicit documentation about file access scope (only `tools.exec.*` from `openclaw.json`)
- Clarified that router does not persist, upload, or transmit tokens or credentials

## v1.7.3 (Security hardening)

**Security improvements:**
- **Input validation**: Added comprehensive validation for task strings (length limits, null byte detection, suspicious pattern detection)
- **Config patch validation**: Whitelist-based validation for `recommended_config_patch` - only allows modifications to `tools.exec.host` and `tools.exec.node`
- **Label validation**: Added validation for label parameters
- **Documentation**: Added security section with safe execution practices and injection prevention guidance

## v1.7.0 (Security-focused release)

**Removed functionality (for improved security rating):**
- **Gateway guard integration** removed; use separate gateway-guard skill
- **Gateway auth secret exposure** removed from router output
- **FACEPALM troubleshooting integration** removed; use separate FACEPALM skill

**Security improvements:**
- Router now only handles model routing with no credential exposure
- No process management capabilities
- Clean separation of concerns: routing vs. gateway management vs. troubleshooting
