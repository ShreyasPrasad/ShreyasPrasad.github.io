Some messy thoughts on how I use AI and if the end is near.
### Using it at work

Over the past few weeks, I've been using AI tools at work to develop a new feature. I've used Claude 3.7 for code generation through Amazon Q CLI. Prior to beginning my 10x vibe coding activities, I decided it would be worth it to spend time structuring my AI use. Other engineers at the company had anecdotes of jumping in and quickly ending up with large chunks of unmaintainable code. 

The general advice was to following a highly opinionated version of prompt-driven development (PDD). AI excels when it is tasked with solving small, unambiguous problems since it does not suffer greatly from holes in its context window in these situations. 

With this in mind, I dedicated time towards writing deep context on the codebase, a detailed description of the problem, and a step-by-step list of what my implementation plan would be. Each incremental step I wrote was a small self-contained task that I could ask AI to go implement. Keeping these steps small allowed me to intervene as needed and easily revert back to an safe ground state if Claude veered off course. This ended up working really well, and I estimate my time savings for working on the feature to be approximately 50%.
### The problems 

AI has fundamentally simplified a lot of the mundane, undesirable parts of software development like boilerplate generation and writing unit tests. At the same, it has introduced a whole new class of problems. Namely, context management and preventing bugs from creeping in.

You may be coding 10x as fast. But that means you are likely generating bugs 10x as fast as well. As models improve and expand, bug generation will definitely reduce. However, until then, treat AI code with what it deserves: a lot of skepticism. 

For me, that has meant:
- Double checking that unit tests actually test the desired functionality. Not that they test that a bug is in fact a bug :)
- Being extra vigilant during code reviews.

### Is the end near?

Is this the end of modern software engineering and human ingenuity? I think it's the opposite. As AI use ramps up and we're faced with incredible advanced bugs and generated code, those with deep understanding shine. I still think AI is the most effective tool in the hands of an experienced engineer that understands its shortfalls from first principles.