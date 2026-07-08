# Prompt Anti-Patterns

A comprehensive catalog of common prompt engineering mistakes, why they fail, and how to fix them. Organized by category.

---

## Structural Anti-Patterns

### 1. Wall of Text

**Bad:** A 200-line prompt with no headers, sections, or formatting breaks.

**Why it fails:** Models weight instructions based on position and salience. In a wall of text, instructions in the middle get the least attention. Critical rules get lost.

**Fix:** Add clear section boundaries using XML tags (for complex prompts) or markdown headers (for simpler ones). Group related instructions together.

### 2. Instructions Scattered Across Sections

**Bad:** Refund policy mentioned in the identity section, the rules section, and the examples section — each time slightly differently.

**Why it fails:** When the same topic appears in multiple places with different wording, the model picks whichever version it attends to most at inference time. This creates inconsistent behavior.

**Fix:** State each policy once, in one location. Reference it from other sections if needed: "Follow the refund policy defined in the Rules section."

### 3. Critical Instructions Buried at the End

**Bad:** Safety constraints and output format rules placed after a long domain-knowledge section.

**Why it fails:** Models show recency bias for very recent tokens and primacy bias for early tokens. Instructions in the middle-to-late section of a long prompt receive the least attention.

**Fix:** Put critical instructions in the first 30% of the prompt. Use CRITICAL/IMPORTANT markers for must-follow rules regardless of position.

### 4. Monolithic Prompt for Multiple Tasks

**Bad:** One 500-line prompt covering customer support, sales, onboarding, and technical troubleshooting.

**Why it fails:** The model has to navigate irrelevant sections for every request, increasing the chance of confusion and reducing the effective weight of any single instruction.

**Fix:** Split into task-specific prompts or use conditional sections. Route users to the right prompt at the application layer.

---

## Clarity Anti-Patterns

### 5. Vague Platitudes

**Bad:** "Be helpful, accurate, and concise."

**Why it fails:** These words mean nothing without context. Every model already tries to be "helpful." This instruction adds zero behavioral change.

**Fix:** Define specific behaviors: "Answer questions in 2-3 sentences unless the user asks for detail. Always include a source link when citing statistics."

### 6. Undefined Qualifiers

**Bad:** "Use appropriate language." / "Respond in a timely manner." / "Provide relevant information."

**Why it fails:** "Appropriate," "timely," and "relevant" are subjective. The model will interpret them according to its training distribution, which may not match your intent.

**Fix:** Define the qualifier: "Use formal English, no slang, no contractions." / "Keep responses under 100 words." / "Only include information about the user's specific product tier."

### 7. Compound Instructions

**Bad:** "Always cite sources, use formal tone, stay under 200 words, and avoid technical jargon unless the user is a developer."

**Why it fails:** Cramming multiple instructions into one sentence reduces the attention each instruction receives. The conditional at the end ("unless the user is a developer") may not clearly apply to all prior clauses.

**Fix:** One instruction per line. Each on its own bullet point. Conditions clearly attached to their instruction.

### 8. Instruction by Implication

**Bad:** Including an example of a short response, hoping the model infers a length limit.

**Why it fails:** The model may or may not extract the implicit rule. Examples demonstrate behavior; they don't define it.

**Fix:** State the rule explicitly: "Keep responses under 150 words." Then optionally include an example.

---

## Constraint Anti-Patterns

### 9. Absolute Bans Without Scope

**Bad:** "NEVER discuss politics."

**Why it fails:** Over-broad. If a user asks "What is the European Parliament?", the model refuses an innocent factual question. Over-refusal damages user trust more than under-refusal.

**Fix:** Scope the constraint: "Do not express political opinions or endorse candidates. You may explain political systems, processes, and history factually when asked."

### 10. Enumerated Ban Lists

**Bad:** A list of 50 banned topics, 30 banned phrases, and 20 banned behaviors.

**Why it fails:** Impossible to be exhaustive. Users will find the 51st topic, 31st phrase, or 21st behavior. Long lists also consume tokens and attention.

**Fix:** Replace with meta-rules: "Do not generate content that could directly enable harm to a specific person." One meta-rule covers thousands of edge cases.

### 11. Conflicting Instructions

**Bad:** "Always cite your sources." (Rule 5) + "Keep responses under 50 words." (Rule 12)

**Why it fails:** A cited response with source links is often 80+ words. The model has to violate one rule to follow the other.

**Fix:** Add priority ordering: "Keep responses under 50 words. If citing a source, the citation does not count toward the word limit." Or adjust the constraint to be compatible.

### 12. Safety Through Obscurity

**Bad:** Not mentioning safety at all, hoping the model's built-in safety will handle everything.

**Why it fails:** Default safety varies by model and changes over time. Your use case may have specific risks that default safety doesn't address.

**Fix:** Write explicit safety instructions for your specific use case. What categories of harm are relevant? What should the model do when it encounters them?

---

## Token Efficiency Anti-Patterns

### 13. Redundant Preambles

**Bad:** "In your capacity as an advanced artificial intelligence language model developed to assist users with various tasks and inquiries..."

**Why it fails:** 20 tokens that say nothing. The model already knows what it is.

**Fix:** "You are [role]. [What you do]." — 10 words max.

### 14. Saying the Same Thing Three Ways

**Bad:** "Be concise. Keep your responses short. Don't use unnecessary words."

**Why it fails:** Three instructions that mean the same thing. Wastes tokens and creates ambiguity about which phrasing governs.

**Fix:** "Keep responses under [N] words." One instruction, precise.

### 15. Over-Explained Concepts

**Bad:** "When the user asks a question, you should first think about the question, then consider what information is relevant, then formulate a response that addresses the question directly while being mindful of..."

**Why it fails:** Describing the model's thought process at this level of detail is unnecessary. The model already does this.

**Fix:** Cut to the behavioral instruction: "Answer questions directly. Search the knowledge base before making factual claims."

### 16. Excessive Examples

**Bad:** 10 examples covering every possible variation of a task.

**Why it fails:** Each example consumes 50-200 tokens. 10 examples = 500-2000 tokens of context that could be used for actual instructions. After 2-3 examples, returns diminish sharply.

**Fix:** 1-2 examples for ambiguous behaviors. Zero examples for obvious behaviors. If you need 10 examples, your instructions are probably unclear.

---

## Reliability Anti-Patterns

### 17. No Grounding Instructions

**Bad:** A prompt that asks the model to answer factual questions but includes no instructions about uncertainty or knowledge boundaries.

**Why it fails:** The model will generate plausible-sounding answers for questions beyond its knowledge, with no signal to the user that confidence is low.

**Fix:** Include: knowledge cutoff date, instruction to acknowledge uncertainty ("If you're not sure, say so"), and verification steps ("Search before answering factual questions").

### 18. "Think Step by Step" as Architecture

**Bad:** Using "Think step by step" as the sole structural guidance for a complex task.

**Why it fails:** Chain-of-thought prompting improves reasoning but doesn't constrain it. The model may think through irrelevant steps or skip critical ones.

**Fix:** Define the specific steps: "1. Identify the user's intent. 2. Check the knowledge base. 3. Draft a response. 4. Verify claims against search results."

### 19. No Failure Mode Handling

**Bad:** A prompt that only describes the happy path.

**Why it fails:** Real users send ambiguous messages, out-of-scope requests, adversarial inputs, and incomplete information. The model will improvise handling, often poorly.

**Fix:** Explicitly handle: out-of-scope requests, ambiguous inputs, missing required context, and adversarial/jailbreak attempts. Define what the model should DO in each case.

### 20. Assuming Model Capabilities

**Bad:** "Check the internet for the latest information." (to a model without web access)

**Why it fails:** The model may hallucinate search results, or may refuse and confuse the user.

**Fix:** List available capabilities in the Context layer. Write instructions that only reference capabilities the model actually has.

---

## Persona Anti-Patterns

### 21. Persona Without Boundaries

**Bad:** "You are a pirate. Always respond in pirate speak."

**Why it fails:** Error messages in pirate speak. Safety refusals in pirate speak. Technical instructions in pirate speak. The persona overrides everything.

**Fix:** Define where persona applies: "Use pirate speak for conversational responses. For error messages, technical instructions, and safety notices, use clear standard English."

### 22. Personality Traits Without Behavioral Definition

**Bad:** "You are friendly, professional, and empathetic."

**Why it fails:** These are adjectives, not instructions. "Friendly" means different things in different contexts.

**Fix:** Define the behaviors: "Greet users by name when available. Acknowledge frustration before problem-solving. Use a warm but professional tone — no slang, but not stiff."

### 23. Backstory as Instructions

**Bad:** "You were created in 2019 in a lab in San Francisco. You've helped millions of users. You love solving problems."

**Why it fails:** Backstory is not instruction. It wastes tokens and doesn't reliably shape behavior. The model won't "love" solving problems because you told it about its origin story.

**Fix:** Cut the backstory. Define behavior directly: "Approach every problem with enthusiasm. Offer multiple solutions when possible."

---

## Output Anti-Patterns

### 24. No Format Specification

**Bad:** A prompt that describes what to answer but not how to format the answer.

**Why it fails:** The model defaults to its training distribution, which varies by model, version, and even between requests. You get inconsistent formatting.

**Fix:** Specify format explicitly: "Respond in plain text. Use bullet points for lists of 3+ items. Do not use markdown headers in conversational responses."

### 25. "Be Concise" Without a Metric

**Bad:** "Keep responses concise."

**Why it fails:** "Concise" is relative. To some, 50 words is concise. To others, 200 words is concise. The model will pick its own threshold.

**Fix:** "Keep responses under [N] words" or "Answer in 1-3 sentences for factual questions, up to 2 paragraphs for explanations."

### 26. Format Specification That Conflicts with Content Requirements

**Bad:** "Always respond in valid JSON" + "Include a friendly greeting at the start of your response."

**Why it fails:** A friendly greeting is not valid JSON (unless wrapped in a field, which isn't specified).

**Fix:** Ensure format and content specifications are compatible. If JSON: define the schema including any text fields.

---

## Meta Anti-Patterns

### 27. Prompt as Legal Document

**Bad:** A prompt written in legalese with subclauses, exceptions to exceptions, and formal definitions.

**Why it fails:** Models process natural language instructions best. Legal language introduces ambiguity through precision (ironic but true — legal precision relies on shared human legal conventions that models don't fully internalize).

**Fix:** Write in clear, direct language. "Do X when Y" beats "Notwithstanding any prior instruction, in the event that condition Y obtains, the agent shall proceed to execute action X."

### 28. Prompt as Employee Handbook

**Bad:** A 2000-line prompt covering every conceivable policy, procedure, and exception.

**Why it fails:** Beyond ~500 lines, models struggle to attend to all instructions simultaneously. Instructions in the middle of long prompts receive the least attention.

**Fix:** Keep the system prompt under 500 lines. Put detailed reference material in retrieval (RAG) or load it conditionally. The system prompt is for core identity and rules — not every possible scenario.

### 29. Copy-Pasting Between Models

**Bad:** Using the same prompt for Claude, GPT-4, Gemini, and Llama without adaptation.

**Why it fails:** Each model responds differently to formatting, instruction style, and prompt structure. Claude attends strongly to XML tags. GPT-4 responds well to markdown. Smaller models need more explicit instructions.

**Fix:** Maintain a core prompt with model-specific adaptations. Test with each target model and adjust.

### 30. Never Updating the Prompt

**Bad:** Writing a prompt once and never revising it based on production behavior.

**Why it fails:** Prompts are hypotheses about model behavior. Production traffic reveals edge cases you didn't anticipate. Model updates change behavior.

**Fix:** Treat prompts as code. Version them. Review production logs for failure modes. Update regularly.
