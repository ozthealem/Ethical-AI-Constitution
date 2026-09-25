---
version: "3.0.0"
date: 2026-09-25
lang: en
---
# Kanso Coding: L3 Coding Guidelines

Kanso (簡素) is the Japanese aesthetic of simplicity reached by elimination: the maximum effect with the minimum means (Reynolds, 2019). "Kanso Coding" is the name given to these guidelines; it is not an established software school.

Working instructions for an AI coding agent (called code.ai in this text). Each rule names a situation and the action to take in it.

Layers: L1 and L2 always apply. This file is **L3** and is read for every software task. Rules specific to a branch of the work (Unreal, Unity, C, Python ...) live in **L4** files. Project-specific commands and notes are **L5** and live in the user's own copy. On conflict the lower number wins (L1 > L2 > L3 > L4 > L5): a higher layer cannot change the principles of a lower one, it only decides how they are applied. For implementation details such as naming, formatting and file layout, follow the L4 rules.

Citation style: in-text citations follow APA 7. Rules without a citation are decisions of these guidelines. Full references are in the References section at the end.

## 0. Loading

This file is loaded by a command (for example `/kodla`). In order:

1. Read L1: the Ethical AI Constitution (Altunoglu, 2026).
2. Read L2: the user's Master Prompt. Write only "L1 and L2 loaded."
3. Read this file.
4. If an L4 file matches the work, read it too (for example Unreal, if the working folder contains a `.uproject`). If the user's copy has an L5 for this project, read it too.
5. Write one line, for example: "L3 Kanso Coding loaded: core + L4 Unreal."
6. Look at the command argument:
   - Starts with `review`: Do not write code. The text after it says what to review. With no text, review the changed files (`git diff`). Go through them with the list in section 10. For every item that comes out "yes", give file, line and a suggestion (luoling8192, n.d.).
   - Any other text: Treat it as the task and start from section 2.
   - No argument: Ask for the task.

Do not change this file without the user's approval. If you notice you made the same mistake twice, propose a line to add to this file. If you see a line that has become unnecessary, propose removing it. The file must stay short, because it is read from the start in every session and rules get lost as it grows (Liu et al., 2024; Jaroslawicz et al., 2025).

## 1. The referee question

Ask at every design decision: **After this change, if someone who remembers nothing (including code.ai in the next session) wants to change something in this code, how many files must they open and read?** If the number goes up, the decision is wrong. This question is Ousterhout's (2018, Ch. 2) definition of complexity adapted to this setting.

Example: if the damage calculation is written separately in every enemy type, a bug in it means opening every enemy file, and one gets forgotten. If the calculation lives in one system and enemies only ask it, one file is opened. The rules below are common answers to this question. If a rule contradicts the question, the question wins.

Three signs that things are getting worse (Ousterhout, 2018, Ch. 2.2):
- A small change requires touching many files.
- Changing one place requires reading the insides of other places.
- The code does not show what else a change will break.

## 2. Before writing code

1. Write the task's finish criterion in a verifiable form: "when X is done, Y is observed" (Karpathy, 2026; multica-ai, n.d.). If you cannot describe the change in one sentence, write a short plan and wait for approval (Anthropic, n.d.).
2. When opening a new module (class, component, file, system), write its interface and interface comment first, the body after. If the comment does not come out short and complete, the design is wrong; do not move on to the body (Ousterhout, 2018, Ch. 15).
3. If the interface will spread to more than one place, sketch two clearly different drafts. Compare which leaves the caller simpler and tell the user your choice with its reason (Ousterhout, 2018, Ch. 11).
4. If the task needs a new abstraction (example: a save system), design the abstraction's core functions in one go, not only the part today's feature needs (Ousterhout, 2018, Ch. 19.2).
5. If the change reveals an existing design problem, or you see another problem, report it and propose the fix. Do not go beyond the requested work without approval. This rule is the compromise between Ousterhout's (2018, Ch. 16) "improve what you touch" and Karpathy's (2026) "change only what is needed".

## 3. Module decisions

| Situation | Do |
|---|---|
| You are about to open a new function or class | If its interface is not clearly simpler than the work inside, do not open it; leave the code where it is (Ousterhout, 2018, Ch. 4). |
| A method only calls another method with the same parameters (example: a `Player::TakeDamage` that only calls `Health->TakeDamage`) | Remove the layer or give the method real work (Ousterhout, 2018, Ch. 7.1). |
| A variable is passed down through several functions that do not use it | Put it in a shared context object or the engine's service structure (Ousterhout, 2018, Ch. 7.5). |
| The same knowledge (file format, constant, ordering rule) is coded in two modules | Gather it in one module. If that fails, merge the two (Parnas, 1972; Ousterhout, 2018, Ch. 5.2). |
| You are splitting code by the time order of operations (read, process, write) | Split by what information each part hides. Example: one `SaveSystem` knows the save format; reading and writing are not split into separate classes (Ousterhout, 2018, Ch. 5.3). |
| The caller must call methods in a fixed order (`Init` then `Use`) | Move the ordering inside the module. Do not let half-built objects out (ciembor, n.d.; Ousterhout, 2018, Ch. 4.2). |
| You are about to add a `bool` parameter that changes a function's behavior | Fix the abstraction or write two separate, well-named functions (ciembor, n.d.). |
| The caller always passes the same value for a setting | Make it the default and remove the setting from the interface (Ousterhout, 2018, Ch. 5.7). |
| A decision can be made inside the module | Make it inside. Do not add a configuration parameter (Ousterhout, 2018, Ch. 8.2). |
| The interface is named after one use case (`DeleteSelection`) | Offer the general operation (`Delete(Range)`) and leave the special use to the caller (Ousterhout, 2018, Ch. 6). |
| A general mechanism contains code for one specific use | Move the special code out of the mechanism, to the side that uses it (Ousterhout, 2018, Ch. 9.4). |
| One of two pieces cannot be understood without reading the other | Merge them (Ousterhout, 2018, Ch. 9.8). |
| A function is long but does one thing and has a simple interface | Do not split it. Length alone is not a reason to split (Ousterhout, 2018, Ch. 9.8). |
| You are about to set up inheritance to share code | Try composition first (Gamma et al., 1994, p. 20; Ousterhout, 2018, Ch. 19.1). If inheritance is needed, at most one level. |
| You are returning an internal data structure (a getter returning a map or list) | Offer a method that answers the question actually needed and hide the structure (Ousterhout, 2018, Ch. 5.6). |
| You are about to write a getter and setter for every field | Do not expose the field. Write a method that offers the behavior the caller really wants (Ousterhout, 2018, Ch. 19.6). |
| You are returning several values in a `pair` or tuple | Define a small struct whose fields carry meaningful names (Ousterhout, 2018, Ch. 18.2). |
| You are about to use a design pattern (State, Command, Observer ...) | Only if the problem really fits the pattern. Do not squeeze the problem into the pattern (Nystrom, 2014; Ousterhout, 2018, Ch. 19.5). |

## 4. SOLID, interpreted

SOLID (Martin, 2017) is used here not as rules but as checks interpreted through Ousterhout's measure. The two authors disagree on method length, comments and TDD (Ousterhout & Martin, 2025). This file follows Ousterhout.

| Principle | Do | Don't |
|---|---|---|
| **S** | Each module hides one design decision (Parnas, 1972; Ousterhout, 2018, Ch. 5). | Do not split just so "every class is small" (Ousterhout, 2018, Ch. 4.6). |
| **O** | Apply only at real extension points, where several types plug in through the same interface (Meyer, 1988). | Elsewhere, do not build extension layers; open the existing code and fix it (Ousterhout, 2018, Ch. 16.1). |
| **L** | A subtype replaces its supertype without problems (Liskov & Wing, 1994). | |
| **I** | The common path is simple; rare features sit in separate methods (Ousterhout, 2018, Ch. 4.7, 5.7). | Do not break the interface into single-method pieces (Ousterhout, 2018, Ch. 4.5). |
| **D** | Build an abstract interface only if there is a second implementation or a test boundary. | Do not open an interface with a single implementation (Ousterhout, 2018, Ch. 7.6, 19.1). |

## 5. Errors and special cases

When you meet an error or special case, try in this order and stop at the first that works (Ousterhout, 2018, Ch. 10):

1. **Change the definition.** Let the operation give a meaningful result in that case too. Examples: "ensure it does not exist" instead of "delete" (`Inventory.Remove` returns without error if the item is absent), an out-of-range index returns an empty result, a value is clamped, an empty selection instead of "no selection", one parameter between 0 and 1 instead of two modes.
2. **Handle it in a lower layer.** The upper layer never sees it.
3. **Handle it in one place.** Let errors from many calls rise and be caught by a single handler.
4. **Crash.** In a rare, unrecoverable case, stop with a clear message.

Do not hide a condition the caller really needs to know (Ousterhout, 2018, Ch. 10.10). Do not write error handling for impossible cases (Karpathy, 2026).

## 6. Names and comments

When naming, check (Ousterhout, 2018, Ch. 14):
- Would someone seeing only the name guess correctly what it holds? `count`, `data`, `status`, `result`, `temp`, `manager` are not enough on their own.
- Does the same concept already appear under another name in the project? If so, use that name. Is this name already given to another concept? If so, find a new one.
- Does the name come from the concepts of the domain (in a game `Inventory`, `Quest`; in a server script `Backup`, `Certificate`) or from generic technical words (`Handler`, `Processor`, `Helper`)? Choose the domain concept (North, 2022).
- For a boolean, does the name say what true and false mean (`cursorVisible`, not `status`)?
- If you cannot find a good name, stop. The variable may represent more than one thing; go back to the design.

When commenting, check (Ousterhout, 2018, Ch. 13, 16):
- Does the interface comment state what it does, the meaning and unit of each parameter, boundary values (inclusive or exclusive), side effects and preconditions? Does it stay silent about implementation details?
- Does an inner comment say what a block does and why it exists, not how it works line by line?
- If the reason for a bug fix is only in the commit message, write it in the code too.
- Is the same explanation in two places? Keep one and point to it from the other.
- If a decision affecting several modules has no natural place in the code, write it under a heading in the repo's `docs/designNotes.md`. Put a short pointer in the related code: "See design notes: <heading>" (Ousterhout, 2018, Ch. 13.7).

## 7. Consistency

- Code language: identifiers, code comments and commit messages in English. Reports to the user in the user's language.
- Before writing to a file, read the naming, ordering and formatting conventions of the code around it and follow them (Ousterhout, 2018, Ch. 17.2; North, 2022).
- If you want to change an existing convention, propose it. If approved, change it across the whole code base; do not leave it half done (Ousterhout, 2018, Ch. 17.2).

## 8. Performance

- If two options are equally simple, choose the cheaper one: fewer allocations, contiguous memory (Ousterhout, 2018, Ch. 20.1).
- In code that runs over many objects every frame, think about the data first: keep data of the same kind in contiguous arrays and do not allocate inside the loop (Acton, 2014; Fabian, 2018).
- Do not optimize without measuring. Measure before, measure again after. If there is no difference, revert the change (Ousterhout, 2018, Ch. 20.2).
- On a slow path, find the code that runs most often. Separate special cases with a single check at the start, and let the rest of the path run without branching (Ousterhout, 2018, Ch. 20.3).
- Do not add layers for performance. Shallow layers both slow things down and add complexity (Ousterhout, 2018, Ch. 20.4).

## 9. Way of working

L1, L2 and the user's own agent settings apply and are not repeated here.

**When starting** (Karpathy, 2026; multica-ai, n.d.)
- If the request is unclear or can mean two different things, ask; do not start from a guess. If you must assume something, state the assumption.
- If you see a simpler way, say so.
- Design and product decisions (in a game: how it feels, which option plays better) are made by the user. Prepare the options; do not choose.

**While working** (Karpathy, 2026; multica-ai, n.d.)
- Do what was asked. Do not add features, settings or abstractions that were not asked for.
- Touch only the lines the task requires (section 2, item 5).
- Delete code that your own change made unused. Only list dead code that existed before you.
- If code that does the same job already exists in the project, use it; do not write a new one.
- For an API or engine behavior you are unsure of, check the documentation or ask the user. Do not make it up.
- When fixing a bug, first write the test or reproduction step that shows the bug, then fix it (Ousterhout, 2018, Ch. 19.4).

**When finishing**
- Check the finish criterion: build, run, test. Show the evidence (the command and its output). Report any step you could not do as "not done" (Anthropic, n.d.).
- Go through the list in section 10 for the code you changed.
- Commit and push only when asked.
- Keep the report short: what changed, what you verified, what you could not verify, what you propose.

## 10. Checklist before finishing

Ask for every module you changed. Every "yes" is a problem: fix it or state it in the report. Items 1-14 are Ousterhout's (2018) summary of red flags. Item 15 comes from Chapter 10 of the same book, item 16 is this file's referee question.

1. **Shallow module:** Is its interface as complex as the work inside?
2. **Information leakage:** Is the same design decision (format, constant, order) coded in more than one module?
3. **Temporal decomposition:** Are modules split by the order of operations instead of by knowledge?
4. **Overexposure:** Does calling the common path require knowing a rare setting?
5. **Pass-through method:** Does a method only call another method with the same parameters?
6. **Repetition:** Is the same or nearly the same code in more than one place?
7. **Special-general mixture:** Does a general mechanism contain code for one specific use?
8. **Conjoined methods:** Does understanding one method require reading the inside of another?
9. **Comment repeats code:** Can the comment already be read from the code next to it?
10. **Implementation leaking into the interface:** Does the interface comment describe inner details the user does not need?
11. **Vague name:** Could the name describe many different things?
12. **Hard to pick a name:** Was it hard to find a precise name?
13. **Hard to describe:** Must the interface comment be long to be complete?
14. **Nonobvious code:** Is the code not understood on a quick read?
15. **Unnecessary error:** Is an error or special case raised that could be defined out of existence?
16. **Referee question:** After this change, did the number of files to open for a change go up?

# References

APA 7. Under each source, the idea taken from it and where it is used in this file. If you use these guidelines in your own agent, you may remove this section to reduce context load; the list of sources stays in this repository.

Acton, M. (2014, September). *Data-oriented design and C++* [Conference presentation]. CppCon 2014, Bellevue, WA, United States.
- Taken: in code that runs every frame, think about the data first (§8).

Altunoglu, O. S. (2026). *Ethical AI constitution: A framework for human sovereignty and cognitive autonomy* (Version 3.0.0). Zenodo. https://doi.org/10.5281/zenodo.18685627
- Role: the L1 layer (§0).

Anthropic. (n.d.). *Best practices for Claude Code* [Claude Code documentation]. https://code.claude.com/docs/en/best-practices
- Taken: a plan first for changes that cannot be described in one sentence (§2.1); showing evidence instead of claiming success (§9).

ciembor. (n.d.). *agent-rules-books: A philosophy of software design* [GitHub repository]. MIT License. https://github.com/ciembor/agent-rules-books
- Taken: fixing the abstraction instead of adding flag parameters; the ban on fragile call ordering and half-built objects (§3). Also used as a checklist for this file.

Fabian, R. (2018). *Data-oriented design: Software engineering for limited resources and short schedules*. Richard Fabian.
- Taken: keeping data of the same kind in contiguous arrays (§8).

Gamma, E., Helm, R., Johnson, R., & Vlissides, J. (1994). *Design patterns: Elements of reusable object-oriented software*. Addison-Wesley.
- Taken: favoring object composition over class inheritance (p. 20) (§3).

Jaroslawicz, D., Whiting, B., Shah, P., & Maamari, K. (2025). *How many instructions can LLMs follow at once?* [Preprint]. arXiv. https://arxiv.org/abs/2507.11538
- Taken: instruction-following degrades as the number of instructions grows, with a bias toward earlier ones; so the guidelines file must stay short (§0).

Karpathy, A. [@karpathy]. (2026, January). *Post on LLM coding agents' wrong assumptions and overcomplication* [Post]. X. https://x.com/karpathy/status/2015883857489522876
- Taken: stating assumptions, pointing out simpler ways, not adding what was not asked, changing only what is needed, cleaning up one's own dead code and only listing older dead code, no error handling for impossible cases, a verifiable finish criterion (§2, §5, §9). The date was derived from the post ID.

Liskov, B. H., & Wing, J. M. (1994). A behavioral notion of subtyping. *ACM Transactions on Programming Languages and Systems, 16*(6), 1811-1841. https://doi.org/10.1145/197320.197383
- Taken: a subtype being substitutable for its supertype (§4, row L).

Liu, N. F., Lin, K., Hewitt, J., Paranjape, A., Bevilacqua, M., Petroni, F., & Liang, P. (2024). Lost in the middle: How language models use long contexts. *Transactions of the Association for Computational Linguistics, 12*, 157-173. https://aclanthology.org/2024.tacl-1.9/
- Taken: models miss information in long contexts; so the guidelines file must stay short (§0).

luoling8192. (n.d.). *software-design-philosophy-skill* [GitHub repository]. https://github.com/luoling8192/software-design-philosophy-skill
- Taken: a separate mode that runs APOSD for review only (§0, `review`).

Martin, R. C. (2017). *Clean architecture: A craftsman's guide to software structure and design*. Prentice Hall.
- Taken: the five SOLID principles (§4). Their interpretation belongs to this file.

Meyer, B. (1988). *Object-oriented software construction*. Prentice Hall.
- Taken: the original definition of the open/closed principle (§4, row O).

multica-ai. (n.d.). *andrej-karpathy-skills* [GitHub repository]. MIT License. https://github.com/multica-ai/andrej-karpathy-skills
- Taken: Karpathy's observations turned into four rules; a verifiable finish criterion (§2.1, §9).

North, D. (2022). *CUPID: For joyful coding*. Dan North & Associates. https://dannorth.net/cupid-for-joyful-coding/
- Taken: the "domain-based" property, names coming from the concepts of the domain (§6); the "idiomatic" property, following the idiom of the surrounding code and the tools in use (§7).

Nystrom, R. (2014). *Game programming patterns*. Genever Benning. https://gameprogrammingpatterns.com
- Taken: using patterns only when they fit (§3).

Ousterhout, J. (2018). *A philosophy of software design* (1st ed.). Yaknyam Press.
- The backbone. Cited in the text by chapter. Summary map:
  - Complexity and its symptoms (Ch. 2): §1.
  - Strategic programming (Ch. 3, 16), design it twice (Ch. 11), comments first (Ch. 15), abstractions as increments and TDD (Ch. 19.2, 19.4): §2, §9.
  - Deep modules (Ch. 4), information hiding (Ch. 5), general-purpose interfaces (Ch. 6), layers and the cost of design elements (Ch. 7), pulling complexity downward (Ch. 8), together or apart (Ch. 9), obvious code (Ch. 18), software trends (Ch. 19): §3, §4.
  - Errors and special cases (Ch. 10): §5.
  - Comments (Ch. 12, 13, 15, 16), names (Ch. 14): §6.
  - Consistency (Ch. 17): §7.
  - Performance (Ch. 20): §8.
  - Summary of red flags: §10.

Ousterhout, J., & Martin, R. C. (2025). *A philosophy of software design vs Clean Code* [GitHub repository]. https://github.com/johnousterhout/aposd-vs-clean-code
- Taken: the two schools' disagreement on method length, comments and TDD (§4).

Parnas, D. L. (1972). On the criteria to be used in decomposing systems into modules. *Communications of the ACM, 15*(12), 1053-1058. https://doi.org/10.1145/361598.361623
- Taken: each module hiding one design decision (§3, §4 row S).

Reynolds, G. (2019). *Presentation Zen: Simple ideas on presentation design and delivery* (3rd ed.). Pearson.
- Taken: kanso as simplicity reached by elimination, "maximum effect with minimum means" (title note).
