# Nests Due for Hatching

Daily-operations workflow for Curaçao's sea turtle monitoring program. It pulls suspected nest events from EarthRanger, computes each nest's status relative to its expected hatching date, and produces a dashboard grouped by beach, so field teams can see at a glance which nests are overdue for excavation, which are coming due within the week, and where they are.

## Dashboard widgets

| Widget | Description |
|--------|-------------|
| Overdue Nests | Count of nests already past their expected hatching date |
| Due Within 7 Days | Count of nests due today through 7 days out |
| Nests Map | Every nest plotted at its recorded coordinates, coloured by status (Overdue / Due within 7 days / Active / Unknown) |
| Nest Summary | One row per nest, with Location, Beach, Coordinates, Species, Activity ID, Relocation, Date laid, Expected hatching date, Status, and 3× triangulation item/distance pairs. Sortable, filterable, downloadable. |

Every widget is split into a tab per group (see **Grouping** below). Switching tabs filters all four to that group's data.

## Event types

| Event type | Used? | Notes |
|------------|-------|-------|
| (suspected) Nest V2 | ✅ | The only event type currently fetched. Carries the hatching schedule (expected hatching date, species, triangulation) that drives this whole workflow. |
| Attempt V2 | ❌ | Deliberately excluded. These record crawls where the turtle made body pits but did not lay eggs, so there is no expected hatching date and nothing to schedule against. |
| Relocation Data V2 | ❌ | Not yet included. It's missing `species`, `relocation`, and `expected_hatching_date` entirely. It appears to record only the physical act of relocating a nest (new coordinates, new triangulation), not a standalone nest record with its own due date. Adding it requires deciding whether relocated nests should appear on their own (status `Unknown`, no due date) or be joined back to the original nest record they belong to, so they inherit its real hatching date. |

## Grouping

The dashboard groups by **Beach** (default) or **Location**, selectable via the `groupers` field. `Location` maps to a single fixed value in the current EarthRanger instance (Curaçao is the only site), so in practice `Beach` is the meaningful grouping dimension. `Location` is kept available in case that changes.

"Today," for the purposes of computing each nest's status (Overdue / Due within 7 days / Active), is taken from the workflow's own configured time range (`until`) rather than the real wall-clock date. That way a run always classifies nests consistently with whatever period it was configured to look at, including re-runs against a past window.

## Status thresholds

| Status | Condition |
|--------|-----------|
| Overdue | Expected hatching date is in the past |
| Due within 7 days | Due today, or anytime in the next 7 days (inclusive) |
| Active | More than 7 days out |
| Unknown | No expected hatching date recorded |

## Requirements

[pixi](https://pixi.sh) is required for environment and dependency management. You will also need an EarthRanger connection configured for the `curacao` data source.
