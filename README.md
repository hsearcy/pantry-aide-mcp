# Pantry Aide — MCP Server 🐴

**Plan your week's meals and fill your grocery cart — without leaving your AI assistant.**

[Pantry Aide](https://pantryaide.com) is a hosted (remote) [Model Context Protocol](https://modelcontextprotocol.io)
server that runs the entire meal‑plan‑to‑checkout journey on behalf of a signed‑in user.

Most meal‑planning tools stop at *"here's a plan."* Pantry Aide closes the loop to **actual groceries**:

1. **Plan** — generate a weekly meal plan (mains + sides) from your own recipes, honoring a free‑text request like *"mostly chicken, nothing too hard."*
2. **Shop list** — build a **pantry‑aware** shopping list that automatically skips staples you likely already have, then refine it.
3. **Checkout** — push the list to your **live Kroger cart**, or generate an **Instacart** deep link.

All from chat: *"Plan 5 easy dinners this week, swap Tuesday for something lighter, then send the cart to Kroger."*

---

## Connecting

The server speaks **Streamable HTTP** at:

```
https://pantryaide.com/mcp
```

You need a free [Pantry Aide account](https://pantryaide.com). Kroger checkout requires connecting a
Kroger account once in the web app; Instacart works without extra setup.

### Option A — OAuth one‑click (Claude.ai, Claude Desktop, Claude for Work)

Add a custom connector / integration pointing at `https://pantryaide.com/mcp`. The server advertises
OAuth 2.1 discovery and Dynamic Client Registration, so the client walks you through sign‑in
automatically — no manual token needed.

### Option B — Bearer API key (Cursor, Windsurf, custom clients)

1. Sign in at [pantryaide.com](https://pantryaide.com) and generate an API key (looks like `pa_live_…`).
2. Add the server to your client config:

**Native remote transport (header auth):**
```json
{
  "mcpServers": {
    "pantry-aide": {
      "url": "https://pantryaide.com/mcp",
      "headers": { "Authorization": "Bearer pa_live_YOUR_KEY" }
    }
  }
}
```

**Via `mcp-remote` (for stdio‑only clients):**
```json
{
  "mcpServers": {
    "pantry-aide": {
      "command": "npx",
      "args": [
        "-y", "mcp-remote", "https://pantryaide.com/mcp",
        "--header", "Authorization: Bearer pa_live_YOUR_KEY"
      ]
    }
  }
}
```

---

## Tools (22)

**Recipes**
| Tool | What it does |
|---|---|
| `list_recipes` | List your recipes (owned + saved), cursor‑paginated |
| `get_recipe` | Full details (ingredients, instructions, timing) for one recipe |
| `discover_recipes` | Browse public recipes; filter by query/tag/difficulty/meal type |
| `save_recipe` | Save a public recipe to your collection (idempotent) |
| `create_recipe` | Author a new recipe |
| `update_recipe` | Edit fields on a recipe you own |
| `delete_recipe` | Delete a recipe you own (destructive) |

**Meal plans**
| Tool | What it does |
|---|---|
| `generate_meal_plan` | AI‑generate a weekly plan (mains + sides) from your recipes |
| `list_meal_plans` | List your meal plans |
| `get_meal_plan` | Full plan details (planned meals + recipes + ingredients) |
| `suggest_recipe_replacements` | Propose alternatives for a planned slot |
| `replace_planned_meal` | Swap one slot to a different recipe |
| `add_planned_meal` | Add a meal/side to a date |
| `remove_planned_meal` | Remove a meal from a plan |

**Shopping list**
| Tool | What it does |
|---|---|
| `create_shopping_list_from_plan` | Build a pantry‑aware list (to_buy vs. excluded) |
| `update_shopping_list_items` | Toggle selected / edit quantity, unit, notes |
| `add_custom_item` | Add a manual item (paper towels, snacks, …) |

**Kroger checkout**
| Tool | What it does |
|---|---|
| `prepare_kroger_cart` | Match items to Kroger products (auto / needs‑review / unmatched) |
| `search_kroger_alternatives` | Refresh candidates for one item |
| `select_kroger_products` | Persist UPC choices |
| `submit_kroger_cart` | Push items to your live Kroger cart (destructive) |

**Instacart**
| Tool | What it does |
|---|---|
| `create_instacart_link` | Generate an Instacart deep link for the selected items |

---

## Example prompts

- *"Plan 5 easy dinners for this week — mostly chicken, nothing too hard."*
- *"Build my shopping list and skip anything I probably already have."*
- *"Swap Tuesday's dinner for something lighter, then re‑do the list."*
- *"Add a vegetable side to Thursday."*
- *"Send the cart to Kroger when it looks good."* → review → *"yep, submit it."*
- *"Just make me an Instacart link for this week's groceries."*

---

## Links

- 🌐 Website: https://pantryaide.com
- 🔌 MCP endpoint: https://pantryaide.com/mcp
- 📖 MCP spec: https://modelcontextprotocol.io

Pantry Aide is a hosted commercial service. This repository documents its public MCP integration;
the application source is not included here.
