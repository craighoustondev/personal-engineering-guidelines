# Long-form structure

For blog posts, published articles, and anything with sections. Read alongside
[SKILL.md](../SKILL.md) — the voice rules there still apply.

## Shape

1. **Open with the reader's pain**, usually as a question or a named pitfall.
   - *"Ever shipped a feature that didn't solve your customers problem or struggled
     to measure whether it made any difference at all?"*
   - *"One of the most common pitfalls when integrating external systems is allowing
     their implementation details to leak into your domain."*
   - Or with your own unanswered question: *"I was asked whether AI had helped us
     deliver it faster. I struggled to answer the question."*

2. **State the promise in one first-person sentence.** *"In this post, I'll share
   how testing in production and working closely with users has helped me close
   that gap."* This is signposting, not throat-clearing — it names what the reader
   gets and commits you to delivering it.

3. **Body sections, often question-shaped H2s.** "What does it mean to test in
   production?", "What is meant by domain?", "Why it's important to separate
   external dependencies from the domain", "Did this solve our problems?"

4. **Close by answering the opening question**, by name where possible: *"So, has
   AI made us faster?"*, *"Did this solve our problems? In short, yes."* Do not
   write a paragraph that restates the body.

## Bridges between sections

End a section by handing off to the next. The reader should never wonder why the
next heading follows this one.

- *"Once we've built confidence internally, the real learning comes from watching
  what happens in production."*
- *"Monitoring tells you what's happening, but it doesn't always explain why.
  That's where direct conversations with users come in."*
- *"What we needed was a way to isolate our domain from these concerns entirely.
  Enter Ports & Adapters."*

## Sourcing

Split the work: the source carries the general claim, your own paragraph carries
the contextual one.

- Name the person and their standing, and link them — Charity Majors (Honeycomb),
  Alistair Cockburn, Eric Evans, the DORA report, PostHog.
- Quote verbatim in a blockquote rather than paraphrasing into your own voice.
- Follow the quote with your own experience: *"In my experience, tools such as
  Datadog, Sentry and Metabase have proven to be invaluable for this purpose."*
- Keep the source's prescriptive register attached to the source. Your own claims
  go back to first person.

## Evidence

Every claim needs a specific incident behind it, with numbers where they exist.

- Real figures over adjectives: 40% of actions completed within 7 days, a goal of
  70%, a first iteration landing near 50%.
- Name the tools actually used — Datadog, Metabase, Miro, feature flags — rather
  than "monitoring" and "collaboration tools".
- Include the case that went against you. The user-research example where removing
  the button would have made things worse is doing more work than any example
  where the team was right.

## Code examples

- Say up front that they are simplified: *"The code examples are simplified and
  adapted for illustrative purposes and intentionally hide the details of a
  specific production system."*
- Use `# code omitted for brevity` rather than inventing filler.
- Show the before as well as the after. The bloated test with three mocks makes
  the argument that the clean one cannot make alone.
- Name the trade-off in the choice: *"Python ABCs don't exactly equate to
  interfaces in strongly-typed languages... some would argue that Python's
  duck-typing makes this class unnecessary. In our case, I valued..."*

## Closing thoughts

Where the piece recommends an approach, end by naming the alternatives and the
limits of your recommendation: *"There are a multitude of ways we could have
tackled this, each with their own pros and cons. I like to apply pragmatism when
making these choices rather than advocating for a one size fits all solution."*

Alternatively, close by turning the lesson into questions you will ask next time,
rather than instructions for the reader.
