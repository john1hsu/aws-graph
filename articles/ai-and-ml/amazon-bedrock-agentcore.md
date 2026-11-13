---
id: kt:amazon-bedrock-agentcore
type: Service
title: Amazon Bedrock AgentCore
aliases: [AgentCore, Bedrock AgentCore, AWS AgentCore]
domain: ai-and-ml
summary: >-
  AWS's agentic platform of modular, consumption-priced services (Runtime, Memory, Gateway, Identity,
  Browser, Code Interpreter, Observability, Policy, Evaluations and more) for deploying and operating
  AI agents built with any framework and any model, inside or outside Amazon Bedrock.
verified: 2026-11-13
status: current
broader: [kt:generative-ai-on-aws]
related: [kt:amazon-bedrock, kt:amazon-bedrock-guardrails]
relations:
  - {predicate: alternativeTo, object: kt:amazon-bedrock-agents}
  - {predicate: integratesWith, object: kt:aws-lambda}
  - {predicate: securedBy, object: kt:aws-iam}
  - {predicate: monitoredBy, object: kt:amazon-cloudwatch}
  - {predicate: governedBy, object: kt:agentcore-quotas}
  - {predicate: coveredBy, object: kt:aws-certified-ai-practitioner}
sources:
  - id: s1
    title: "Overview - Amazon Bedrock AgentCore (What is Amazon Bedrock AgentCore?)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/what-is-bedrock-agentcore.html
    type: official-docs
    accessed: 2026-11-13
  - id: s2
    title: "Amazon Bedrock AgentCore is now generally available"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2025/10/amazon-bedrock-agentcore-available
    type: official-blog
    accessed: 2026-11-13
  - id: s3
    title: "AgentCore harness is now generally available"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/about-aws/whats-new/2026/06/amazon-bedrock-agentcore-harness-generally-available
    type: official-blog
    accessed: 2026-11-13
  - id: s4
    title: "Amazon Bedrock AgentCore Pricing"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/bedrock/agentcore/pricing/
    type: official-docs
    accessed: 2026-11-13
  - id: s5
    title: "Quotas for Amazon Bedrock AgentCore"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/bedrock-agentcore-limits.html
    type: official-docs
    accessed: 2026-11-13
  - id: s6
    title: "Amazon Bedrock AgentCore FAQs"
    publisher: Amazon Web Services
    url: https://aws.amazon.com/bedrock/agentcore/faqs/
    type: official-docs
    accessed: 2026-11-13
  - id: s7
    title: "In-Scope AWS Services - AWS Certified AI Practitioner (AIF-C01)"
    publisher: Amazon Web Services
    url: https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/aif-01-in-scope-services.html
    type: official-learning
    accessed: 2026-11-13
created: 2026-11-13
---

# Amazon Bedrock AgentCore

Amazon Bedrock AgentCore is AWS's platform for running AI agents in production. Rather than one monolithic agent builder, it is a set of modular services that can be adopted together or one at a time, and it is deliberately framework- and model-agnostic: agents written with Strands Agents, LangGraph, CrewAI, LlamaIndex, Google ADK, the OpenAI Agents SDK or custom code can call models hosted in Amazon Bedrock or elsewhere (for example OpenAI or Gemini).[^s1] After a preview, AgentCore became generally available on 13 October 2025 in nine Regions: US East (N. Virginia, Ohio), US West (Oregon), Asia Pacific (Mumbai, Singapore, Sydney, Tokyo) and Europe (Frankfurt, Ireland).[^s2]

## How it works

The developer guide lists the building blocks (as of 2026-11):[^s1]

- **Runtime** – serverless hosting for agents and tools, with fast cold starts, per-session isolation, long-running asynchronous work, and support for the Model Context Protocol (MCP) and Agent-to-Agent (A2A) protocol.[^s1]
- **Harness** – a managed agent loop: you declare the model, tools, skills and instructions as configuration and invoke the agent with one API call; each session gets an isolated microVM with filesystem and shell access.[^s1] It reached GA on 17 June 2026 in all commercial Regions where AgentCore is offered, and can switch models mid-session or export to Strands-based code.[^s3]
- **Memory** – short-term memory for multi-turn conversations and long-term memory that persists across sessions and can be shared between agents.[^s1]
- **Gateway** – turns REST APIs, Lambda functions and existing MCP servers into MCP-compatible tools; **Identity** – agent identity and outbound credential management that works with existing IdPs such as Amazon Cognito, Okta, Microsoft Entra ID and Auth0.[^s1]
- **Code Interpreter** (sandboxed Python, JavaScript, TypeScript) and **Browser** (managed cloud browser usable from Playwright or BrowserUse).[^s1]
- **Observability** (OpenTelemetry-compatible tracing), **Evaluations**, **Optimization** (AI-generated recommendations and A/B testing), **Policy** (deterministic rules, written in natural language or a Cedar-compatible language, enforced on every Gateway tool call), **Registry** (an organisation-wide catalog of agents, MCP servers and skills) and **Payments** (agent microtransactions with spending limits).[^s1]

Each Runtime session runs in its own Firecracker microVM with its own identity and policy, which AWS describes as hardware-enforced isolation; Browser and Code Interpreter likewise use VM-isolated sandboxes.[^s6] At GA every AgentCore service gained VPC connectivity, AWS PrivateLink, AWS CloudFormation and tagging support; Gateway added IAM authorization alongside OAuth; and Observability added CloudWatch dashboards while staying compatible with third-party OTEL tools such as Datadog, Dynatrace and Langfuse.[^s2]

## Quotas, pricing and gotchas

Key Runtime quotas (as of 2026-11, per Region):[^s5]

- Maximum session lifetime is 8 hours on microVMs and up to 14 days on the newer Instances compute type; idle timeout defaults to 15 minutes. Both are set per agent through lifecycle configuration (`maxLifetime`, `idleRuntimeSessionTimeout`), not through a quota increase.[^s5]
- Active session workloads per account: 5,000 in US East (N. Virginia) and US West (Oregon), 2,500 elsewhere; adjustable through Service Quotas.[^s5]
- Synchronous request timeout is 15 minutes and maximum payload is 100 MB; neither is adjustable. Streaming responses can last up to 60 minutes.[^s5]
- Gateway defaults include 1,000 gateways per account, 100 targets per gateway and 200 TPS of tool calls (all adjustable); Memory allows 150 memory resources per Region (adjustable) and 6 strategies per resource (fixed).[^s5]

Pricing is consumption-based with no upfront commitment, and each service is billed independently.[^s6] As of 2026-11 the pricing page (which names no Region for these list rates) shows Runtime v2 at $0.1276 per vCPU-hour and $0.0169 per GB-hour, with Browser and Code Interpreter at $0.0895 per vCPU-hour and $0.00945 per GB-hour, all billed per second with a 1-second minimum and a 128 MB memory floor.[^s4] CPU is not charged while an agent waits on I/O such as a model response, but memory is billed while held; Runtime v2 reclaims idle memory after 120 seconds.[^s4] Gateway costs $0.005 per 1,000 tool invocations, Identity $0.010 per 1,000 tokens or API keys for non-AWS resources (free when used through Runtime or Gateway), and Policy $0.000025 per authorization request; Guardrails are billed separately under Amazon Bedrock.[^s4]

Compliance gotcha: the FAQ lists AgentCore as HIPAA eligible but says AWS is still *pursuing* FedRAMP, so do not assume it is authorized for FedRAMP workloads; check the Services in Scope pages before using it in regulated environments.[^s6]

## When to use it (and when not)

AWS positions AgentCore for teams taking agents from proof of concept to production, especially when they need framework or model choice, MCP and A2A interoperability, private VPC connectivity, or long-running isolated sessions.[^s6] Amazon Bedrock Agents, the earlier fully managed agent feature inside Bedrock, has no announced deprecation and existing users can keep using it, but it is tied to Bedrock's own orchestration and models.[^s6]

## Connections

AgentCore is part of the broader **generative AI on AWS** stack and is the agent layer that sits beside **Amazon Bedrock** inference; content safety comes from **Amazon Bedrock Guardrails**, billed under Bedrock.[^s4] It is the more flexible **alternative to Amazon Bedrock Agents**.[^s6] Gateway **integrates with AWS Lambda** by exposing functions as MCP tools, access is **secured by AWS IAM** (plus OAuth via Identity), and agents are **monitored by Amazon CloudWatch** through Observability dashboards.[^s1][^s2] Runtime, Gateway and Memory limits are captured in **AgentCore quotas**.[^s5] Amazon Bedrock AgentCore is listed among the in-scope machine-learning services for the **AWS Certified AI Practitioner** (AIF-C01) exam.[^s7]

## Sources
[^s1]: Amazon Web Services, "Overview - Amazon Bedrock AgentCore". https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/what-is-bedrock-agentcore.html (accessed 2026-11-13)
[^s2]: Amazon Web Services, "Amazon Bedrock AgentCore is now generally available" (What's New, 2025-10-13). https://aws.amazon.com/about-aws/whats-new/2025/10/amazon-bedrock-agentcore-available (accessed 2026-11-13)
[^s3]: Amazon Web Services, "AgentCore harness is now generally available" (What's New, 2026-06-17). https://aws.amazon.com/about-aws/whats-new/2026/06/amazon-bedrock-agentcore-harness-generally-available (accessed 2026-11-13)
[^s4]: Amazon Web Services, "Amazon Bedrock AgentCore Pricing". https://aws.amazon.com/bedrock/agentcore/pricing/ (accessed 2026-11-13)
[^s5]: Amazon Web Services, "Quotas for Amazon Bedrock AgentCore". https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/bedrock-agentcore-limits.html (accessed 2026-11-13)
[^s6]: Amazon Web Services, "Amazon Bedrock AgentCore FAQs". https://aws.amazon.com/bedrock/agentcore/faqs/ (accessed 2026-11-13)
[^s7]: Amazon Web Services, "In-Scope AWS Services - AWS Certified AI Practitioner". https://docs.aws.amazon.com/aws-certification/latest/ai-practitioner-01/aif-01-in-scope-services.html (accessed 2026-11-13)
