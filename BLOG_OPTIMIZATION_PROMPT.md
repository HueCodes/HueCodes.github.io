# Blog Post Optimization Prompt Template

Use this prompt to transform any project blog post into a professional, interview-ready format.

---

## Prompt to Use

```
I need you to rewrite my [PROJECT_NAME] blog post to be professional and interview-ready, following the same approach used for the Go-Hunter blog post.

**Project**: [PROJECT_NAME]
**Blog location**: /Users/hugh/Dev/projects/HueCodes.github.io/_posts/[YYYY-MM-DD-project-name].md

**Requirements**:

1. **Structure**
   - Paragraph-focused writing (minimize headings to 1-2 maximum)
   - Flow naturally without breaking up ideas with excessive headings
   - Maintain any existing images/graphics in strategic positions
   - Keep opening one-liner technical summary
   - Add context paragraph explaining the problem/use case

2. **Tone and Style**
   - Professional and direct (suitable for technical interviews)
   - No em dashes (—) or emojis
   - No casual phrases like "works but feels limited" or "Reality is messy"
   - No self-deprecating conclusions
   - Fix any grammatical errors or typos
   - Use active voice
   - Avoid hedging language ("kind of", "sort of", "trying to")

3. **Technical Depth**
   - Every paragraph should include specific technical details or metrics
   - Name technologies/patterns explicitly
   - Discuss tradeoffs thoughtfully (not as weaknesses)
   - Include concrete examples where relevant
   - Add performance metrics if available
   - Show architectural thinking

4. **Content Strategy**
   - Opening: Establish the problem space and why it matters
   - Middle: Technical implementation with specific decisions explained
   - Architecture section: Show systems thinking (if applicable)
   - Learnings: Frame challenges constructively with growth mindset
   - Future work: Mentioned as natural evolution, not incomplete features

5. **Length**
   - Target: 700-900 words for substantial projects
   - Target: 500-700 words for smaller/simpler projects
   - No unnecessary verbosity, but sufficient depth to showcase competence

6. **Interview-Ready Elements**
   Answer these questions naturally in the flow:
   - Why this technology stack?
   - What was the hardest technical challenge?
   - What would you do differently?
   - How did you ensure quality/security/performance?
   - What patterns did you learn/apply?

**Reference Example**:
Use /Users/hugh/Dev/projects/HueCodes.github.io/_posts/2026-01-31-go-hunter.md as the gold standard for tone, structure, and technical depth.

**Process**:
1. Read the current blog post at the specified location
2. Read the Go-Hunter blog post for style reference
3. Read other blog posts (forge-db, nodix, gretun) to understand the consistent voice
4. Create a draft at [BLOG_FILE].DRAFT
5. Show me a comparison of key sections (before/after) for approval
6. After approval, replace the original and commit with a descriptive message

**Output**:
First, create the draft and show me:
- Word count comparison (before/after)
- Preview of opening paragraph (before/after)
- Preview of any substantially changed sections
- List of key improvements made

Wait for my approval before replacing the original file.
```

---

## Usage Instructions

### For Each Project Blog:

1. **Copy the prompt above**

2. **Fill in the placeholders**:
   - `[PROJECT_NAME]` → The actual project name (e.g., "Forge-DB", "Nodix", "Gretun")
   - `[YYYY-MM-DD-project-name]` → The actual blog post filename

3. **Add project-specific context** (optional):
   If there are specific things you want emphasized, add:
   ```
   **Additional Context for [PROJECT_NAME]**:
   - Emphasize: [key technical achievement]
   - Include metric: [specific performance number]
   - Explain tradeoff: [specific decision made]
   ```

4. **Paste into Claude** and review the draft before approving

---

## Example Usage

### For Forge-DB:
```
I need you to rewrite my Forge-DB blog post to be professional and interview-ready, following the same approach used for the Go-Hunter blog post.

**Project**: Forge-DB
**Blog location**: /Users/hugh/Dev/projects/HueCodes.github.io/_posts/2026-01-31-forge-db.md

[...rest of requirements from template above...]

**Additional Context for Forge-DB**:
- Emphasize: The SQLite-inspired B-tree implementation
- Include metric: Transaction throughput if available
- Explain tradeoff: Simplicity vs feature completeness for educational project
```

### For Nodix:
```
I need you to rewrite my Nodix blog post to be professional and interview-ready, following the same approach used for the Go-Hunter blog post.

**Project**: Nodix
**Blog location**: /Users/hugh/Dev/projects/HueCodes.github.io/_posts/2026-01-31-nodix.md

[...rest of requirements from template above...]

**Additional Context for Nodix**:
- Emphasize: Determinism guarantees in distributed systems
- Include metric: Message throughput or latency numbers
- Explain tradeoff: Determinism vs throughput (the core design decision)
```

### For Gretun:
```
I need you to rewrite my Gretun blog post to be professional and interview-ready, following the same approach used for the Go-Hunter blog post.

**Project**: Gretun
**Blog location**: /Users/hugh/Dev/projects/HueCodes.github.io/_posts/2026-01-31-gretun.md

[...rest of requirements from template above...]

**Additional Context for Gretun**:
- Emphasize: TUN device programming and packet routing
- Include metric: Throughput or MTU discovery details
- Explain tradeoff: Complexity of kernel interfaces
```

---

## Quality Checklist

After the rewrite, verify:

### Structure
- [ ] 1-2 headings maximum (preferably just 1 for "Architecture" or similar)
- [ ] Flows in paragraphs, not lists
- [ ] Images/graphics positioned strategically
- [ ] Natural narrative progression

### Tone
- [ ] No em dashes or emojis
- [ ] No casual/self-deprecating language
- [ ] Professional and confident
- [ ] Grammatically correct

### Technical Depth
- [ ] Specific technologies named
- [ ] Metrics or numbers included where available
- [ ] Architectural decisions explained
- [ ] Tradeoffs discussed constructively
- [ ] Shows systems thinking

### Length
- [ ] Appropriate depth (700-900 words for major projects)
- [ ] Not verbose, but sufficiently detailed
- [ ] Every paragraph advances the narrative

### Interview Readiness
- [ ] Demonstrates competence without bragging
- [ ] Shows learning and iteration
- [ ] Explains why, not just what
- [ ] Future work framed positively
- [ ] Would impress a technical interviewer

---

## Before/After Comparison

### Go-Hunter Example

**Before (Opening)**:
> "Attack surface management answers: 'What's exposed to the internet?' Organizations spin up cloud resources constantly..."

**After (Opening)**:
> "Organizations running multi-cloud infrastructure face a fundamental visibility problem. Development teams spin up VMs, storage buckets, DNS records, and load balancers across different providers without a unified inventory. Each resource is a potential attack vector..."

**Improvements**:
- More professional opening
- Explains the business problem clearly
- Sets context for non-security audiences
- Removes casual question format

**Before (Conclusion)**:
> "The HTMX dashboard works but feels limited for complex filtering."

**After (Conclusion)**:
> "The HTMX dashboard works well for basic operations but shows its limits with complex filtering and data visualization. Building it was fast, which mattered for shipping quickly. A proper frontend framework would enable richer interactions for power users. That trade-off made sense for an initial version."

**Improvements**:
- Frames limitation as a conscious tradeoff
- Explains the reasoning (fast to build, ship quickly)
- Shows maturity in decision-making
- Future work is constructive

---

## Common Transformations

### Remove Casual Language
- ❌ "Reality is messy" → ✅ "Organizations use multiple cloud providers"
- ❌ "works but feels limited" → ✅ "shows its limits with complex use cases"
- ❌ "The goal with X is to solve this" → ✅ "X addresses this by..."

### Add Technical Specificity
- ❌ "Uses a database" → ✅ "Uses PostgreSQL with JSONB for flexible metadata"
- ❌ "Fast performance" → ✅ "1,000 assets/minute throughput"
- ❌ "Concurrent scanning" → ✅ "Worker pool pattern with goroutines and semaphore-based concurrency control"

### Frame Tradeoffs Constructively
- ❌ "Should have used X instead" → ✅ "Y worked well for initial version; X would enable Z for power users"
- ❌ "This is a limitation" → ✅ "This trade-off prioritized A over B, which made sense because..."
- ❌ "Doesn't support X" → ✅ "Next iteration would add X to enable Y use case"

### Expand Architecture Descriptions
- ❌ "Uses Chi and PostgreSQL" → ✅ "Stateless API server built with Chi handles requests while PostgreSQL stores all state, enabling horizontal scaling"
- ❌ "Background workers process jobs" → ✅ "Background workers using Asynq pull jobs from Redis queue, execute operations, and write results back. Adding capacity means spinning up more workers with no coordination required."

---

## Tips for Success

1. **Read Go-Hunter first**: It's your reference for tone and depth

2. **Focus on flow**: Paragraphs should connect naturally, not feel like a list

3. **Show technical maturity**: Explain decisions, not just implementations

4. **Use metrics**: Numbers make claims concrete and impressive

5. **Frame learning positively**: "taught me that..." not "I should have..."

6. **Balance depth and brevity**: Detailed enough to impress, concise enough to scan

7. **Think interview context**: Would this make a hiring manager want to ask follow-up questions?

---

## Batch Processing (All Blogs)

If you want to optimize all blog posts at once:

```
I need you to optimize all my project blog posts for professional presentation, using the Go-Hunter blog as the reference standard.

**Blog directory**: /Users/hugh/Dev/projects/HueCodes.github.io/_posts/

**Projects to optimize**:
1. Forge-DB (2026-01-31-forge-db.md)
2. Nodix (2026-01-31-nodix.md)
3. Gretun (2026-01-31-gretun.md)
[Add any others...]

**Approach**:
- Read all existing blog posts to understand current voice
- Use Go-Hunter (2026-01-31-go-hunter.md) as the gold standard
- For each project, create a .DRAFT file with improvements
- Show me a summary of changes for all projects
- Wait for approval before committing

**Requirements**: [Use same requirements from template above]

**Process for each blog**:
1. Analyze current state
2. Create draft with improvements
3. Highlight key changes
4. Get approval
5. Commit with descriptive message

Start with Forge-DB, then proceed to the others after I approve each one.
```

---

## Success Metrics

A successfully optimized blog post should:

✅ **Sound professional** when read aloud
✅ **Demonstrate competence** without overselling
✅ **Explain technical decisions** with reasoning
✅ **Flow naturally** in paragraph form
✅ **Be scannable** despite paragraph format (through strategic structure)
✅ **Impress technical interviewers** with depth and maturity
✅ **Show growth mindset** in learnings section
✅ **Be 700-900 words** for substantial projects

---

## Maintenance

As you create new project blog posts in the future, use this checklist:

**Writing a New Blog Post**:
1. Write initial draft focusing on technical accuracy
2. Use this optimization prompt to polish it
3. Compare against Go-Hunter for tone/structure
4. Ensure all quality checklist items pass
5. Get feedback if unsure
6. Publish when professional and interview-ready

**Updating Existing Posts**:
- Run through this optimization prompt annually
- Update with new metrics or learnings
- Ensure tone remains professional as standards evolve
- Keep Go-Hunter as the reference standard

---

## Questions to Ask Claude

If you're unsure about a section:

1. "Is this paragraph too casual for a professional blog?"
2. "Does this architecture description show systems thinking?"
3. "How can I frame this limitation more constructively?"
4. "What technical details should I add to strengthen this section?"
5. "Does this match the tone of the Go-Hunter blog post?"

---

## Final Note

The goal is **not** to sound corporate or sterile. The goal is to:
- Sound confident and competent
- Demonstrate technical thinking
- Show maturity in decision-making
- Be suitable for someone evaluating you for a senior role

Your personality and voice should still come through, just in a more polished, professional way.

**Reference**: The Go-Hunter blog post at `/Users/hugh/Dev/projects/HueCodes.github.io/_posts/2026-01-31-go-hunter.md` is your gold standard. When in doubt, match its tone.
