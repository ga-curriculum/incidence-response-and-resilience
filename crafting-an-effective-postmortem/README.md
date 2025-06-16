<h1>
  <span class="headline">Incident Response and Resilience</span>
  <span class="subhead">Crafting an Effective Postmortem</span>
</h1>

**Learning objective:** Write a clear, blameless postmortem using a structured template.

### Why blameless postmortems matter

Picture you are debugging an unexpected failure in a global e-commerce platform. The system is live, pressure is high, and everyone wants answers. In moments like these, it is natural to worry about “who is at fault.” However, resilient engineering teams focus less on blaming individuals and more on understanding how the system or processes allowed the incident to occur.

Blameless postmortems create an environment where everyone feels safe to share what actually happened—mistakes, gaps, or honest oversights. Teams that embrace this culture uncover deeper insights and make meaningful changes that prevent repeated failures.

### What does “blameless” look like in practice?

- Focus on _what_ happened and _why_ it happened, rather than _who_ made a mistake.
- Use neutral, factual language that avoids suggesting intent or incompetence.
- Assume everyone wanted to do the right thing given what they knew at the time.

### Example: Neutral vs. blameful language

**Blameful:**  
"Lin forgot to update the permissions, causing an outage."

**Blameless:**  
"The deployment was missing an updated permissions setting, resulting in reduced access."

## The anatomy of an effective postmortem

Crafting a postmortem is similar to telling the story of an incident—what happened, how the team responded, and, most importantly, what can be improved.

Most engineering teams use a clear, repeatable template.

## Review : Typical postmortem structure

1. **Executive summary**

   - A concise explanation of the incident, its impact, and the outcome.
   - Written so anyone (not just engineers) can understand.

2. **Timeline of events**

   - Time-stamped sequence, showing how the incident unfolded and was handled.

3. **Root cause analysis**

   - A factual breakdown of why the incident occurred, including both technical and process contributors.
   - Techniques like the "Five Whys" help uncover deeper causes beyond just the immediate trigger.

4. **Impact assessment**

   - Honest, measurable description of who or what was affected—number of users, support requests, business metrics, and more.

5. **Resolution and recovery**

   - Clear record of what actions restored normal service and how the team validated the fix.

6. **Action items and prevention**
   - Concrete, trackable steps to avoid similar incidents, each with an owner and timeline.

## Concrete steps: Writing a clear, actionable postmortem

The strength of the postmortem lies in clarity and neutrality.

### Key writing habits

- Prefer active voice while keeping descriptions about processes or systems, not individual people.
- Define or avoid specialized terms, especially if they are unique to your company or product.
- Be concise—include all facts relevant to understanding the chain of events without unnecessary details.

> 🏆 Before finalizing your postmortem, imagine another team needs to learn from it quickly. Would they be able to understand what happened and take action?

## Review: The “Five Whys” technique

Root cause analysis pushes you to dig deeper than the surface. The “Five Whys” is a simple, structured way to trace issues back to fundamental process or system gaps.

### Example: Uncovering a checkout bug

1. **Why did checkout fail?**  
   Because an error in the promotion feature caused exceptions.

2. **Why did the promotion feature error occur?**  
   It did not handle missing promotion data.

3. **Why was missing promotion data possible?**  
   The code allowed values to be undefined.

4. **Why did this code reach production?**  
   Automated tests did not cover the scenario.

5. **Why were tests incomplete?**  
   The team’s checklist did not include validation for optional data.

This series of questions moves from immediate symptoms to underlying causes, which are often fixable at the process or documentation level.

<div class="activity solo-exercise">
  <h2 class="title">Drafting a structured, blameless postmortem</h2>
  <span class="minutes">10 min</span>
</div>

This is your opportunity to practice what you have learned solo—moving from analysis to action.

Let's use a real-world scenario to practice writing a complete, blameless postmortem. Focus on using neutral language and making action items as clear and concrete as possible.

### Scenario: Search Outage at Retailly

**System:** `search-service` — powers the product search functionality on Retailly, a global online shopping platform.

**Incident:** A 25-minute outage that caused all user search queries to return empty results due to a misconfigured search filter.

**Time of incident:**

- **14:35 UTC:** Internal dashboards detected a sudden drop in search result counts and user click-throughs.
- **Duration:** Outage lasted from 14:35 to 15:00 UTC.

#### Background

- Retailly recently launched a new **seasonal search filter system** to improve relevance during campaigns (e.g., “Back to School”, “Winter Sale”).
- At **14:30 UTC**, a configuration change was deployed to Elasticsearch to enable a default "Back to School" filter that scoped results to products tagged with `campaign:back_to_school`.
- This change was made by updating a JSON config file in a shared config repo, which triggered an automated rollout via CI.

#### What Went Wrong

- The filter logic was intended to include products _with_ the \`back_to_school\` tag, but instead, it **excluded all products** due to a malformed condition.
- Specifically, the filter applied a strict match against a \`campaign\` field that **did not exist** in the majority of indexed products.
- As a result, search queries returned **zero results**, even for popular or generic keywords.

- The config was reviewed post-incident and contained this incorrect clause:

```json
"filter": {
  "term": {
    "campaign": "back_to_school"
  }
}
```

- This blocked results for all products not explicitly tagged with that field—roughly 96% of the catalog.

#### Monitoring & Alerts

- Search result counts dropped to near-zero in Grafana dashboards by 14:35 UTC.
- Conversion rates from search dropped by 82% within 5 minutes.
- Site error logs did **not show any exceptions**, which delayed detection—this was a **silent failure** in functionality, not stability.
- The on-call engineer was alerted by a **drop in search click-throughs**, not by system errors.

#### User Impact

- All users worldwide received **empty search results** from 14:35 to 15:00 UTC.
- Hundreds of users submitted site feedback through the on-page “Can’t find what I’m looking for” prompt.
- Support team received 40+ chat tickets within 20 minutes referencing “search not working.”
- Marketing campaigns linked to search-based landing pages saw a 65% drop in engagement.
- Internal sales estimates suggest the outage disrupted ~8% of hour-by-hour projected revenue during peak afternoon traffic.

#### Detection & Response

- At 14:35 UTC, an internal dashboard flagged a drop in search conversions.
- By 14:40, the on-call engineer confirmed the issue by reproducing empty search results manually.
- The team identified the new config as the only change between healthy and failing state.
- A rollback to the previous config version was initiated at 14:55 UTC and completed by 15:00 UTC.
- Normal results resumed immediately upon reload.

#### Why It Wasn’t Caught Earlier

- The configuration change passed schema validation but lacked **business logic validation**.
- There was **no automated test** to simulate search behavior with real product data.
- The config rollout bypassed staging, as it was treated as a "non-code" change.
- Monitoring thresholds were not tuned to detect silent feature breakage (e.g., empty results with 200 OK responses).

#### Available Artifacts

- Config file diffs in GitHub for `search-filters.json`
- Grafana dashboards for search result count, conversion rate, and search latency
- Slack logs from the `#search-team` and `#incident` channel
- Deployment logs from CI/CD for config rollout
- Reproduction test cases and rollback script confirmation logs

### Exercise

1. **Write a postmortem draft using these sections:**

   - Executive summary (2–3 sentences: what happened, impact, resolution)
   - Timeline of events (4–6 points, each time-stamped)
   - Root cause analysis (using the “Five Whys” or structured reasoning; focus on process or system, not people)
   - Impact assessment (who/what was affected, metrics, operational consequences)
   - Resolution and recovery (what actions were taken, how you validated the fix)
   - Action items and prevention (3 owner-assigned steps with goals and due dates)

## Key takeaways

- Blameless postmortems help teams learn and grow from incidents without fear.
- Structured templates and clear language make postmortems actionable and easy to share.
- Root cause analysis focuses on system and process improvements—the foundation for building resilient teams and products.
