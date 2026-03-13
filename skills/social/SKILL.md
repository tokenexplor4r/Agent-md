---
name: social
description: Public communication for Tokenized Agents. Posting to X, announcing buybacks, sharing revenue stats, engaging with the community. Uses the identity skill for voice consistency.
metadata:
  author: Agent.md
  version: "1.0"
---

# Social

This skill handles the agent's public presence. Posting to X, engaging with the community, announcing buybacks, sharing revenue stats. The agent's social output should match its identity and voice defined in the identity skill.

## Configuration

```env
# X API credentials
X_API_KEY=<your-api-key>
X_API_SECRET=<your-api-secret>
X_ACCESS_TOKEN=<your-access-token>
X_ACCESS_SECRET=<your-access-secret>

# Agent's X handle (for self-reference)
AGENT_X_HANDLE=@YourAgentHandle
```

## Post Categories

The agent should have predefined post types. Each category has a template and a trigger condition.

### Buyback Announcement

Triggered when the analytics skill detects a buyback event.

```markdown
Template:
[amount] [currency] in revenue converted to $[TOKEN] buyback and burn.
[tokens_burned] tokens removed from supply.
Total burned to date: [total_burned]
```

### Revenue Milestone

Triggered when cumulative revenue crosses a threshold.

```markdown
Template:
[total_revenue] [currency] earned to date.
[buyback_portion] allocated to buybacks.
[tokens_burned] tokens burned so far.
```

### Service Announcement

Manual or scheduled. Announces what the agent offers.

```markdown
Template:
[service_description]
[price] per request. Pay in [currency]. Revenue feeds buybacks.
[link_to_service]
```

### Status Update

Periodic health check post.

```markdown
Template:
Agent status: [online/offline]
Uptime: [duration]
Requests served: [count]
Revenue: [amount] [currency]
Next buyback estimate: [time]
```

## Posting Logic

```typescript
interface Post {
  content: string;
  category: "buyback" | "milestone" | "announcement" | "status" | "engagement";
  scheduledAt?: number; // Unix timestamp for scheduled posts
}

async function composePost(category: string, data: Record<string, string>): Promise<string> {
  // Load the agent's VOICE.md for tone and style
  // Load the template for this category
  // Fill in the data
  // Return the composed post
}

async function publishPost(post: Post): Promise<void> {
  // Validate post length (280 characters for X)
  // Check rate limits (avoid spam)
  // Publish via X API
  // Log the post to analytics
}
```

## Engagement Rules

Define how the agent responds to mentions and replies.

```markdown
## Engagement Policy

**Reply to:** Direct questions about the agent's services, token info, or technical queries.
**Ignore:** Spam, off-topic mentions, low effort messages.
**Never:** Engage in price predictions, financial advice, or arguments.
**Tone:** Match VOICE.md at all times.
**Rate limit:** Maximum 20 replies per hour.
```

## Scheduled Posting

Set up a posting schedule so the agent maintains a consistent presence.

```typescript
interface PostSchedule {
  category: string;
  frequency: "hourly" | "daily" | "weekly";
  preferredTime?: string; // e.g. "14:00 UTC"
}

const schedule: PostSchedule[] = [
  { category: "status", frequency: "daily", preferredTime: "12:00 UTC" },
  { category: "milestone", frequency: "weekly", preferredTime: "18:00 UTC" },
];
```

## Content Guidelines

All posts should follow these rules:

Proper grammar. Capitalize I. No emojis unless the identity specifically includes them. No cringe. No generic AI language like "exciting update" or "we are thrilled to announce". Write in the agent's voice, not a corporate blog voice.

Never post private keys, wallet seeds, or internal configuration. Never make financial predictions or promises about token price. Never engage with obvious bait or FUD in a defensive way.

## Integration with Other Skills

**Identity** provides the VOICE.md that governs how every post reads.

**Analytics** triggers buyback announcements and revenue milestone posts with real data.

**Payments** provides revenue data for status updates.

**Onchain** monitors for buyback and burn events to announce.
