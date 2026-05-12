# The lean-review adds a skill to review a specification or plan using a panel of reviewers

This plugin arguments the superpowers skills relating to specifications and plans, or may be used standalone.  These reviewers are designed for specification and planning, not code review.  A synthesizer takes the output from the specialists and deduplicates as well as deals with conflicts. For example if the Complexity Challenger and Data-Driven Advocate disagree the Advocate wins.  The following reviewers are configured

* Scope Minimizer - Cut unasked-for features
* Complexity Challenger - Simplify, exempt data-driven patterns
* Feasibility Auditor - Enforce single-plan boundary
* Data-Driven Advocate - Push behavior into config/data
* Maintainability Reviewer - Comprehensibility, modularity, debuggability
* Devil's Advocate - Propose superior alternative or affirm (credit: Alex Michael)
* Security Expert - CIA triad + best practices 
* SOLID Principles - Martin Fowler's SOLID principles (credit: Babu Paritala)
* Consistency Checker - Ensure document is consistent and has no contradictions.

Why focus so much on the planning stage?  Because agent driven software development is basically the [Waterfall Model](https://en.wikipedia.org/wiki/Waterfall_model), but at [Ludicrous Speed](https://www.youtube.com/watch?v=NAWL8ejf2nM).  It is quicker and cheaper to fix issues at this stage than later after the code is written.
