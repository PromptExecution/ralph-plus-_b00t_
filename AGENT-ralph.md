# Ralph Agent - Role-Based Skills & MCP Tools

Ralph is an autonomous agent loop runner integrated with b00t agent coordination system.

## Role Configuration

Ralph operates with the following role profile:

- **Role**: `executor`
- **Capabilities**: TaskMaster workflow execution, autonomous loop management
- **Skills**: `taskmaster`, `autonomous-loop`, `prd-parsing`, `workflow-execution`
- **Personality**: `persistent` (continues until completion or max iterations)

## Skills Mapping

### Core Skills

1. **taskmaster** - TaskMaster-AI integration
   - Parse PRD documents
   - Generate task lists
   - Track progress
   - Update task status

2. **autonomous-loop** - Self-directed execution
   - Iterate until completion
   - Handle blockers
   - Manage dependencies

3. **prd-parsing** - Requirements analysis
   - Convert PRDs to actionable tasks
   - Identify dependencies
   - Prioritize work

4. **workflow-execution** - Task implementation
   - Execute tasks in priority order
   - Run quality checks
   - Commit changes
   - Update progress

## MCP Tools Integration

Ralph exposes the following MCP tools when running in MCP server mode:

### Tools

- **ralph_run** - Start autonomous loop execution
  - Parameters: `--tool` (amp|claude|codex|opencode), `--max-iterations`, `--filter`

- **ralph_status** - Check current execution status
  - Returns: current task, iteration count, completion status

- **ralph_list_tasks** - List TaskMaster tasks
  - Parameters: `--filter` (pending|in-progress|done|blocked)

### Prompts

- **/ralph-prd** - Generate TaskMaster tasks from PRD
- **/ralph** - Convert existing tasks to Ralph-compatible format

### Resources

- **taskmaster://tasks** - Access to TaskMaster task data
- **ralph://progress** - Ralph execution progress log

## Executor Tools

Ralph supports multiple executor backends:

- **amp** - Amp CLI
- **claude** - Claude Code CLI
- **codex** - Codex CLI
- **opencode** - OpenCode CLI

Each executor tool must be configured separately in b00t datums.

## Agent Coordination

Ralph integrates with b00t agent coordination via:

1. **IPC Socket**: `/tmp/b00t/agents/ralph.sock`
2. **Protocol**: msgpack
3. **PubSub**: enabled for agent discovery

### Coordination Commands

```bash
# Start ralph agent
b00t-cli agent start _b00t_/ralph.agent.toml

# Discover ralph on the network
b00t-cli agent discover --role executor

# Delegate task to ralph
b00t-cli agent delegate ralph task-001 "Implement feature X" --priority high

# Check ralph progress
b00t-cli agent message ralph "status" "Get current progress"
```

## Stack Integration

Ralph is part of the `ralph-stack` which includes:

- ralph.cli - CLI tools
- ralph.mcp - MCP server
- ralph.agent - Agent configuration
- taskmaster.cli - TaskMaster CLI
- taskmaster.mcp - TaskMaster MCP server
- python (3.11+)
- uv package manager

## Usage Patterns

### Direct CLI

```bash
# Initialize TaskMaster in project
cd /path/to/project
taskmaster init

# Generate tasks from PRD
# (use /ralph-prd skill in your agent)

# Run ralph loop
uv run ralph run --tool claude --max-iterations 10
```

### Via b00t Agent System

```bash
# Start ralph agent
b00t-cli agent start _b00t_/ralph.agent.toml

# Ralph will:
# 1. Monitor taskmaster://tasks resource
# 2. Execute pending tasks in priority order
# 3. Report progress via ralph://progress
# 4. Coordinate with other agents via IPC
```

### Via MCP Tools

Configure your MCP client:

```json
{
  "mcpServers": {
    "ralph": {
      "command": "uv",
      "args": ["run", "ralph", "--mcp", "--transport", "stdio"],
      "env": {
        "RALPH_HOME": "_b00t_/ralph"
      }
    }
  }
}
```

Then use ralph tools from any MCP client:
- `ralph_run` to start execution
- `ralph_status` to check progress
- `ralph_list_tasks` to view tasks

## Learning Resources

Ralph learns from execution via:

1. **progress.txt** - Execution history and learnings
2. **CLAUDE.md files** - Directory-specific patterns
3. **Codebase Patterns** - Consolidated reusable knowledge

These resources help ralph improve over iterations and avoid repeating mistakes.

---

🥾 Generated via b00t gospel alignment
