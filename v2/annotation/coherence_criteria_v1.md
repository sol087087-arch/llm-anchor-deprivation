Coherence Evaluation Criteria 
1. Coherent
A text is coherent if it satisfies most of the following:
* Semantic continuity: A consistent topic or conceptual thread is maintained.
* Referential stability: Entities or concepts persist across sentences.
* Relational structure: Logical, causal, or associative links connect parts of the text.
* Progression: The text extends or transforms the prior line rather than merely repeating its lexical or conceptual material.
* Interpretability: The text can be paraphrased without significant loss of meaning.
* Linguistic integrity: No malformed tokens or syntactic breakdown.
2. Borderline
A text is borderline if:
* Weak semantic cohesion: Ideas accumulate without clear progression.
* Reduced interpretability: Paraphrasing is difficult or partial.
* Conceptual drift: Topics shift without clear linkage.
* Limited progression: The text recycles prior motifs without adding meaningful development.
* Minor linguistic anomalies: Occasional malformed or fused tokens.
3. Degenerate
A text is degenerate if any of the following hold:
* Semantic collapse: No recoverable meaning or paraphrasable content.
* Loss of referential structure: Absence of stable entities or relations.
* Fragmentation: Sentences form an unconnected sequence.
* No progression: Output stalls in repetition, looping, or static re-description.
* Pseudo-semantic output: Fluent surface form without underlying meaning.
4. Hard Degenerate
* Token-level corruption: Non-words, mixed scripts, or code-like artifacts.
* Syntactic failure: Grammar no longer supports interpretation.
Fast Heuristic (3-step)
1. Paraphrasability test
   * Yes - Coherent
   * Partial - Borderline
   * No - Degenerate
2. Progression test
   * Extends prior material - Coherent
   * Mostly restates prior material - Borderline
   * Loops or stalls - Degenerate
3. Form check
   * Clean language - OK
   * Corrupted tokens - Hard Degenerate
Core Principle
Surface fluency is not evidence of semantic coherence; coherence also requires progression across adjacent steps.
