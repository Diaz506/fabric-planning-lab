# Contributing

Corrections and improvements are welcome, particularly from anyone who has run these
modules against a live capacity.

## What is most useful

1. **Step drift.** Fabric Planning changes quickly. If a ribbon path, dialog field, or
   menu name no longer matches what you see, that is the highest-value fix. Include the
   date you checked and your capacity SKU.
2. **Screenshots.** `assets/screenshots/` is empty by design, because a screenshot that
   is six months stale is worse than none. If you capture a set while running the lab,
   note the date they were taken.
3. **Sizing data.** Module 08 uses published rates and a modeled population. Real
   consumption figures from a real deployment would improve it.
4. **Failure modes.** If something broke in a way the lab did not warn you about, say so.
   The prerequisite traps in Module 00 came from exactly that.

## Ground rules

- **Everything factual cites Microsoft Learn.** Each module ends with a Sources section.
  If you add a claim, add the link that supports it. Where the lab deviates from
  Microsoft's own tutorials, it says so and explains why, as in Module 06.
- **Keep the narrative.** Harborlight Provisions is not decoration. It gives every number
  a reason to exist, and the FY25 and FY26 figures deliberately match the sample data.
- **Plain prose.** No em dashes as a stylistic tic, no unfalsifiable adjectives, no
  marketing register. Write the way you would explain it to a colleague who has to make
  the number.
- **Do not commit the sample files.** `.pbix` and `.xlsx` are gitignored. They belong to
  `microsoft/fabric-samples` and should be linked, not copied.

## Checks before you open a pull request

```bash
# every relative link resolves
# no em dash budget violations
# module numbering and next/back navigation stay consistent
```

If you renumber or add a module, update the table in `README.md`, the navigation footers
in the neighbouring modules, and both diagrams in `assets/diagrams/lab-flow.md`.
