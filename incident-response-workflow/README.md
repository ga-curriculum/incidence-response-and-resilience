<h1>
  <span class="headline">Incident Response and Resilience</span>
  <span class="subhead">Incident Response Workflow</span>
</h1>

**Learning objective:**  
Follow a structured incident response process from detection to recovery, and practice applying it through a guided team simulation.

## The Eight-Step Workflow

This builds on the 4-phase model by expanding _Response_ and _Recovery_ into actionable steps.

| Phase      | Detailed Step             | Key Focus                                |
| ---------- | ------------------------- | ---------------------------------------- |
| Detection  | 1. Detection & Alerting   | Monitoring tools, user reports           |
| Response   | 2. Acknowledge & Triage   | Assess severity & affected systems       |
|            | 3. Assemble Response Team | Assign IC, Scribe, Responders            |
|            | 4. Investigate & Diagnose | Review logs, metrics, recent deploys     |
|            | 5. Escalate & Communicate | Update stakeholders clearly              |
| Mitigation | 6. Mitigate Impact        | Rollback, disable features, etc.         |
| Recovery   | 7. Validate Recovery      | Confirm resolution, stability            |
|            | 8. Post-Incident Review   | Identify improvements & assign follow-up |

## Typical Roles in a Response Team

- **Incident Commander (IC)**: Owns decisions, delegates tasks, manages communication
- **Scribe**: Logs all actions, decisions, timestamps for postmortem
- **Responders**: Investigate, test, mitigate, escalate

<div class="activity group-exercise">
  <h2 class="title">Respond to a Live Scenario</h2>
  <span class="minutes">20 min</span>
</div>

Simulate a real-world incident from alert to resolution, practicing structured response and communication.

### Setup

- Form small groups (3–4 students)
- Assign roles: **IC**, **Scribe**, **Responders**
- Prepare a shared doc or worksheet to record events

### Full Incident Scenario

You’re the **on-call engineer** for **OpenPath**, an international online education platform used by learners and instructors across multiple time zones.

### Time of Alert

At **10:00 UTC**, you receive a high-priority alert from your monitoring tool:

> **“500 error rate on `/api/login` increased by 700% in the last 10 minutes.”**

### Background Context

- A new feature—**“Save Last Login Location”**—was **deployed at 09:50 UTC** as part of release v2.3.5.
- The deployment modified the login service logic to log the user's last login time and inferred region, based on timezone.
- Logs show new errors related to **datetime parsing** and **timezone mismatches**.
- Support has started receiving user complaints from Singapore, Bahrain, and the UK.
- Several **social media posts** are now trending, tagging your company with screenshots of failed login attempts.

### Technical Clues

- Application logs are showing:
  ```
  ValueError: time data '2025-03-31 23:59:59' does not match format '%Y-%m-%dT%H:%M:%S%z'
  ```
- Prometheus metrics show a spike in `http_500_total` and increased latency for `/api/login`.
- Your Grafana dashboard for user auth displays:
  - Request count: normal
  - Success rate: dropped from 99.2% to 41%
  - P95 login latency: spiked from 300ms to 5s
- CPU and memory usage on login pods appear normal.
- Rollout was performed using a blue/green deployment strategy, and **rollback is available** but must be triggered manually.

### Business Context

- This week is the launch of a **global certification prep program** and traffic is up 30%.
- The **marketing team** has a paid ad campaign driving users to register and log in this morning.
- Your **customer success manager** pings the incident channel:
  > “We have instructors live on webinars and students who can’t access their dashboards. ETA?”

### Team & Tools

- You have access to:
  - Full Prometheus + Grafana dashboards
  - Deployment logs and GitHub commit history
  - A rollback script for release v2.3.5
  - System health status pages
- Slack and PagerDuty are your main communication tools.
- The SRE team is distributed across 3 time zones, and the database SME is on standby if needed.

### Exercise

As a group, step through the full incident response workflow:

1. **Detection**

   - Who received the alert? What triggered it?

2. **Triage**

   - Confirm severity: what systems/users are affected?

3. **Assemble & Assign**

   - Clarify who is IC, Scribe, Responders

4. **Investigate**

   - What do logs and metrics show? Is the deployment suspicious?

5. **Mitigate**

   - What’s the fastest safe action to reduce impact?

6. **Validate**

   - Is login working again? Are errors dropping?

7. **Review**
   - What follow-up actions should be assigned?

### Reflect

- What worked well in your process?
- Where was communication strong or weak?
- How would you improve your team’s readiness?
