# AgentFabric Examples

## Specification v0.1

### Example 1: Electricity Tariff Checker (Simple DAG)

Agent Type: electricity-tariff-checker
Executor Target: Mobile / Desktop

Agent Bundle Structure:
```yaml
agent-bundle/
├─ agent.yaml
├─ wasm/
│   ├─ fetch-tariffs.wasm
│   └─ push-notify.wasm
├─ assets/
└─ signature.sig
```

#### Agent YAML (bundle):
```yaml
agent:
  id: "agent-uuid-1234"
  name: "Electricity Tariff Checker"
  version: "0.1"
  runtime: "AgentFabric v0.1"
  schedule:
    type: cron
    value: "0 9 * * *"
  tasks:
    - task_id: "fetch-tariffs"
      image: "wasm/fetch-tariffs:v0.1"
      inputs:
        location: "{{user_config.location}}"
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

#### User Instance Example:
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

### Example 2: Multi-Task Weather + Electricity Agent (Fork-Join DAG)

Scenario: Combine weather and electricity price checks; notify only if conditions meet criteria.

#### Agent YAML (bundle):
```yaml
agent:
  id: "agent-uuid-5678"
  name: "Weather + Tariff Checker"
  version: "0.1"
  runtime: "AgentFabric v0.1"
  schedule:
    type: cron
    value: "0 7 * * *"
  tasks:
    - task_id: "fetch-weather"
      image: "wasm/fetch-weather:v0.1"
      inputs:
        city: "{{user_config.city}}"
      outputs: ["weather.json"]
      resources:
        cpu: 1
        memory: 64MB
      fork_join: true
    - task_id: "fetch-tariffs"
      image: "wasm/fetch-tariffs:v0.1"
      inputs:
        location: "{{user_config.location}}"
      outputs: ["tariffs.json"]
      resources:
        cpu: 1
        memory: 128MB
      fork_join: true
    - task_id: "notify-user"
      image: "wasm/push-notify:v0.1"
      dependencies: ["fetch-weather", "fetch-tariffs"]
      fork_join: false
```

#### User Instance Example:
```yaml
agent_id: "user-agent-uuid-2345"
schema_type: "weather-tariff-checker"
schema_version: "0.1"
user_config:
  city: "ES-Madrid"
  location: "ES-Madrid"
  notification_type: "push"
  check_frequency: "0 7 * * *"
  price_threshold: 0.18
resources:
  cpu: 2
  memory: 192MB
fork_join: true
```

✅ This shows fork-join in action: two tasks run in parallel, results joined for notification.

### Example 3: Desktop Executor Task

Scenario: Heavy data processing that cannot run on mobile.

#### Agent YAML (bundle):
```yaml
agent:
  id: "agent-uuid-9012"
  name: "Large CSV Processor"
  version: "0.1"
  runtime: "AgentFabric v0.1"
  schedule:
    type: interval
    value: "3600" # every hour
  tasks:
    - task_id: "download-csv"
      image: "docker/csv-downloader:v0.1"
      inputs:
        url: "{{user_config.csv_url}}"
      outputs: ["data.csv"]
      resources:
        cpu: 1
        memory: 256MB
    - task_id: "process-csv"
      image: "docker/csv-processor:v0.1"
      dependencies: ["download-csv"]
      outputs: ["results.json"]
      resources:
        cpu: 4
        memory: 1GB
```

#### User Instance Example:
```yaml
agent_id: "user-agent-uuid-5678"
schema_type: "csv-processor"
schema_version: "0.1"
user_config:
  csv_url: "https://example.com/data.csv"
resources:
  cpu: 4
  memory: 1GB
fork_join: false
```

Desktop executor handles higher CPU and memory, mobile skipped for this agent.

### 4️⃣ Example 4: Marketplace-Ready Agent

Scenario: Public agent for everyone in the Hive app marketplace.

```yaml
agent_schema:
  type: "daily-news-notifier"
  version: "1.0"
  description: "Fetches top news headlines and sends daily notifications"
  parameters:
    topics:
      type: enum
      enum: ["tech", "finance", "sports", "world"]
      default: ["tech"]
    notification_type:
      type: enum
      enum: ["push", "email"]
      default: "push"
  resources:
    cpu: 1
    memory: 64MB
  fork_join: false
  schedule:
    type: cron
    value: "0 8 * * *"
  marketplace:
    public: true
    credits_required: 5
```

#### User Instance Example:
```yaml
agent_id: "user-agent-uuid-9999"
schema_type: "daily-news-notifier"
schema_version: "1.0"
user_config:
  topics: ["finance", "tech"]
  notification_type: "email"
resources:
  cpu: 1
  memory: 64MB
fork_join: false
```

This example demonstrates a marketplace agent that anyone can select and customize.

### Notes

{{user_config.*}} placeholders are replaced at runtime with user-defined values.

Executors validate agent schema before executing tasks.

Fork-join DAG execution allows parallelism with dependencies.

All examples are cross-platform, adjusted for mobile, desktop, or cloud resources.

Marketplace metadata supports credits/tokens, public/private agents, and versioning.