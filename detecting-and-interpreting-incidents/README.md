<h1>
  <span class="headline">Incident Response and Resilience</span>
  <span class="subhead">Detecting and Interpreting Incidents</span>
</h1>

**Learning objective:** Identify signals of failure and interpret alerts or symptoms.

## How do developers really know when something is wrong?

Imagine working on a team that maintains a global online marketplace. When users experience problems—like slow checkouts, login failures, or missing inventory updates—these issues might start as invisible glitches, not obvious breakdowns. Your first clue is rarely a user message; instead, your system itself will begin to “talk” through data and alerts. Recognizing and interpreting these signals is where incident response truly begins.

> 💡 The ability to spot early warning signs is one of the most valuable skills for proactive engineers. By catching failures quickly, you can prevent minor glitches from becoming system-wide outages.

## The top three failure signals

Engineering teams typically rely on data patterns, not just user reports, to spot trouble.

Here are the three core signals you will encounter:

| **Symptom Type**        | **What It Indicates**                                                                                    |
| ----------------------- | -------------------------------------------------------------------------------------------------------- |
| **Error rates**         | Tracks failed requests or processes (e.g., HTTP 500s, DB errors). Spikes suggest something is broken.    |
| **Latency spikes**      | Measures how long requests take. High latency may point to overload, deadlocks, or integration failures. |
| **Resource exhaustion** | Observes CPU, memory, disk, or network usage. Saturation can trigger cascading failures across services. |

### Scenario 1: The overworked call center

Picture a busy multilingual customer service center.

Engineers can detect problems in three ways:

- Calls start dropping unexpectedly (errors).
- Callers wait several minutes for help instead of seconds (latency).
- The phone system warns it is running at its maximum line capacity (resource exhaustion).

Just as with software systems, each of these signals tells you where problems might start or where immediate attention is needed.

### Scenario 2: API error log

Suppose your team supports a payments API used by small businesses worldwide. Your log records show the following:

```
2024-06-04T12:02:10Z ERROR Route /checkout 500 Internal Server Error
2024-06-04T12:02:11Z ERROR Route /checkout 500 Internal Server Error
2024-06-04T12:02:12Z ERROR Route /checkout 500 Internal Server Error
```

If you usually see just a few errors each day, spotting multiple errors every second is a clear sign of an emerging incident. These alerts allow you to act before customers flood support with complaints.

## Separating real issues from “noise”

Modern systems generate an overwhelming number of alerts. Not every alert signals a crisis—some are brief or unimportant, while others indicate serious trouble. Being able to sort through them is essential.

### Key alert types in incident management

You will most often see alerts in three main categories:

1. **Performance alerts**: These fire when response times or request rates are out of bounds.
2. **Error alerts**: Triggered by an increased rate of failures—such as more HTTP 500s or failed background jobs.
3. **Capacity alerts**: Indicate that a specific resource (CPU, memory, disk, or bandwidth) is running low or almost exhausted.

### Example: Decoding different alert types

- _Performance alert_: "Checkout API latency above 2s for 10 minutes."
- _Error alert_: "500 error rate on payment API up 500% over baseline."
- _Capacity alert_: "Web server memory usage above 90% for 15 minutes."

Which ones require urgent action? That depends on impact and duration. Sustained high latency or error spikes on functions like payments or logins should get priority.

## Confirming true incidents

Alerts are your early warning. But just as a smoke alarm does not always mean there is a fire, you need more information before reacting. Logs and metrics help verify whether an incident is happening or if it is just a short-lived anomaly.

### Interpreting logs

Logs provide a timeline and context for system activity. Look out for:

- **Timestamps** to identify when issues began.
- **Status codes** (such as 500 for server errors).
- **Endpoints** and affected features.

### Example: Parsing a web server log

```
[2024-06-04T12:11:13Z] "GET /api/orders" 500 0.233
[2024-06-04T12:11:14Z] "GET /api/orders" 500 0.245
[2024-06-04T12:11:14Z] "GET /api/orders" 200 0.122
[2024-06-04T12:11:15Z] "GET /api/orders" 500 0.238
```

A cluster of 500 status responses means something substantial has failed. If these errors disappear after a few seconds, it may be less urgent; if they continue, you are likely facing a real incident.

### Understanding metrics

Metrics show trends and patterns over time—such as error rates, average response time, or memory usage.

- **Spikes and sudden jumps**: Often signal a deployment or third-party system issue.
- **Gradual increases**: Could hint at slow-developing problems, like a memory leak.

#### Example: Spotting a false positive

You notice a graph where memory usage jumps from 60% to 85% for one minute, then quickly drops. Checking logs, no errors occurred during that time. This suggests a transient background task—not an incident that needs immediate action.

> 💡 By cross-referencing logs and metrics, you build confidence in your assessment—helping to avoid “alert fatigue” from acting on every signal.

## Prioritizing and responding to signals

When an alert arrives, use these guiding questions to decide what to do next:

- Is the signal ongoing, or just a quick blip?
- Does it affect critical functionality (ex: checkout, user login), or non-essential features?
- Are there multiple signals pointing to the same issue?
- Are users reporting problems, or is this only visible internally?

> The strongest evidence of a critical incident comes from multiple independent signals aligning—such as high latency _and_ a spike in errors on a key API.

<div class="activity knowledge-check">
  <h2 class="title">Consider this situation...</h2>
  <span class="minutes">2 min</span>
</div>

- Your team receives simultaneous alerts—a memory usage warning on one server, and a large increase in login failures.
- How would your response differ compared to receiving just one of these alerts?
- What steps would you prioritize, and who would you notify first?

_(Pause and discuss with a peer, or write down your approach. Think about how overlapping signals change your investigation.)_

<div class="activity partner-exercise">
  <h2 class="title">Triage and interpret sample incident scenarios</h2>
  <span class="minutes">10 min</span>
</div>

This discussion-based activity gives you a chance to apply your understanding of incident signals, distinguish between true and false positives, assign severity, and consider appropriate response strategies—just like an on-call engineer would.

1. Pair up with a partner.

2. Together, read each alert scenario and associated data.

3. For each scenario, discuss and answer:

   - Which signal(s) are present?
     (ex: Error rate, latency, resource usage, or a combination)
   - Does this look like a real incident or a false positive? Why?
   - What severity level would you assign—high, medium, or low? Justify your choice.
   - What would be your first response as the on-call engineer?

### 🟠 Scenario 1: Login Failures Surge

**Alert:**  
`500 error rate on /api/login up 600% over past 5 minutes.`

**Log snippet:**

```
[2024-06-04T09:02:10Z] "POST /api/login" 500 0.112
[2024-06-04T09:02:11Z] "POST /api/login" 500 0.099
[2024-06-04T09:02:12Z] "POST /api/login" 500 0.102
```

**Additional context:**

- Users in a chat are complaining they can’t log in.
- The marketing team deployed an updated login form 20 minutes ago.
- No database or backend deployments today.

### 🟡 Scenario 2: CPU Spikes on Web Servers

**Alert:**  
`CPU usage above 95% on webserver cluster for 15 seconds.`

**Metrics:**

- CPU usage spiked across all 4 nodes at once, then dropped to ~60%.
- No increase in HTTP 5xx errors.
- Latency remained stable around 150ms.

**Additional context:**

- A data team cron job runs at the top of every hour.
- No complaints from users.

### 🔴 Scenario 3: Sluggish Checkout Flow

**Alert:**  
`Average checkout latency above 8s for 10 minutes.`

**Logs:**

```
[2024-06-04T13:51:01Z] "POST /checkout" 200 8.943
[2024-06-04T13:51:02Z] "POST /checkout" 200 9.022
[2024-06-04T13:51:03Z] "POST /checkout" 200 8.761
```

**Additional context:**

- Latency spike started after a new recommendation engine rollout.
- Users are completing purchases, but customer support tickets mention “long checkout loading.”
- No failed requests, but front-end analytics show increased drop-off at checkout.
