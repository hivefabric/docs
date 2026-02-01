# AgentFabric Runtime Specification

## Overview

The AgentFabric runtime executes fork-join DAGs (Directed Acyclic Graphs) that define multi-step agent workflows.

## Purpose

AgentFabric is a lightweight, cross-platform runtime for autonomous agents that allows tasks to:

Run in sandboxed containers (mobile, desktop, cloud)

Be scheduled as fork-join DAGs for parallelism and dependency management

Report results back to HiveFabric with audit logs

Earn credits/tokens when executed on user devices

## AgentFabric v0.1

### Agent Definition

An Agent is a self-contained unit of work with:

* Metadata
* Execution rules
* Fork-join DAG tasks
* Runtime resource requirements

### Example Agent Definition
```yaml
agent:
  id: "agent-uuid-1234"
  name: "Electricity Tariff Checker"
  version: "0.1"
  author: "user123"
  description: "Checks best electricity tariffs daily and sends notification"
  runtime: "AgentFabric v0.1"
  schedule:
    type: "cron"
    value: "0 9 * * *"
  tasks:
    - task_id: "fetch-tariffs"
      image: "wasm/fetch-tariffs:v0.1"
      inputs:
        location: "ES-Vigo"
      outputs: ["tariffs.json"]
      resources:
        cpu: 1
        memory: 128MB
      fork_join: false
    - task_id: "notify-user"
      image: "wasm/push-notify:v0.1"
      dependencies: ["fetch-tariffs"]
      fork_join: false
```

### Agent Schema Definition
Agent Schemas define agent types and configurable parameters for users.

#### agent-schema.yaml
```yaml
$schema: "https://yaml-schema.org/draft-07/schema#"
title: "HiveFabric Agent Schema"
description: "Defines agent types, parameters, resources, and marketplace metadata for HiveFabric."
type: object
required:
  - type
  - version
  - parameters
properties:
  type:
    type: string
    description: "Unique identifier for the agent type"
  version:
    type: string
    description: "Semantic version of the agent schema"
  description:
    type: string
    description: "Human-readable description of the agent type"
  parameters:
    type: object
    description: "User-configurable parameters for this agent type"
    additionalProperties: false
    patternProperties:
      ".*":
        type: object
        required: ["type"]
        properties:
          type:
            type: string
            enum: ["string", "number", "boolean", "enum"]
          description:
            type: string
          default:
            type: any
          enum:
            type: array
            items:
              type: string
  resources:
    type: object
    required: ["cpu", "memory"]
    properties:
      cpu:
        type: number
      memory:
        type: string
  fork_join:
    type: boolean
    default: false
  schedule:
    type: object
    properties:
      type:
        type: string
        enum: ["cron", "interval", "manual"]
      value:
        type: string
  marketplace:
    type: object
    properties:
      public:
        type: boolean
        default: true
      credits_required:
        type: number
        default: 10
```

### Example Agent Type
```yaml
type: "electricity-tariff-checker"
version: "1.0"
description: "Checks best electricity tariffs and sends notifications"
parameters:
  location:
    type: string
    description: "City or region for electricity tariffs"
    default: "ES-Vigo"
  notification_type:
    type: enum
    description: "Notification method"
    enum: ["push", "email", "sms"]
    default: "push"
  check_frequency:
    type: string
    description: "Cron expression for agent execution"
    default: "0 9 * * *"
  price_threshold:
    type: number
    description: "Only notify if tariff is below this value"
    default: 0.20
resources:
  cpu: 1
  memory: 128MB
fork_join: false
schedule:
  type: cron
  value: "0 9 * * *"
marketplace:
  public: true
  credits_required: 10
```

### Example Agent Instance (User Configured)
```yaml
agent_id: "user-agent-uuid-9876"
schema_type: "electricity-tariff-checker"
schema_version: "1.0"
user_config:
  location: "ES-Madrid"
  notification_type: "email"
  check_frequency: "0 8 * * *"
  price_threshold: 0.15
resources:
  cpu: 1
  memory: 128MB
fork_join: false
```

HiveFabric validates the user_config against the schema

Executor runs tasks using user_config values

### Task Lifecycle

Scheduled / Triggered – HiveFabric assigns task to executor

Fetch / Prepare – Executor downloads and validates agent bundle

Execute – Sandbox execution (WASM, container)

Fork-Join Handling – Wait for dependencies if required

Report – Output results + logs back to HiveFabric

Credit / Token Accounting – Executor earns proportional tokens

### Executor Interfaces
Mobile (Android / iOS)

Runs inside app sandbox

Resource throttling to avoid battery drain

Limited CPU/memory

Auto-updates runtime via HiveFabric

Desktop / Server (Windows / Linux / macOS)

Background service / daemon

Full CPU/memory access (configurable)

Uses WASM sandbox or lightweight container

Auto-updates via HiveFabric

### Security & Isolation

Executors have unique keypairs for authentication

Agent bundles are signed by HiveFabric

Resource limits enforced per task

Execution logs are tamper-evident and auditable

Enterprise isolation optional (private executor pools, private DAGs)

### Scheduler / Fork-Join Model

Tasks organized in DAGs

Executors pick ready-to-run tasks

Fork-join ensures parallel execution when possible, joins when required

HiveFabric handles load balancing across heterogeneous nodes

### Marketplace & Credit System

Executors earn credits proportional to CPU/memory usage

Users spend credits to run their agents or access premium marketplace agents

HiveFabric retains a fraction for profit

Enterprise agreements support private deployments

### File / Module Structure
```yaml
agent-bundle/
├─ agent.yaml           # Metadata + DAG
├─ wasm/                # WASM modules or containers
│   ├─ fetch-tariffs.wasm
│   └─ push-notify.wasm
├─ assets/              # Optional static assets
└─ signature.sig        # Signed bundle hash
```

