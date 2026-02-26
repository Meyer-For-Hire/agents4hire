# Well-Factored Code — Reference Links

## Primary Sources

- [C2 Wiki: Well Factored Code](https://wiki.c2.com/?WellFactoredCode) — Community discussion on factoring as discovery, self-documenting code, method length heuristics, and the relationship between factoring and testability. Key contributors: TomStambaugh, KentBeck, MartinFowler, DaveHarris.

- [C2 Wiki: Well Factored Code Leads To Better Optimizations](https://wiki.c2.com/?WellFactoredCodeLeadsToBetterOptimizations) — Discussion on how well-factored code isolates bottlenecks for targeted optimization. Includes KentBeck's "Make it work, make it right, make it fast" principle and real-world examples of InlineMethod yielding dramatic performance gains.

- [Mike Austin: "Well-Factored Code" (PDF)](https://www.mike-austin.com/new/content/Well-Factored%20Code.pdf) — Presentation connecting well-factored code to unit testability. Identifies mutable data, side effects, and dependencies (coupling vs. cohesion) as barriers to testing. Covers functional programming principles, dependency injection, and the insight that "writing code that's easy to test is actually just well written code."

- [Liz Acosta, The New Stack: "Why Quality Code Matters and How To Achieve It"](https://thenewstack.io/why-quality-code-matters-and-how-to-achieve-it/) — Article on technical debt costs, DRY, single responsibility principle, naming consistency, and implementing coding standards. Notes developers spend ~23% of their time on technical debt.

## Local Copies

PDF and webarchive copies of these sources are stored alongside this file in `docs/well-factored-code/`.
