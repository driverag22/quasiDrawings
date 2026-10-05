# Nested C14 drawings (3-planar, klim = 3)

Computed 2026-10-05 on Euler cluster with `iterative_nested_c14_split.cpp` / `split_driver.h` / `iterative_nested_split.cpp`
(repo `quasi`). Family: nested 14-cycles connected by the braided matching of `iterative_nested_c14.cpp`; 
the first (and last) cycle is uncrossable (base drawing: the cycle plus an uncrossable star blocking one side).

## Files
| file | drawings | content |
|---|---|---|
| `intermediate_level1.jsonl.gz` | 44682 | Pass 1 on the base drawing: all reduced drawings after adding one crossable cycle (distinct up to gadget-aware isomorphism) |
| `final_2cycles.jsonl.gz` | 1 | Pass 2 on the base drawing: base cycle + one new uncrossed cycle (also as `final_2cycles_0.json` / `.graphml`) |
| `final_3cycles.jsonl.gz` | 15445 | Pass 2 on every intermediate drawing: base cycle, crossable middle cycle, uncrossed last cycle (distinct up to strong isomorphism) |

## Numbers
- Pass 1: 877520 prefixes at split depth 12, 100 Slurm array tasks (at most 38 at a time), wall clock 58 min (task times 12-28 min);
  532374 intermediate drawings written (deduplicated per task only), 44682 after merging.
- Pass 2 on the base drawing: 1 complete drawing(s), 1 up to isomorphism; all matching edges have exactly 3 crossings.
- Pass 2 on the intermediate drawings: 6311 of the 44682 can be finished (uncrossed cycle can be added); 15655 complete
  drawings in total, 15445 up to strong isomorphism.

## Format
One drawing per line (gzipped JSON lines) in the JSON recipe format of `hds_kplanar.h` (`kplane`, `num_vertices`,
`abstract_graph`, `drawing_recipe`). Optional per-step field `"pcr"`: prescribed crossings of that edge (crossing
capacities and uncrossable star edges of reduced drawings, uncrossable last cycle of final drawings).

Loading needs the current `hds_kplanar.h`; without it, these edges get 0 prescribed crossings.
- Intermediate drawings: the active cycle is labelled `0..13`; former crossings are ordinary vertices; irrelevant regions
  are replaced by uncrossable stars.
- Final drawings: the new cycle is labelled `nm..nm+13` (nm = number of vertices of the parent). Field `meta`:
  `cycle_crossings` (new cycle edges; 14x 3 = prescribed = uncrossed), `matching_crossings` (new matching edges),
  `parent` (in `final_3cycles`: `<path on Euler>#<line>`, the 0-based line of the parent in `intermediate_level1`).
- Read: `zcat file.jsonl.gz` gives one JSON per line; `Drawing<3> d(nlohmann::json::parse(line)); d.graphml_output(out);`
  writes a graphml.
