---
name: identity
description: Define a Tokenized Agent's personality, voice, knowledge domains, and operational context. Use when configuring who the agent is, how it communicates, and what it knows. Templates included for quick setup.
metadata:
  author: Agent.md
  version: "1.0"
---

# Identity

This skill defines who the agent is. Without identity, an agent is a blank function that accepts payments and returns outputs. With identity, it has a voice, a perspective, a memory, and a reason to exist.

Identity determines how the agent responds to users, what tone it uses, what it knows, what it refuses to do, and how it represents itself publicly. Every other skill references the identity to stay consistent.

## Templates

Agent.md provides three identity templates. Copy them into your agent's directory and fill them in.

```bash
cp skills/identity/templates/AGENT.template.md my-agent/AGENT.md
cp skills/identity/templates/VOICE.template.md my-agent/VOICE.md
cp skills/identity/templates/CONTEXT.template.md my-agent/CONTEXT.md
```

### AGENT.md

The core identity file. Contains the agent's name, description, purpose, knowledge domains, personality traits, and behavioral boundaries.

### VOICE.md

How the agent communicates. Tone, vocabulary, sentence structure, things it always says, things it never says. This file gets loaded into every interaction so the agent maintains a consistent voice.

### CONTEXT.md

What the agent knows about its own operational environment. Its token, its community, its revenue model, its buyback configuration, its creator. This is not personality. This is facts the agent needs to function.

## How to Use

Load the identity files into your agent's context at the start of every session. The agent reads them before processing any user request.

```
You are an AI agent. Read the following files and follow them exactly:
- AGENT.md defines who you are
- VOICE.md defines how you communicate
- CONTEXT.md defines what you know about your token and operations

Then process the user's request.
```

## Integration with Other Skills

The identity skill is referenced by every other skill:

**Services** uses the identity to frame service descriptions and responses in the agent's voice.

**Social** uses the voice template to write tweets and announcements that sound like the agent.

**Payments** uses the context to know which token mint to charge against and what currency to accept.

**Trading** uses personality traits to determine risk appetite and communication style around trades.

## Building Identity from Existing Data

If the agent is based on an existing person or project, you can build the identity from their data. Feed the agent existing tweets, writing samples, project documentation, and let it generate the AGENT.md and VOICE.md files.

```
Read the following tweets and writing samples. Extract the voice, personality, and communication patterns. Generate an AGENT.md and VOICE.md file that captures this identity.

[paste tweets, writing, project docs]
```

## Updating Identity

Identity is not static. As the project evolves, update the templates. The CONTEXT.md should be updated whenever the token's status changes (graduation, new services, revenue milestones). The VOICE.md might evolve as the community develops its own language. The AGENT.md is the most stable but even core personality can shift.

Version your identity files. Keep a changelog. The agent should know when its own identity was last updated.
