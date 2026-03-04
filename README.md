# Cortex RBAC Runbooks

A collection of Snowflake Notebook runbooks that provide step-by-step examples for implementing **Role-Based Access Control (RBAC)** across the Snowflake Cortex AI platform.

Each runbook is a self-contained Snowflake Notebook (SQL + Python) that creates roles, grants privileges, builds sample objects, and demonstrates secure access patterns for a specific Cortex feature.

## Runbooks

| # | Notebook | Description |
|---|----------|-------------|
| 01 | [Cortex Functions](01_CortexFunctions/CORTEX_FUNCTIONS_RBAC.ipynb) | RBAC for Cortex LLM functions -- model allowlists, tiered role hierarchies, owner's-rights procedures, secure views for batch inference, fine-tuning workflows, and embedding privileges |
| 02 | [Cortex Search](02_CortexSearch/CORTEX_SEARCH_RBAC.ipynb) | RBAC for Cortex Search services -- service creation, hybrid search queries, operator controls (suspend/resume/refresh), and privilege separation |
| 03 | [Cortex Analyst](03_CortexAnalyst/CORTEX_ANALYST_RBAC.ipynb) | RBAC for Cortex Analyst -- semantic view creation, natural-language-to-SQL access, and row access policies that propagate through semantic views |
| 04 | [Cortex Agents](04_CortexAgents/CORTEX_AGENTS_RBAC.ipynb) | RBAC for Cortex Agents -- agent creation with multi-tool orchestration (Analyst + Search), operator and user privilege separation |
| 05 | [MCP Servers](05_MCPServers/MCP_RBAC.ipynb) | RBAC for Snowflake-managed MCP servers -- exposing Cortex tools (Analyst, Search, Agents, SQL, custom UDFs) to external AI agents via the MCP standard |
| 06 | [Cost Attribution](06_CortexCost/CORTEX_COST_ATTRIBUTION.ipynb) | Cost attribution queries across all Cortex services -- credits by feature, user, model, warehouse, and service, plus week-over-week comparisons |

## Prerequisites

- A Snowflake account with Cortex AI enabled
- `ACCOUNTADMIN` role (or equivalent privileges) to create roles and set account parameters
- A warehouse (default: `COMPUTE_WH`)

## Getting Started

1. Import the notebooks into Snowflake Notebooks (via Snowsight or the Snowflake CLI)
2. **Run the notebooks in the following order:**

```
01 Cortex Functions ──> 02 Cortex Search ──┬──> 04 Cortex Agents ──> 05 MCP Servers
                    ──> 03 Cortex Analyst ─┘

06 Cost Attribution (standalone, run anytime)
```

   - **01 first** -- sets account-level controls (model allowlists, cross-region inference) that affect 02 and 03
   - **02 and 03 next** (either order) -- create the Search service and Semantic View used by 04
   - **04 next** -- creates the Agent used by 05
   - **05 last** -- exposes objects from 02, 03, and 04 via MCP
   - **06 anytime** -- read-only cost queries, no dependencies

3. Each notebook uses session variables at the top for easy customization (database name, warehouse, role names, etc.)
4. Every notebook (except 06) includes commented-out cleanup SQL at the end to tear down created objects

## Architecture

All runbooks share a common database (`RUNBOOKS_DB`) with per-feature schemas:

```
RUNBOOKS_DB
├── CORTEX_FUNCTIONS   -- Notebook 01
├── CORTEX_SEARCH      -- Notebook 02
├── CORTEX_ANALYST     -- Notebook 03
├── CORTEX_AGENTS      -- Notebook 04
└── MCP                -- Notebook 05
```

Notebook 06 (Cost Attribution) is read-only and queries `SNOWFLAKE.ACCOUNT_USAGE` views -- it does not create any objects.

## Snowflake Features Covered

- **Cortex AI**: `COMPLETE`, `SENTIMENT`, `FINETUNE`, `SEARCH_PREVIEW`, `AI_EMBED`, Agents, MCP Servers
- **Access Control**: Custom roles, role hierarchies, database roles (`CORTEX_USER`, `CORTEX_ANALYST_USER`, `CORTEX_AGENT_USER`), model application roles
- **Security**: Row access policies, secure views, stored procedures with `EXECUTE AS OWNER`
- **Account Parameters**: `CORTEX_ENABLED_CROSS_REGION`, `CORTEX_MODELS_ALLOWLIST`

## License

Apache License 2.0 -- see [LICENSE](LICENSE) for details.
