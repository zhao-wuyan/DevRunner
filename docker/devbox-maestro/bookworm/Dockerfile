# Maestro-Flow development environment built on top of DevRunner devbox stack
ARG BASE_IMAGE=ghcr.io/zhao-wuyan/devrunner/devbox:bookworm
FROM ${BASE_IMAGE}

LABEL org.opencontainers.image.title="DevRunner Devbox Maestro" \
      org.opencontainers.image.source="https://github.com/zhao-wuyan/DevRunner" \
      org.opencontainers.image.description="Devbox with Maestro-Flow CLI and full component suite (devbox-maestro:bookworm)"

# Layer 1 (~1G): maestro-flow CLI + full component install
# Version not pinned — rebuild picks up the latest maestro-flow from npm.
# Rebuild this image only when you intend to upgrade maestro; Docker caches
# this layer across unrelated rebuilds (RUN string unchanged → cache hit).
# Profile schema v1: all 25 component groups + hooks + mcp + statusline enabled
RUN npm install -g maestro-flow \
    && maestro --version \
    && mkdir -p ~/.maestro/install-profiles \
    && printf '%s\n' \
      '{' \
      '  "$schema": "maestro-install-config/v1",' \
      '  "name": "full",' \
      '  "createdAt": "2026-06-22T00:00:00.000Z",' \
      '  "scope": "global",' \
      '  "components": {' \
      '    "enabled": true,' \
      '    "selectedIds": [' \
      '      "workflows","templates","chains","overlays",' \
      '      "commands","commands-odyssey","commands-learn",' \
      '      "agents","skills","skills-team",' \
      '      "claude-md","codex-agents-md","claude-md-chinese","codex-md-chinese",' \
      '      "codex-agents","codex-skills",' \
      '      "agy-context","agy-md-chinese","agy-skills","agy-agents",' \
      '      "agents-standard-skills","agents-standard-agents",' \
      '      "skills-extra-team","skills-scholar","skills-meta"' \
      '    ]' \
      '  },' \
      '  "claude": {' \
      '    "hooks": {"enabled": true, "basePreset": "standard", "selectedHooks": [], "isCustom": false},' \
      '    "mcp": {"enabled": true, "tools": ["write_file","edit_file","read_file","read_many_files","team_msg","store_knowhow"], "projectRoot": ""},' \
      '    "statusline": {"enabled": true, "theme": "notion"}' \
      '  },' \
      '  "codex": {' \
      '    "hooks": {"enabled": true, "basePreset": "standard", "selectedHooks": [], "isCustom": false},' \
      '    "mcp": {"enabled": true, "tools": ["write_file","edit_file","read_file","read_many_files","team_msg","store_knowhow"], "projectRoot": ""}' \
      '  },' \
      '  "agy": {' \
      '    "hooks": {"enabled": true, "basePreset": "standard", "selectedHooks": [], "isCustom": false}' \
      '  },' \
      '  "extraMcp": {"enabled": false, "targetIds": []},' \
      '  "backup": {"claudeMd": true, "all": false}' \
      '}' > ~/.maestro/install-profiles/full.json \
    && maestro install --import ~/.maestro/install-profiles/full.json \
    && rm -rf ~/.maestro/install-profiles/full.json

# Layer 2 (small, volatile): opencode CLI + maestro delegate reset
# Combine frequently-changing tools here so only this small layer re-downloads.
RUN npm install -g opencode-ai@latest \
    && maestro config delegate reset

WORKDIR /workspace
