<h1>
  <span class="headline">Incident Response and Resilience</span>
  <span class="subhead">Instructor Guide</span>
</h1>

## Introduction to Incident Management

### Delivery notes

- Encourage learners to relate the four phases of incident management to their own experiences. If few have production exposure, invite them to share experiences from smaller technical or personal projects (like organizing a community event or managing a group assignment).
- When reviewing the interactive activity, circulate or observe to ensure learners are applying each phase distinctly. Offer probing questions for learners who conflate phases (for example: "How would you know the problem was really fixed?" or "What might you do while waiting for a full solution?").
- For global contexts, stress that incidents and team setups vary widely—avoid assuming specific tools or processes. Highlight the universal principles (communication, collaboration, learning from failure).
- Use the provided assets (timeline diagrams, annotated dashboards) to visualize complex concepts and make abstract processes more concrete.

### Activity sample solution

| **Phase**      | **Sample Instructor Notes**                                                                                                                                                                                                                                                                                                                                 |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Detection**  | - **First signals:** Customer support complaints and a chat ping from marketing. <br> - **Missed detection opportunity:** No alert from monitoring tools; monitoring wasn't configured for promo usage drops or error rates. <br> - **Takeaway:** Instrument discount API metrics and alert on sudden drops in usage or spikes in "invalid code" responses. |
| **Response**   | - **On-call engineer** investigates logs and sees recent deployment modified expiration logic. <br> - Loops in dev who made the change + infra team to discuss rollback or patch options. <br> - **Slack thread created** in `#incident-discounts-0401` for coordination.                                                                                   |
| **Mitigation** | - Temporary fix: hot-patch the expiration logic to compare UTC correctly or extend the code's expiration.<br> - Add a warning banner at checkout: _"Promo code issue detected—discounts will be honored."_ <br> - Roll back deployment if hotfix isn't fast.<br> - Coordinate with support and social media teams.                                          |
| **Recovery**   | - Validate fix by testing promo code across timezones in staging and production.<br> - Monitor promo code usage to confirm it rebounds.<br> - Schedule postmortem. <br> - Long-term fix: enforce UTC everywhere; add unit tests for promo expiration logic.                                                                                                 |

### Discussion prompt sample answer

A common challenge is the _Mitigation_ phase—acting under pressure when the root cause is unknown. Building skill in making temporary fixes and communicating transparently, even before having all the answers, can help. Practicing scenarios and learning your monitoring tools increases confidence during this stage.

---

## Detecting and Interpreting Incidents

## Instructor guide

### Delivery notes

- Start by anchoring the abstract nature of failure signals to relatable, real-world examples. Use the call center analogy to help learners see the parallels with non-technical crisis management situations.
- Encourage learners to work individually or in small, geographically diverse groups for the knowledge check and main activity. This enables comparison across contexts and helps learners see that technical signals and responses transcend company, region, or technology stack.
- Emphasize the importance of using both logs and metrics, not just reacting to alerts. Cross-referencing data helps guard against “alert fatigue.”
- Use the visual assets—metrics trend lines, error log snippets, and analogy diagrams—to cue discussion and ensure everyone can interpret key signals, even if they are new to production monitoring.
- For knowledge check discussions, prompt learners to differentiate how they would respond to multiple signals versus a single warning, especially in prioritizing communication and investigation.
- When reviewing the activity, ask follow-ups such as:
  - “If you saw these logs but no user complaints, would you act differently?”
  - “How do you know when a spike in resource usage becomes a real problem?”
- Focus group conversation on how real-world triage often relies on “weight of evidence” from various sources.

### Activity sample solution

**Scenario 1:**

- Signals: Error spike (500s) on the login API.
- True incident? Most likely, given sustained high error rate on a critical function.
- Severity: High. Login failure affects all users’ ability to access the system.
- First action: Begin investigation immediately. Check for recent changes or releases affecting authentication, notify the team, and communicate possible login issues to stakeholders.

**Scenario 2:**

- Signals: Resource (CPU) exhaustion.
- True incident? Unlikely—short spike, no reported errors or performance issues.
- Severity: Low. Monitor for recurrence; may be a heavy background task.
- First action: No immediate intervention needed. Set a watch for patterns.

**Scenario 3:**

- Signals: Latency spike (checkout responses above target); no error increase.
- True incident? Yes—sustained latency above acceptable threshold, even without errors.
- Severity: Medium to high, depending on impact to users and business.
- First action: Begin root cause analysis. Investigate upstream dependencies (database, third-party services), communicate delays to team, consider temporary mitigation if users cannot complete purchases.

---

## Incident Response Workflow

## Sample Activity solution: OpenPath Login Incident

### 1. Detection

- **Who received the alert:** On-call engineer via PagerDuty at 10:00 UTC
- **What triggered it:** Alert on `http_500_total` for `/api/login` — 700% increase in error rate in 10 minutes

### 2. Triage

- **Severity:** High
- **Systems affected:** `/api/login` endpoint, affecting all regions
- **Users affected:** Instructors and students in Singapore, Bahrain, UK
- **Business impact:**
  - Blocking access during global program launch
  - Real-time user complaints and social media visibility

### 3. Assemble & Assign

- **Incident Commander (IC):** [Student Name A]
- **Scribe:** [Student Name B]
- **Responders:** [Students C, D]
- **Channels used:** Slack incident channel, shared Google Doc, GitHub for code review

### 4. Investigation

- **Logs:**

```
ValueError: time data '2025-03-31 23:59:59' does not match format '%Y-%m-%dT%H:%M:%S%z'
```

Indicates a datetime parsing issue — likely due to region-dependent logic in the new "Save Last Login Location" feature.

- **Grafana metrics:**
- Login success rate dropped to 41%
- P95 latency increased from 300ms to 5s
- No CPU or memory issues on pods

- **GitHub:** Deployment v2.3.5 pushed at 09:50 UTC, includes updated login logic with timezone parsing

- **Deployment strategy:** Blue/Green — rollback possible

### 5. Mitigation

- **Action taken:** Triggered rollback to previous stable release (v2.3.4) at 10:15 UTC
- **Reason:** Fastest safe option while root cause is investigated; avoids introducing new changes under pressure

### 6. Validate

- **Prometheus confirms:**
- Error rate decreasing within 2–3 minutes of rollback
- Success rate climbing back above 90% by 10:25 UTC
- **Manual test:** Successful login from test accounts in Singapore and UK
- **Support channel:** Reports of user access being restored
- **IC Update to stakeholders:** ETA shared as ~10–15 minutes from rollback trigger

### 7. Review

**Follow-ups:**

- Add test coverage for datetime parsing with region-specific input
- Rework login feature to handle datetime without assuming format or offset
- Coordinate with marketing to monitor residual campaign impact
- Review alerting thresholds for login latency and error % per region
- Run postmortem and document findings in shared incident report doc

> 💡 Final IC Note: "Fast rollback and coordinated response helped us recover quickly. Key lesson: datetime assumptions across timezones can break core features during global usage windows."

## The Anatomy of a Postmortem

### Suggested lesson delivery

- Begin with a group discussion: “Who here has seen or written a postmortem before? What made it helpful — or difficult to follow?”
- Use the roof leak analogy or sports/game replay to anchor the value of root cause analysis and structured improvement.
- Highlight real-world, cross-industry examples to reinforce the universal nature of postmortem practices.
- Guide the main activity by reviewing the example incident out loud, modeling a think-aloud process as you turn scenario data into a postmortem summary.
- Pair or group review: Encourage learners to give feedback not only on technical details, but on clarity for non-technical readers and practical action items.
- For discussion, prompt reflection on the tradeoffs between focusing on technical fixes, process changes, or communication/culture improvements.

### Knowledge check answers (Spot the strengths)

- The root cause in the scenario reaches beyond the null bug — it identifies a missing check, a gap in test coverage, and an incomplete code review checklist.
- Action items are practical (null checks, live sandbox testing) and assigned to specific owners.
- A new team member could follow the timeline and understand what led to the incident and what is changing.

### Activity sample solution

### Activity sample solution

**Executive summary:**  
At 10:05 UTC, the checkout page of our online retail site became unavailable following a code deployment. Approximately 12% of users attempting checkout could not complete their purchase until the deployment was rolled back at 10:30 UTC.

**Timeline of events:**

- 10:05: Monitoring system reports spike in HTTP 500 errors on checkout
- 10:07: On-call engineer investigates, identifies recent deployment
- 10:15: Issue traced to new promotion feature
- 10:20: Decision made to roll back deployment
- 10:30: Rollback complete; normal checkout service restored
- 10:35: Users notified; support tickets reviewed

**Root cause analysis:**

- The checkout page failed due to unhandled exceptions in the newly released promotion feature.
- The promotion code allowed missing data, which was not anticipated.
- Automated tests did not include scenarios for missing promotion data.
- Test coverage was incomplete for this edge case because the code review checklist did not specify data validation for optional fields.

**Impact assessment:**

- Checkout unavailable for 30 minutes
- ~12% of transactions failed
- Multiple customer support tickets logged

**Resolution and recovery:**

- Deployment was rolled back to previous stable version
- System metrics monitored to confirm error rates returned to baseline
- Affected users notified

**Action items and prevention:**

- Add test cases for undefined or missing data in promotion feature (Owner: Priya, due: YYYY-MM-DD)
- Update code review checklist to include edge case validation for optional fields (Owner: Adil, due: YYYY-MM-DD)
- Schedule peer training session on writing blameless postmortems (Owner: Team Lead, due: YYYY-MM-DD)

### Facilitating the discussion

- Encourage learners to recognize that technical, process, and communication improvements all contribute to resilience.
- Highlight that the greatest long-term benefits usually come from combining these approaches based on patterns seen across postmortems.
- Invite students to share experiences across cultural and technical backgrounds to underline global relevance.

---

## Crafting an Effective Postmortem

### Delivery notes

- Anchor the lesson by connecting incident postmortems to concepts already learned, such as the four incident management phases and the structured response workflow.
- Use the analogies (team project review, ship crew responding to a leak) to make the purpose of blameless postmortems tangible and universal.
- Use assets thoughtfully: employ infographics and flowcharts to help make abstract structures more visible, especially for newer engineers.
- Encourage learners to approach the practice activity from their own experience when possible, or draw from the provided e-commerce scenario.
- When facilitating pair and group review, emphasize actionable feedback: “Does this wording support a blameless culture? Are the action items practical and owner-assigned?”
- For the discussion prompt, guide learners toward practical solutions: shared templates, documenting shared norms, and leadership modeling blameless language.

### 📄 Postmortem Solution: Retailly Search Outage

#### Executive Summary

On August 28, 2025, at 14:35 UTC, a misconfigured search filter caused all product search queries on Retailly’s site to return empty results. The issue stemmed from a malformed promotion config deployed at 14:30 UTC. The incident lasted 25 minutes and was resolved by rolling back the configuration at 15:00 UTC. Approximately 8% of projected revenue for the hour was impacted.

#### Timeline of Events

- **14:30 UTC** – Config change deployed to enable seasonal "Back to School" filter
- **14:35 UTC** – Monitoring alert: drop in search result counts and click-through rates
- **14:40 UTC** – On-call engineer confirms search results are empty
- **14:45 UTC** – Root cause traced to recently deployed filter configuration
- **14:55 UTC** – Rollback initiated
- **15:00 UTC** – Config rollback completed; search results return to normal

#### Root Cause Analysis

- **Why were results empty?** A `term` filter matched on a non-existent field (`campaign`), filtering out nearly all products.
- **Why was this field missing?** Only ~4% of products had the new `campaign` tag applied.
- **Why wasn’t this caught earlier?** The config change passed schema checks but lacked logic-level validation.
- **Why wasn’t staging used?** The change was considered “non-code” and didn’t follow the standard pre-prod deployment process.
- **Why did monitoring delay detection?** No alerts were configured for sharp drops in successful search queries.

#### Impact Assessment

- 100% of users experienced failed or empty search results for 25 minutes
- 87 support messages and 40 chat tickets submitted
- Estimated 8% revenue dip during the hour
- 65% drop in marketing campaign conversions tied to search-based landing pages

#### Resolution and Recovery

- Engineers identified the config file as the only delta between healthy and failing states
- A manual rollback was performed using CI/CD tooling
- Recovery was verified via test searches and dashboard recovery of search result count and click-through rates

#### Action Items and Prevention

| Action Item                                                                          | Owner       | Goal                                                | Due Date |
| ------------------------------------------------------------------------------------ | ----------- | --------------------------------------------------- | -------- |
| Add logic-based tests to validate search filter behavior against sample product data | QA Lead     | Prevent regressions from malformed filters          | Sept 10  |
| Require staging rollout for all config changes impacting search behavior             | Eng Manager | Ensure staging validation across “non-code” updates | Sept 3   |
| Add alerting for zero-result spikes and major search traffic drops                   | DevOps      | Detect silent search failures sooner                | Sept 5   |

---

🏗️ **Under Construction**

We are constantly working to improve our resources for instructors and students.

**Have something to contribute to this Instructor Guide?** [Let us know](https://ga-curriculum.github.io/universal-resources-internal/module-feedback.html).
