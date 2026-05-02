# A skill to review a software specification or plan using a panel of reviewers

Usage: 

* `/lean-review myspec.md`
* `/lean-review myspec.md spec`
* `/lean-review myplan.md plan`

If the second argument is not provided, and spec vs plan can't be inferred from the file path, the user is prompted for the information.

These reviewers are designed for specification and planning, not code review.  A synthesizer takes the output from the specialists and deduplicates as well as deals with conflicts. For example if the Complexity Challenger and Data-Driven Advocate disagree the Advocate wins.  The following reviewers are configured

* Scope Minimizer - Cut unasked-for features
* Complexity Challenger - Simplify, exempt data-driven patterns
* Feasibility Auditor - Enforce single-plan boundary
* Data-Driven Advocate - Push behavior into config/data
* Maintainability Reviewer - Comprehensibility, modularity, debuggability
* Devil's Advocate - Propose superior alternative or affirm (credit: Alex Michael)
* Security Expert - CIA triad + best practices 
* SOLID Principles - Martin Fowler's SOLID principles (credit: Babu Paritala)

The Devil's advocate is only run the first time and each time the Devil's Advocate's recommendation is accepted.  After that,
it is skipped because it is expensive and it has already reviwed the plan fundamentals.

Why focus so much on the planning stage?  Because agent driven software development is basically the [Waterfall Model](https://en.wikipedia.org/wiki/Waterfall_model), but at [Ludicrous Speed](https://www.youtube.com/watch?v=NAWL8ejf2nM).  It is quicker and cheaper to fix issues at this stage than later after the code is written.
