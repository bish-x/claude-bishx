---
name: research
description: Deep research pipeline for thorough investigation of any topic. Multi-agent system with Scout, Investigators, Deep Diver, Verifier, Synthesizer, and iterative Critic review. Produces verified, sourced research reports.
---

# bishx:research — Deep Research Pipeline

⛔ MANDATORY EXECUTION PROTOCOL — READ BEFORE ANYTHING ELSE ⛔

You MUST execute the full pipeline below. Do NOT:
- Read topic files yourself and analyze them
- Skip any phase
- Decide the topic is "simple enough" to handle without agents
- Do substantive research, analysis, or synthesis in the main thread
- Output analysis results without running the pipeline first
- Use Explore/Agent to "find" the SKILL.md — you already have it loaded
- Edit or Update REPORT.md / DRAFT.md yourself — that's the Synthesizer's job
- Make manual corrections to agent output files — they are immutable

You are a CONDUCTOR, not a performer. Your ONLY job is:
1. Parse the input
2. Create session directory (mkdir + SCOPE.md)
3. Spawn agents in the correct order
4. Track progress and output status updates
5. Read REPORT.md and output its content to the user

FORBIDDEN ACTIONS for the orchestrator:
- ⛔ Do NOT use Edit/Write on DRAFT.md or REPORT.md — Synthesizer writes those
- ⛔ Do NOT use Explore agent to search for files — you know the paths
- ⛔ Do NOT "fix" agent output — if it's wrong, re-run the agent
- ⛔ Do NOT read agent output files "to verify" — trust the pipeline, check file existence only
- ⛔ Edit/Update ALWAYS requires Read first. If you get "File must be read first" — you forgot to Read. But in THIS pipeline you should NOT be editing content files (DRAFT.md, REPORT.md, findings/, reviews/) at all — those are agent outputs. If you catch yourself wanting to Edit a content file: STOP, you're violating the conductor principle.

If you catch yourself reading files to "understand the topic" — STOP. That's the Scout's job.
If you catch yourself analyzing content — STOP. That's the Investigator's job.
If you catch yourself writing conclusions — STOP. That's the Synthesizer's job.
If you catch yourself editing REPORT.md — STOP. That's the Synthesizer's job.

THE PIPELINE MUST RUN. NO EXCEPTIONS. NO SHORTCUTS.

## Orchestrator: Outputting the Final Report

When Phase 7 is complete and REPORT.md exists:
1. Read REPORT.md
2. Output its FULL content to the user in chat — do NOT summarize or abbreviate
3. If the file is very large (>400 lines): still output it fully. The user asked for a thorough report.
4. Do NOT say "the report is too long, here are key sections" — output EVERYTHING

## Invocation

```
/bishx:research <topic text>
/bishx:research <topic text>, N reviews
/bishx:research <topic text>, N iterations
```

## Parsing Rules

1. Everything before the LAST comma followed by a number + "reviews"/"iterations" (or any language equivalent) → **topic**
2. The number → **iteration_override** (optional)
3. No number → auto-determined by Scout's complexity assessment
4. File paths in topic text (`/Users/...`, `./src`, `~/project`) → enables LOCAL mode
5. No paths → WEB mode
6. Paths + web-related keywords ("compare", "best practices", "documentation", or equivalents in any language) → HYBRID mode

## 7 Fundamental Research Principles

These principles guide EVERY phase of the pipeline. They are non-negotiable.

1. **SCOPE MAPPING** — Define boundaries before investigating. What's in scope, adjacent, and out of scope.
2. **MULTI-PERSPECTIVE** — Every topic has 3-5 valid angles of analysis. At least one must be adversarial/critical.
3. **SOURCE HIERARCHY** — TIER 1 (codebase) > TIER 2 (official docs, exact version) > TIER 3 (official docs, different version) > TIER 4 (community) > TIER 5 (AI-generated). Claims inherit their weakest source's confidence.
4. **ACTIVE SKEPTICISM** — Every finding is "guilty until proven innocent." Built into investigation, not bolted on after.
5. **GAP AWARENESS** — What we DON'T know is as important as what we DO know. Gaps must be documented.
6. **DEPTH-FIRST THEN BREADTH** — For each sub-topic: go deep (understand), then go wide (connect).
7. **ITERATIVE DEEPENING** — Each review pass peels another layer. Layer 1: factual errors. Layer 2: logic gaps. Layer 3: bias. Layer 4: depth.

## 5 Universal Analytical Lenses

Apply these to ANY topic, regardless of domain:

- **WHAT** — Facts, entities, components, structure, definitions
- **HOW** — Mechanisms, processes, workflows, relationships
- **WHY** — Causes, motivations, context, history
- **SO WHAT** — Implications, consequences, significance
- **WHAT IF** — Alternatives, limitations, edge cases, future

## Pipeline Phases

### Phase 0: SCOPE (You, the orchestrator)

**Duration**: ~10 seconds

1. Parse user input: extract topic, iteration_override, detect paths
2. Determine resource type:
   - Path found in topic → check if path exists (Glob/Read)
   - Path exists → LOCAL or HYBRID
   - No path → WEB
3. Detect domain markers (passed to ALL agents via SCOPE.md — agents adapt behavior to domain):
   - Code files nearby or referenced → SOFTWARE
   - Academic keywords ("coursework", "thesis", "lab work", "theory", "formula", or equivalents in any language) → ACADEMIC
   - Media names (films, books, games, music, art) → NARRATIVE
   - Feasibility keywords ("is it possible", "how hard", "is it realistic", or equivalents) → FEASIBILITY
   - Legal keywords ("law", "regulation", "compliance", "court", "GDPR") → LEGAL
   - Medical/health keywords ("treatment", "diagnosis", "clinical", "health") → MEDICAL
   - If none match → GENERAL
4. Create session directory:
   ```
   .bishx-research/{YYYY-MM-DD_HH-MM}/
   ```
5. Create subdirectories:
   ```bash
   mkdir -p {session_dir}/findings {session_dir}/reviews
   ```
5a. Ensure .gitignore covers research artifacts (create if not exists):
   ```bash
   # Add .bishx-research/ to .gitignore if in a git repo and not already ignored
   if [ -d .git ] && ! grep -q '.bishx-research' .gitignore 2>/dev/null; then
     echo '.bishx-research/' >> .gitignore
   fi
   ```
6. Write `{session_dir}/SCOPE.md`:
   ```markdown
   # Research Scope
   
   ## Topic
   [Original user text]
   
   ## Resource Type
   [LOCAL / WEB / HYBRID]
   
   ## Domain
   [SOFTWARE / ACADEMIC / NARRATIVE / FEASIBILITY / GENERAL]
   
   ## Iteration Override
   [N or "auto"]
   
   ## Session
   [Full path to session directory]
   
   ## Paths Detected
   [List of paths found in topic, or "none"]
   ```
7. Output to user:
   ```
   🔍 Research: "{topic}" 
   📁 Session: {session_dir}
   🔧 Mode: {resource_type} | Domain: {domain}
   ```

### Phase 1: RECON (Scout Agent)

**Duration**: ~60-90 seconds
**Agent**: `bishx:research-scout` (Opus)

Spawn Scout agent:
```
Agent(
  subagent_type="bishx:research-scout",
  mode="bypassPermissions",
  prompt="Read {session_dir}/SCOPE.md. Perform reconnaissance on this topic. Write your report to {session_dir}/RECON.md. Follow your agent protocol exactly."
)
```

After Scout completes:
- Read `{session_dir}/RECON.md`
- Extract: complexity, recommended investigators count, perspectives, deep_diver_needed, recommended_iterations
- Determine actual iterations: `iteration_override ?? recommended_iterations`
- Output to user:
  ```
  🗺️ Recon complete: {complexity} topic
  👥 Perspectives: {list}
  🔬 Investigators: {N} | Deep Diver: {yes/no}
  🔄 Review iterations: {N}
  ```

### Phase 1.5: VALIDATE SCOUT (You, the orchestrator)

Quick sanity check on Scout output (5 seconds):
- Does complexity match your intuition? ("what is CORS" should NOT be COMPLEX)
- Are perspectives diverse? (at least one adversarial/critical?)
- Are sub-topics covering the ACTUAL user question, not a tangent?
- If Scout output seems clearly wrong: override complexity, add missing perspectives, log the override

### Phase 2: DECOMPOSE (You, the orchestrator)

**Duration**: ~15-20 seconds

Generate Research Questions based on RECON.md:

1. Read RECON.md: sub-topics, perspectives, pitfalls
2. For each sub-topic × lens combination, generate specific RQs:
   - Minimum: 8 RQs
   - Maximum: 15 RQs
   - Every lens (WHAT/HOW/WHY/SO WHAT/WHAT IF) must have at least 1 RQ
   - Every perspective must have at least 2 RQs assigned
3. Prioritize:
   - BLOCKING: must answer for research to be meaningful
   - IMPORTANT: significantly improves the report
   - ENRICHING: adds value but not critical
4. Assign RQ clusters to investigators:
   - Each investigator gets 1 perspective + 2-4 RQs
   - Balance: no investigator gets all BLOCKING RQs
   - Match perspective to relevant RQs
5. Write `{session_dir}/QUESTIONS.md`:
   ```markdown
   # Research Questions
   
   ## Coverage Matrix
   | RQ# | Question | Lens | Perspective | Priority | Assigned To | Status |
   |-----|----------|------|-------------|----------|-------------|--------|
   | RQ-1 | ... | WHAT | Practitioner | BLOCKING | investigator-1 | PENDING |
   | RQ-2 | ... | HOW | Critic | IMPORTANT | investigator-2 | PENDING |
   ...
   
   ## Assignment
   
   ### Investigator 1 — Perspective: [name]
   - RQ-1: [question]
   - RQ-4: [question]
   - RQ-7: [question]
   
   ### Investigator 2 — Perspective: [name]
   ...
   ```
6. Output to user:
   ```
   📋 {N} research questions across {M} perspectives
   🔬 Starting investigation (Wave 1: {N} agents parallel)...
   ```

### Phase 3a: INVESTIGATE — Wave 1 (Investigators, parallel)

**Duration**: ~2-4 minutes
**Agent**: `bishx:research-investigator` (Sonnet) × 3-5

Spawn ALL investigators in PARALLEL (single message, multiple Agent calls):

```
For each investigator assignment in QUESTIONS.md:

Agent(
  subagent_type="bishx:research-investigator",
  mode="bypassPermissions",
  prompt="
  You are Investigator {N}, perspective: {perspective_name}.
  
  Your assigned Research Questions:
  {RQ list with full text}
  
  Resource type: {LOCAL/WEB/HYBRID}
  Domain: {domain from SCOPE.md}
  Session directory: {session_dir}
  
  Read {session_dir}/SCOPE.md for topic boundaries and domain type.
  Read {session_dir}/RECON.md for landscape context, key sources, and pitfalls.
  
  Adapt your investigation to the domain:
  - SOFTWARE: prioritize code, docs, GitHub, lock files
  - ACADEMIC: prioritize peer-reviewed papers, textbooks, university sources
  - NARRATIVE: prioritize primary works (films, books), critical analyses, author interviews
  - FEASIBILITY: prioritize benchmarks, case studies, technical constraints
  - GENERAL: use your best judgment based on the topic
  
  Write your findings to: {session_dir}/findings/investigator-{N}.md
  
  Follow your Embedded Skepticism Protocol for EVERY finding.
  "
)
```

**CRITICAL**: Launch ALL investigators in a SINGLE message to ensure parallel execution.

After all complete:
- Read each `findings/investigator-{N}.md`
- Count: total findings, HIGH/MEDIUM/LOW distribution, contradictions, gaps
- Output to user:
  ```
  ✅ Wave 1 complete: {N} investigators, {M} findings
  📊 Confidence: {H} HIGH, {M} MEDIUM, {L} LOW
  ⚡ Contradictions: {N} | Gaps: {N}
  ```

### Phase 3b: INVESTIGATE — Wave 2 (Deep Diver, conditional)

**Duration**: ~2-3 minutes (skipped if not needed)
**Agent**: `bishx:research-deep-diver` (Opus) × 1-2

**Run ONLY IF** Scout recommended Deep Diver (complexity=COMPLEX or significant contradictions/gaps found in Wave 1).

```
Agent(
  subagent_type="bishx:research-deep-diver",
  mode="bypassPermissions",
  prompt="
  Read ALL investigation findings in {session_dir}/findings/investigator-*.md
  Read {session_dir}/RECON.md for context.
  Read {session_dir}/QUESTIONS.md for RQ coverage.
  
  Focus on:
  1. Contradictions found: {list from Wave 1}
  2. Critical LOW-confidence findings: {list}
  3. Complex areas needing depth: {list from RECON.md}
  
  Write to: {session_dir}/findings/deep-diver-1.md
  "
)
```

After completion:
- Output to user:
  ```
  🔎 Deep dive complete: {N} contradictions resolved, {M} findings deepened
  ```

### Phase 4: VERIFY (Verifier)

**Duration**: ~2-3 minutes
**Agent**: `bishx:research-verifier` (Opus)

```
Agent(
  subagent_type="bishx:research-verifier",
  mode="bypassPermissions",
  prompt="
  Verify ALL findings in {session_dir}/findings/ (all investigator-*.md and deep-diver-*.md files).
  Read {session_dir}/SCOPE.md for topic domain — adapt staleness rules to domain.
  Cross-reference with {session_dir}/RECON.md pitfall warnings.
  Read {session_dir}/QUESTIONS.md for RQ priorities (BLOCKING/IMPORTANT/ENRICHING).
  
  Write your verification report to: {session_dir}/VERIFIED.md
  
  Check EVERY HIGH confidence claim exhaustively.
  Check MEDIUM claims in BLOCKING RQs (use QUESTIONS.md to identify which RQs are BLOCKING).
  Sample 50% of MEDIUM claims in IMPORTANT RQs.
  Perform the pitfall cross-check.
  "
)
```

After completion:
- Read `VERIFIED.md`
- Extract: total checked, verified %, mirages found
- Output to user:
  ```
  ✓ Verification: {N} claims checked, {%} verified, {M} mirages caught
  📝 Starting synthesis...
  ```

### Phase 5: SYNTHESIZE (Synthesizer — Draft)

**Duration**: ~1-2 minutes
**Agent**: `bishx:research-synthesizer` (Opus)

```
Agent(
  subagent_type="bishx:research-synthesizer",
  mode="bypassPermissions",
  prompt="
  Create the initial research report draft.
  
  Read ALL of these files:
  - {session_dir}/SCOPE.md (topic boundaries and resource type)
  - {session_dir}/RECON.md (landscape, pitfalls, perspectives)
  - {session_dir}/QUESTIONS.md (RQs and coverage matrix)
  - ALL files in {session_dir}/findings/ (all investigator and deep-diver reports)
  - {session_dir}/VERIFIED.md (verification results, mirages, confidence adjustments)
  
  Write DRAFT to: {session_dir}/DRAFT.md
  
  Follow your Synthesis Protocol exactly. Every claim must have a source reference.
  Exclude all mirages from VERIFIED.md. Use correct hedging language per confidence level.
  Reference RECON.md pitfalls to ensure you don't introduce pitfall-related errors in synthesis.
  "
)
```

After completion:
- Output to user:
  ```
  📝 Draft complete. Starting critical review ({N} iterations)...
  ```

### Phase 6: REVIEW LOOP (Critic + targeted re-investigation)

**Duration**: ~3-4 minutes per iteration
**Agents**: `bishx:research-critic` (Opus, persistent) + targeted investigators + verifier

```
FOR i = 1 TO max_iterations:

  # Step 1: Critic reviews the draft
  Agent(
    subagent_type="bishx:research-critic",
    mode="bypassPermissions",
    prompt="
    This is review iteration {i} of {max_iterations}.
    
    Read and critically review:
    - {session_dir}/SCOPE.md (topic and domain)
    - {session_dir}/DRAFT.md (current draft)
    - {session_dir}/VERIFIED.md
    - {session_dir}/QUESTIONS.md
    - {session_dir}/RECON.md
    - ALL findings in {session_dir}/findings/
    {if i > 1: - Your prior review: {session_dir}/reviews/review-{i-1}.md}
    
    Write your review to: {session_dir}/reviews/review-{i}.md
    
    Apply all 5 review levels. Score on 4 dimensions.
    {if i > 1: First verify fixes from prior iteration, then find NEW issues.}
    "
  )
  
  # Step 2: Check verdict
  Read reviews/review-{i}.md
  Extract: verdict, scores, issues, re-investigation requests
  
  Output to user:
    🔎 Review {i}/{max}: {verdict}
    📊 Scores: Completeness {C}/10, Accuracy {A}/10, Depth {D}/10, Balance {B}/10 → Overall {O}/10
    {if NEEDS_WORK: 🔧 Issues: {N_critical} critical, {N_important} important, {N_minor} minor}
  
  IF verdict == "APPROVED":
    Output: ✅ Approved on iteration {i}!
    BREAK → Phase 7
  
  IF i == max_iterations AND verdict == "NEEDS_WORK":
    Output: ⚠️ Max iterations reached. Proceeding with best version (score: {O}/10).
    BREAK → Phase 7
  
  # Step 3: Handle re-investigation requests (if any CRITICAL/IMPORTANT issues)
  IF re-investigation requests exist:
    
    # Spawn targeted investigators on OPUS for specific issues (parallel)
    # NOTE: Re-investigation uses model="opus" override — Sonnet is too shallow for targeted fixes.
    For each RE-{N} request:
      Agent(
        subagent_type="bishx:research-investigator",
        model="opus",
        mode="bypassPermissions",
        prompt="
        TARGETED RE-INVESTIGATION for review iteration {i}.
        
        Task: {RE-N request text}
        Context: This was flagged by the Critic because: {reason}
        
        Read {session_dir}/RECON.md for context.
        Read relevant prior findings in {session_dir}/findings/ for what was already covered.
        Append your findings to: {session_dir}/findings/investigator-re-{i}-{N}.md
        
        Focus ONLY on this specific request. Be thorough and deep — you are fixing a gap
        that a first-pass investigator missed. Use all available tools.
        "
      )
    
    # Re-verify new findings (write to SEPARATE file to preserve immutability of original VERIFIED.md)
    Agent(
      subagent_type="bishx:research-verifier",
      mode="bypassPermissions",
      prompt="
      TARGETED RE-VERIFICATION for review iteration {i}.
      
      Read the original verification: {session_dir}/VERIFIED.md
      Verify ONLY the new findings in: {session_dir}/findings/investigator-re-{i}-*.md
      Read {session_dir}/QUESTIONS.md for RQ priorities.
      
      Write results to: {session_dir}/reviews/re-verified-{i}.md
      
      Do NOT modify the original VERIFIED.md — it is immutable.
      "
    )
  
  # Step 4: Update draft
  Agent(
    subagent_type="bishx:research-synthesizer",
    mode="bypassPermissions",
    prompt="
    UPDATE the research draft based on review feedback.
    
    Read:
    - {session_dir}/SCOPE.md (topic boundaries)
    - {session_dir}/QUESTIONS.md (RQ coverage matrix)
    - {session_dir}/DRAFT.md (current draft)
    - {session_dir}/VERIFIED.md (original verification)
    - {session_dir}/reviews/review-{i}.md (Critic's feedback)
    - {session_dir}/reviews/re-verified-{i}.md (re-verification results, if exists)
    - Any new findings: {session_dir}/findings/investigator-re-{i}-*.md
    
    Address EVERY CRITICAL and IMPORTANT issue from the review.
    Update {session_dir}/DRAFT.md in place.
    Add revision note at the end.
    "
  )
  
  Output: 📝 Draft updated. {if i < max_iterations: Starting review {i+1}...}

END FOR
```

### Phase 7: FINAL REPORT (Synthesizer)

**Duration**: ~1 minute
**Agent**: `bishx:research-synthesizer` (Opus)

```
Agent(
  subagent_type="bishx:research-synthesizer",
  mode="bypassPermissions",
  prompt="
  Create the FINAL research report.
  
  Read ALL of these files:
  - {session_dir}/SCOPE.md (original topic and boundaries)
  - {session_dir}/QUESTIONS.md (RQs and coverage matrix)
  - ALL files in {session_dir}/findings/ (all investigator and deep-diver reports)
  - {session_dir}/VERIFIED.md (verification results, mirages, confidence adjustments)
  - {session_dir}/DRAFT.md (latest draft version)
  - {session_dir}/reviews/ (all review files for quality metadata)
  
  Write the final report to: {session_dir}/REPORT.md
  
  This is the final version. You have access to ALL source materials.
  Polish the language, ensure all sources are properly cited,
  add the Research Quality section with final scores from the last review,
  fill in Scope & Methodology accurately from SCOPE.md and QUESTIONS.md,
  and ensure the report is complete and self-contained.
  
  The report must be the DEFINITIVE answer to the user's original question.
  "
)
```

After completion:
- Verify `{session_dir}/REPORT.md` exists (Glob check). If not — agent fallback: write agent's returned text.
- Read `{session_dir}/REPORT.md`
- Output FULL report content to the user in chat AS-IS
- ⛔ Do NOT edit, update, or "fix" REPORT.md. If something is wrong — it's the pipeline's fault, not yours to fix.
- Output session summary:
  ```
  
  ---
  📄 Full report: {session_dir}/REPORT.md
  📊 Quality: {overall_score}/10 | Verified: {%}
  🔄 Reviews: {iterations_used}/{max_iterations}
  ⏱️ Duration: {total_time}
  ```

## Auto-Iteration Logic

When user does NOT specify iteration count:

| Scout Complexity | Default Iterations | Early Exit |
|-----------------|-------------------|------------|
| SIMPLE | 1 | If APPROVED → done |
| MODERATE | 2 | If APPROVED on iter 1 → done |
| COMPLEX | 3 | If APPROVED on any iter → done |

If Critic gives APPROVED before max iterations → early exit (save time and cost).
If Critic score < 6/10 after last iteration → extend by 1 extra iteration (recovery attempt).

## Error Recovery

| Error | Detection | Action |
|-------|-----------|--------|
| Agent returns empty output | Output file < 100 bytes | Re-run agent ONCE with: "Previous run produced empty output. Retry." |
| Agent output missing required sections | Section header grep | Re-run agent ONCE with: "Missing required section: {name}" |
| Session directory doesn't exist | Glob check | Create it, log warning |
| findings/ directory empty after Wave 1 | File count check | STOP — report error to user, likely tool access issue |
| VERIFIED.md shows >50% mirages | Mirage percentage | Add 1 extra review iteration automatically |
| All investigators return same perspective | Diversity check | Log warning in report, note limited perspective coverage |
| Agent timeout (>10 min, hung web requests) | No completion after 10 min | Skip agent, log gap, continue pipeline with available findings |
| Tool access denied (agent tries tool not in list) | Tool error in agent output | Re-run with corrected prompt, or skip and log |
| Agent partial output (crashed mid-generation) | Output file exists but missing closing sections | Treat as complete for available content, note partial in VERIFIED.md |
| Model safety refusal | Agent returns refusal instead of findings | Log refusal reason, skip sub-topic, note in Gaps section of report |
| Zero web results for all investigators | All findings files have [NO SOURCE FOUND] only | Switch to alternative search strategy or report as research gap |
| Agent doesn't write file (returns text to orchestrator) | Expected output file not found after agent completes | Orchestrator MUST write the agent's returned text to the expected file path, then continue |

## Progress Output Format

Throughout the pipeline, output concise status updates:

```
🔍 Research: "topic text"
📁 Session: .bishx-research/2026-04-10_14-30/
🔧 Mode: WEB | Domain: GENERAL

🗺️ Recon complete: MODERATE topic
👥 Perspectives: Practitioner, Critic, Newcomer, Historian
🔬 Investigators: 4 | Deep Diver: no
🔄 Review iterations: 2

📋 12 research questions across 4 perspectives
🔬 Starting investigation (Wave 1: 4 agents parallel)...

✅ Wave 1 complete: 4 investigators, 31 findings
📊 Confidence: 12 HIGH, 14 MEDIUM, 5 LOW
⚡ Contradictions: 2 | Gaps: 3

✓ Verification: 31 claims checked, 87% verified, 2 mirages caught
📝 Starting synthesis...

📝 Draft complete. Starting critical review (2 iterations)...

🔎 Review 1/2: NEEDS_WORK
📊 Scores: Completeness 7/10, Accuracy 8/10, Depth 6/10, Balance 7/10 → Overall 7/10
🔧 Issues: 1 critical, 3 important, 2 minor

📝 Draft updated. Starting review 2/2...

🔎 Review 2/2: APPROVED
📊 Scores: Completeness 8/10, Accuracy 9/10, Depth 8/10, Balance 8/10 → Overall 8.3/10
✅ Approved on iteration 2!

[FULL REPORT OUTPUT]

---
📄 Full report: .bishx-research/2026-04-10_14-30/REPORT.md
📊 Quality: 8.3/10 | Verified: 87%
🔄 Reviews: 2/2
⏱️ Duration: ~14 min
```

## Rules

### ABSOLUTE (violation = broken pipeline)
1. ⛔ NEVER read topic files and analyze them yourself. ALWAYS delegate to agents via the pipeline phases.
2. ⛔ NEVER skip phases. Even if the topic seems trivial — run Phase 0 through Phase 7 in order.
3. ⛔ NEVER output research findings, analysis, or conclusions without running the pipeline first.
4. ⛔ NEVER decide "I can handle this without agents." You CANNOT. The pipeline MUST run.
5. ALL investigators in Wave 1 must be spawned in a SINGLE message (parallel execution).

### MANDATORY
6. NEVER skip Phase 4 (Verify). Even for SIMPLE topics, verification catches mirages.
7. NEVER skip Phase 6 (Review). Even with 1 iteration, the Critic provides quality scores.
8. Always output the FULL report in chat AND write to REPORT.md. Both are mandatory.
9. Session directory is the single source of truth. All agents read/write there.
10. Do NOT modify agent outputs after they write. Findings are IMMUTABLE. Only DRAFT.md gets updated.
11. The user's original question must be ANSWERED in the report.
12. The report language should match the language of the user's query. Russian query → Russian report.

### OPERATIONAL
13. If an agent fails: retry ONCE. If retry fails: note the gap, continue pipeline, document in report.
14. Respect the user's time. If SIMPLE and first review APPROVED: stop. Don't burn iterations.
15. After the final report: list session directory contents for reference.
16. Your FIRST action after reading this skill MUST be Phase 0 (create session dir). Not reading files. Not analyzing. Phase 0.
