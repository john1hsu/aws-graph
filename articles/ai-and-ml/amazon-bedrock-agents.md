---
id: kt:amazon-bedrock-agents
type: Feature
title: Amazon Bedrock Agents Classic
aliases: [Amazon Bedrock Agents, Bedrock Agents, Agents for Amazon Bedrock, Bedrock Agents Classic]
domain: ai-and-ml
summary: >-
  The original fully managed, configuration-driven agent capability of Amazon Bedrock (launched
  November 2023), which orchestrates a foundation model, instructions, action groups and knowledge
  bases through the InvokeAgent API; renamed Agents Classic and closed to new customers on
  30 July 2026, with Amazon Bedrock AgentCore as AWS's recommended path.
verified: 2026-11-16
status: closed-to-new-customers
broader: [kt:amazon-bedrock, kt:generative-ai-on-aws]
related: [kt:amazon-bedrock-knowledge-bases, kt:amazon-bedrock-guardrails]
relations:
  - {predicate: replacedBy, object: kt:amazon-bedrock-agentcore}
  - {predicate: integratesWith, object: kt:aws-lambda}
  - {predicate: scopedTo, object: kt:aws-regions}
sources:
  - id: s1
    title: "How Amazon Bedrock Agents works"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/bedrock/latest/userguide/agents-how.html
    type: official-docs
    accessed: 2026-11-16
  - id: s2
    title: "Amazon Bedrock Agents Classic maintenance mode"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/bedrock/latest/userguide/agents-classic-maintenance-mode.html
    type: official-docs
    accessed: 2026-11-16
  - id: s3
    title: "AWS Service Availability Updates"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/06/aws-service-availability/
    type: official-blog
    accessed: 2026-11-16
  - id: s4
    title: "Amazon Bedrock Agents - Agentic AI frameworks (AWS Prescriptive Guidance)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/prescriptive-guidance/latest/agentic-ai-frameworks/bedrock-agents.html
    type: official-docs
    accessed: 2026-11-16
  - id: s5
    title: "Amazon Bedrock now supports multi-agent collaboration"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/03/amazon-bedrock-multi-agent-collaboration/
    type: official-blog
    accessed: 2026-11-16
  - id: s6
    title: "Amazon Bedrock endpoints and quotas"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/general/latest/gr/bedrock.html
    type: official-docs
    accessed: 2026-11-16
created: 2026-11-16
---

# Amazon Bedrock Agents Classic

Amazon Bedrock Agents was Bedrock's first managed way to build autonomous agents: you describe what the agent should do, attach tools and data, and Bedrock runs the reasoning loop with no infrastructure to provision.[^s4] Launched in November 2023, it was renamed **Amazon Bedrock Agents Classic** and, from 30 July 2026, is no longer open to new customers; existing customers can keep using it and AWS continues to operate and support it.[^s3][^s2] Its status in this graph is therefore *closed to new customers*, not deprecated: AWS states there is no planned end-of-life date and no migration deadline.[^s2]

## How it works

An agent is assembled from a foundation model, plain-language instructions, and at least one action group or knowledge base.[^s1] Each action group is described either by an OpenAPI schema (API operations and the parameters to elicit) or by a simpler function-detail schema, and can optionally point to an AWS Lambda function that executes the chosen action.[^s1] Alternatively the agent can **return control**, handing the predicted action and parameters back in the InvokeAgent response so your own application runs it.[^s1] Four editable prompt templates cover pre-processing, orchestration, knowledge-base response generation and post-processing; after changes you *prepare* the agent to package everything for testing.[^s1] Versions and aliases let teams roll out a tested configuration.[^s4]

At runtime, `InvokeAgent` runs optional pre-processing, then an orchestration loop in which the model writes a rationale, picks an action or knowledge-base query, and feeds the resulting observation back into its prompt until it can answer or needs more input from the user; post-processing is off by default.[^s1] Conversation history within a session is kept and added to the orchestration prompt, and traces expose each rationale, action and model output for debugging.[^s1] Multi-agent collaboration, generally available since March 2025, lets a supervisor agent coordinate specialised agents, and inline agents allow roles to be changed at runtime.[^s5]

## Quotas, pricing and gotchas

Default quotas as of 2026-11, per supported Region: 20 action groups per agent, 11 APIs per agent, 2 associated knowledge bases per agent and 1,000 agents per account (all adjustable); 10 aliases per agent (not adjustable).[^s6] There is no charge for Agents Classic itself; you pay for the model inference and other resources it uses.[^s2]

The maintenance-mode rules are the main gotcha. Only `CreateAgent` and `InvokeInlineAgent` are restricted, and only for AWS accounts without Bedrock Agents usage in the previous 12 months; such accounts get an `AccessDeniedException`, and there is no exception process.[^s2] Everything else (InvokeAgent, PrepareAgent, alias, action-group and knowledge-base APIs, and existing CloudFormation/CDK/Terraform templates for allowlisted accounts) keeps working.[^s2] No new features will be added, and the agent model catalog was frozen on 30 July 2026, although Bedrock inference, Knowledge Bases and Guardrails themselves are unaffected.[^s2]

## When to use it (and when not)

AWS Prescriptive Guidance positioned Bedrock Agents for teams wanting a fast, configuration-driven agent with tight Knowledge Bases and Guardrails integration and no infrastructure to run.[^s4] Accounts that already run it can continue to do so. For anything new, AWS recommends Amazon Bedrock AgentCore: its managed *harness* is the closest config-based analog, and code-defined agents (Strands, LangChain, OpenAI Agents SDK and others) suit custom orchestration.[^s2] Simple agents built from a model, action groups and a knowledge base may migrate in hours; stage-specific prompt overrides and routing-mode multi-agent collaboration do not map directly.[^s2]

## Connections

Agents Classic is a feature **of Amazon Bedrock** and part of **generative AI on AWS**.[^s4] It draws on **Amazon Bedrock Knowledge Bases** for retrieval and on **Amazon Bedrock Guardrails** for safety controls.[^s4][^s2] Action groups **integrate with AWS Lambda** for fulfilment.[^s1] Its quotas are set **per AWS Region**.[^s6] AWS's recommended successor for new work is **Amazon Bedrock AgentCore**, hence the `replacedBy` edge, even though the classic feature remains supported for existing users.[^s2][^s3]

## Sources
[^s1]: Amazon Web Services, "How Amazon Bedrock Agents works". https://docs.aws.amazon.com/bedrock/latest/userguide/agents-how.html (accessed 2026-11-16)
[^s2]: Amazon Web Services, "Amazon Bedrock Agents Classic maintenance mode". https://docs.aws.amazon.com/bedrock/latest/userguide/agents-classic-maintenance-mode.html (accessed 2026-11-16)
[^s3]: Amazon Web Services, "AWS Service Availability Updates" (What's New, 2026-06-30). https://aws.amazon.com/about-aws/whats-new/2026/06/aws-service-availability/ (accessed 2026-11-16)
[^s4]: Amazon Web Services, "Amazon Bedrock Agents", AWS Prescriptive Guidance: Agentic AI frameworks. https://docs.aws.amazon.com/prescriptive-guidance/latest/agentic-ai-frameworks/bedrock-agents.html (accessed 2026-11-16)
[^s5]: Amazon Web Services, "Amazon Bedrock now supports multi-agent collaboration" (What's New, 2025-03-10). https://aws.amazon.com/about-aws/whats-new/2025/03/amazon-bedrock-multi-agent-collaboration/ (accessed 2026-11-16)
[^s6]: Amazon Web Services, "Amazon Bedrock endpoints and quotas". https://docs.aws.amazon.com/general/latest/gr/bedrock.html (accessed 2026-11-16)
