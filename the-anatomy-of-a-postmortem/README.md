<h1>
  <span class="headline">Incident Response and Resilience</span>
  <span class="subhead">The Anatomy of a Postmortem</span>
</h1>

**Learning objective:** Understand the purpose and structure of a postmortem document.

## Why postmortems matter for your team and your users

Production incidents, no matter how well a team prepares, are a universal part of software development. What truly sets resilient teams apart is not just how they respond in the moment, but how they turn setbacks into learning and lasting improvement.

A _postmortem_ is an essential tool for this process. It is a structured, written review that — rather than focusing on individual mistakes — explores deeply what happened, why, and how to prevent or respond better next time.

> 💡 Think of a postmortem like a “game replay” in sports or a review after a group project at university. Instead of reviewing only what went wrong, you are looking for patterns, points for improvement, and opportunities for the entire group to learn.

Well-crafted postmortems transform failures into practical steps for greater system reliability and support a stronger, more effective engineering culture.

## What is a postmortem?

A _postmortem_ (sometimes known as a post-incident review) is a formal document created after an incident is fully resolved. Unlike a quick chat or informal summary, a postmortem aims to provide:

- A clear record of what happened, when, and how.
- An analysis of _why_ the incident occurred, going beyond surface-level issues.
- A list of concrete steps to improve systems or processes, making repeat incidents less likely.

Rather than focusing on blame, postmortems focus on systems, workflows, and shared team learnings.

### Key questions every postmortem should answer

- What happened?
- Why did it happen?
- How did we respond as a team?
- What can we improve or change to prevent or manage similar events in the future?

> 📚 A _postmortem_ is a structured review document that breaks down a technical or process failure to uncover root causes and inform meaningful improvement.

## Why do postmortems make systems (and teams) better?

### For engineering teams

- **Support team learning:** By making every incident a lesson, teams uncover unknown risks or process gaps.
- **Drive change:** Postmortems connect failures to needed fixes, whether new tests, workflow updates, or improved monitoring.
- **Spread knowledge:** Sharing postmortems helps your team — and sometimes the broader organization — avoid repeating mistakes.
- **Promote a blameless culture:** Focusing on system issues, not individuals, encourages honest discussion and deeper analysis.

> 🏆 Engineering teams that treat postmortems as opportunities for shared learning, rather than blame, tend to uncover deeper issues and generate more effective solutions.

### For stakeholders and users

- **Build trust:** Transparent postmortems show users and partners your team is serious about learning from setbacks.
- **Demonstrate accountability:** Regularly reviewing and improving after incidents signals reliability and commitment.

## Creating a great postmortem

Most organizations use a standard template to make postmortems thorough and easy to follow. Here are the building blocks you are likely to see:

### 1. Executive summary

A brief overview in accessible language. It explains what happened, when, and the user or business impact.

**Example:**

> On June 3, our checkout API was returning errors for about 30 minutes. The root cause was a faulty database migration. After rolling back, normal service resumed, and about 10% of customers were unable to complete their purchase during the disruption.

### 2. Timeline of events

A sequence of key moments, showing how the incident progressed from start to finish. Timelines help teams spot where response or communication can improve.

**Example:**

- 09:35: Error alerts triggered for checkout API.
- 09:40: Team assembled, investigation begins.
- 09:47: Cause identified — recent deployment.
- 09:50: Rollback started.
- 10:05: System stable, users notified.

### 3. Root cause analysis

Going beyond _what_ failed to _why_ it failed. This section digs into both technical and human factors, often using structured tools like the "Five Whys" technique.

**Example:**

> - **_Why_** did the checkout API return errors?  
>   Because a database change broke a table.
> - **_Why_** did the change break the table?  
>   The new code assumed a column existed, but it did not.
> - **_Why_** was the column missing?  
>   The migration script skipped a step.
> - **_Why_** did it skip?  
>   The deployment tool lacked a check for dependent steps.
> - **_Why_** was the check missing?  
>   The workflow was new and documentation was incomplete.

### 4. Impact assessment

Describes exactly who or what was affected. This could include:

- Number or percentage of users impacted.
- Financial loss or missed business opportunities.
- Reputational effects.

### 5. Resolution and recovery

Clearly lists the steps that returned the system to normal.

### 6. Action items and prevention

Concrete, accountable actions, each with an owner and timeline. Actions can address code changes, process updates, improved monitoring, or more team training.

**Examples:**

- Add code test for database changes (Owner: Alex, due June 10)
- Update deployment documentation (Owner: Jamie, due June 15)
- Add alert for schema mismatches (Owner: Priya, due June 12)

> 💡 Assigning ownership and deadlines makes it much more likely that improvements actually happen, not just get discussed.

## Postmortems in the real world

### Example: Streaming media platform incident

A streaming media company faces a surge in errors during a major sports event. Their postmortem summary might look like this:

**Executive summary:**

- At 18:00 UTC, 20% of users could not access live streams for 20 minutes. Cause: an untested load spike during a popular match.

**Timeline:**

- 17:55: Traffic surge detected.
- 17:56: Automated scaling failed to provision resources.
- 18:10: Manual scale-up performed.
- 18:20: Service recovered.

**Root cause:**

- Load balancer thresholds not aligned with event capacity. Scaling automation lacked recent updates.

**Impact:**

- About 1 in 5 users lost access temporarily. Event sponsors voiced concerns.

**Resolution:**

- Added manual capacity, patched scripts.

**Action items:**

- Review and update scaling policies before major events. Test automation under real user loads.

> 😎 Even large organizations face high-pressure incidents. The difference is in how quickly they learn and adapt their systems and processes afterward.

## Postmortems as part of team learning and system improvement

### Creating institutional memory

Every postmortem adds to your team’s knowledge base. Future teammates can learn from past incidents, recognize patterns, and prevent repeat issues.

### Encouraging psychological safety

Blameless postmortems help everyone feel comfortable sharing what really happened — including missed signals or mistakes — leading to deeper understanding and more robust fixes.

### Continuous system resilience

Action items lead to improvements: automated alerts, stronger documentation, improved test coverage, and better communication. Over time, postmortems make teams and systems more resilient.

<div class="activity guided-walkthrough">
  <h2 class="title">Simulate writing a postmortem summary</h2>
  <span class="minutes">5 min</span>
</div>

Let's Practice summarizing a real-world incident to reinforce understanding of postmortem structure and objectives.

### Scenario: Checkout Outage at Retailly

**System:** `checkout-service` — part of Retailly’s online shopping platform, responsible for handling final order submission and payment initiation.

**Incident:** A 30-minute outage that prevented customers from completing their purchases after a production deployment.

**Time of incident:**

- **10:05 UTC:** Alert triggered by a spike in HTTP 500 errors on the `/checkout/submit` endpoint.
- **Duration:** Outage lasted from 10:05 to 10:30 UTC.

**New release deployed:**

- Version `v1.48.0` was rolled out at **10:00 UTC**.
- The release included a **new “Buy More, Save More” promotion feature** designed to apply tiered discounts based on cart quantity.
- The feature queries a backend `promotions-service` for discount rules.

**Observed errors:**

- Logs showed `TypeError: 'NoneType' object is not subscriptable` in the discount calculation step.
- Errors occurred when the promotion service returned null data for edge-case carts (e.g. empty categories or untagged items).
- Checkout-service did not check for `null` before accessing the response.

**Monitoring & metrics:**

- HTTP 500 error rate rose by **450%**, primarily on checkout submissions.
- Grafana showed a sharp drop in successful checkouts and conversion rates.
- CPU and memory usage remained normal; no signs of infrastructure strain.

**User impact:**

- 12% of all checkout attempts during the incident window failed.
- ~1,500 users affected across global regions.
- 87 support tickets were submitted.
- Multiple complaints were posted to social media, including screenshots of broken checkout pages.

**Business context:**

- The outage occurred during a **regional flash sale** in Bahrain and Singapore.
- This campaign was expected to drive high traffic and revenue during a limited promotion window.
- The marketing team had issued discount codes tied to this new feature.
- Sales targets for the hour were missed by 18% due to failed transactions.

**Detection & response:**

- Alert received via PagerDuty by the on-call engineer at 10:05 UTC.
- Engineers investigated logs and confirmed the issue was linked to the newly deployed promotion logic.
- Rollback decision made at 10:25 UTC and executed at 10:28 UTC.
- Error rate returned to normal by 10:30 UTC.

**Why it wasn't caught earlier:**

- Unit tests were present but did not include `None` or missing-data scenarios.
- No integration test between checkout-service and promotions-service existed.
- Staging environment had a simplified dataset and didn’t reflect live edge cases.

### Together

1. **We'll write a postmortem summary document** (5–8 sentences).  
   Include these sections:

   - Executive summary (what happened and impact)
   - 2–3 step timeline
   - Root cause
   - Resolution and key action items

> 💡 The most useful postmortems are clear, concise, and actionable — and can be understood by anyone who joins your team.
