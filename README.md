# The lean-review skill reviews a specification or plan using a panel of reviewers

AI is reasonably good at implementing requirements, but not the boring but important stuff.  Things like will the code be maintainable?  Will its actions be auditable?  Is it secure?  Can an operator verify how it ran?  Is it configurable?  Does it follow good development practices?  This is the difference between production-ready code and prototype quality code.

This plugin augments the superpowers skills relating to specifications and plans, or may be used standalone.  These reviewers are designed for specification and planning, not code review.  A synthesizer takes the output from the specialists and deduplicates as well as deals with conflicts. For example if the Complexity Challenger and Data-Driven Advocate disagree the Advocate wins.  The following reviewers are configured

* Scope Minimizer - Cut unasked-for features
* Complexity Challenger - Simplify, exempt data-driven, auditability, and observability patterns
* Feasibility Auditor - Enforce single-plan boundary
* Data-Driven Advocate - Push behavior into config/data
* Maintainability Reviewer - Comprehensibility, modularity, debuggability
* Devil's Advocate - Runs once and proposes a superior alternative or affirms existing (credit: Alex Michael)
* Security Expert - CIA triad + best practices 
* SOLID Principles - Martin Fowler's SOLID principles (credit: Babu Paritala)
* Auditability Advocate - Ensure results, calculations, and decisions have durable, traceable evidence
* Observability Advocate - Ensure operations have sufficient logging to answer operational questions after the fact
* Consistency Checker - Ensure document is consistent and has no contradictions.

Why focus so much on the planning stage?  Because agent driven software development is basically the [Waterfall Model](https://en.wikipedia.org/wiki/Waterfall_model), but at [Ludicrous Speed](https://www.youtube.com/watch?v=NAWL8ejf2nM).  It is quicker and cheaper to fix issues at this stage than later after the code is written.
