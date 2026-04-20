# Kafka-Based Communications Engine — Technical Design

## 1. Overview / Problem Statement
- Current communications are heterogeneous: Email, SMS, APN.
- Challenges:
  - High volume
  - Multiple teams with different delivery needs
  - Need auditability
  - Flexible onboarding for new communications
- Vision: Thin-core engine for orchestration with Kafka as the backbone, supporting real-time, batch, and ad-hoc messaging.

---

## 2. Core Concepts
- **Communication**: Single message to a recipient.
- **Campaigns**: Collections of communications handled via batch metadata.
- **Template + Data**: Templates live in channel services; engine passes unrendered data + template ID.
- **Channels**: Email, SMS, APN (mobile push).

---

## 3. Responsibilities
### Comms Engine
- Accepts communication intent.
- Validates core envelope structure (Avro schema).
- Routes to channel-specific topics.
- Applies default and override routing policies.
- Emits audit and fallback events.

### Channel Services
- Perform channel-specific validation.
- Template interpolation and rendering.
- Provider integration and delivery.
- Emit **status events** (`ACCEPTED`, `REJECTED_VALIDATION`, `FAILED_PROVIDER`, etc.).

---

## 4. Event Model
### Core Envelope (Avro)
- `communicationId` (UUID)
- `campaignId` (optional)
- `correlationId` / `traceId`
- `channelPriority` (ordered list, e.g., `["APN","EMAIL","SMS"]`)
- `deliveryMode` (`REALTIME`, `BATCH`, `ADHOC`)
- `scheduledAt` (timestamp, optional)
- `priority` (numeric)
- `recipient` (channel-agnostic, e.g., email, phone, device token)
- `templateId`, `templateVersion`, `locale`
- `payload` (key-value map)
- `sourceSystem`
- `createdAt`
- `schemaVersion`

### Channel-Specific Extensions
- Optional namespace per channel, validated by that channel service only.
- Allows independent evolution without breaking the core envelope.

### Status Events
- `communication.status` topic.
- Status values:
  - `ACCEPTED`
  - `REJECTED_VALIDATION`
  - `FAILED_PROVIDER`
  - `RETRYING`
  - `DELIVERED`
  - `GAVE_UP`
  - `MANUAL_INTERVENTION_REQUIRED`

---

## 5. Topic Strategy
- **communication.intent** — canonical source-of-truth topic.
- **Channel-specific topics**:
  - `communication.apn.intent`
  - `communication.email.intent`
  - `communication.sms.intent`
- **communication.status** — status / audit topic.
- Engine performs routing + fallback evaluation.

---

## 6. Producer Onboarding
- Producers can:
  - Use REST API (transitional).
  - Publish directly to Kafka.
- Enforced via Avro schema + Schema Registry.
- SDK may provide:
  - Object structures
  - Kafka producer setup
  - Optional convenience helpers
- Minimal dependencies across teams.
- Email may require template dev, SMS/APN usually configuration-only.

---

## 7. Routing & Fallback
- Default engine routing policy: `APN → EMAIL → SMS`.
- Producers can override via `channelPriority` ordered list.
- Engine applies fallback when channel service emits failure status.
- Fully deterministic and auditable.

---

## 8. Batch vs Real-Time vs Ad-Hoc
- Same topic and envelope.
- Differ only via metadata (`deliveryMode`, `scheduledAt`, `campaignId`, `priority`).
- Kafka provides natural throttling.
- Channel services may implement batch-friendly APIs internally.

---

## 9. Schema & Validation
- **Avro + Confluent Schema Registry**.
- Core envelope centrally enforced.
- Channel-specific extensions validated downstream.
- Mixed validation model:
  - Kafka: structural enforcement
  - Consumers: semantic validation.

---

## 10. Auditability & Replay
- Kafka topics are the **source of truth**.
- All communication attempts, delivery statuses, failures are recorded.
- Replayable at any level:
  - Individual messages
  - Campaigns
  - Specific channels.

---

## 11. Operational Considerations
- SDKs for producer teams.
- Monitoring and alerting on status events and DLQs.
- Governance for schema evolution and channel onboarding.
- Security, tenancy, and multi-team deployment.
- Replay and throttling strategies.

---

## 12. Diagram Concepts (ASCII placeholders, to be replaced with visuals)
```
Producer ---> communication.intent ---> Comms Engine ---> Channel Topics ---> Channel Services
                                           |
                                           v
                                      communication.status
```

