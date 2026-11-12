# Compact Regions Experiment (Folia 26.3.x)

This branch is an aggressive region-recalculation experiment, not a strict geometric region-size cap.

Changes in `folia-server/minecraft-patches/features/0012-Experimental-compact-region-recalculation.patch`:

- `minSectionRecalcCount`: reduced from the grid-compensated 2048-chunk-equivalent threshold to 2 region sections.
- `maxDeadRegionPercent`: reduced from 1/6 to 5%, allowing stale sections to be reclaimed sooner.
- `emptySectionCreateRadius`: reduced to 1 section (with `grid-exponent: 0`, one chunk per section).

Recommended test configuration:

```yaml
threaded-regions:
  grid-exponent: 0
```

Important limitations:

- This does not split a continuously loaded, connected set of chunk holders into fixed-size regions. The regionizer still merges nearby loaded sections to preserve ownership and threading invariants.
- A player's view/simulation-distance tickets can keep a large connected chunk area alive, so they also bound how small a player-centered region can become.
- Reducing the empty-section halo from 8 chunks to 1 chunk weakens Folia's usual safety margin for synchronous nearby chunk access. Plugins or server code that rely on the larger ownership radius may hit out-of-region checks or expose unsafe accesses.
- This is for isolated testing and profiling. Do not replace a production jar without validating gameplay, chunk loading, plugins, and stress behavior.

The desired hard maximum of 50–100 blocks cannot be honestly guaranteed by these parameter changes alone; that requires changing how contiguous loaded chunks are partitioned and how cross-region interactions are synchronized.
