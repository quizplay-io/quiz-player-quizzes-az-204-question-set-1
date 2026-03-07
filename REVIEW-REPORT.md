# AZ-204 Quiz Deep Accuracy Review

**Date:** March 6, 2026
**Scope:** 36 questions + 4 case study scenarios
**Quiz:** `quiz-player-quizzes-az-204-question-set-1`

---

## Summary

| Severity | Count |
|----------|-------|
| Errors (incorrect answer or factual mistake) | 2 |
| Misleading / Debatable | 3 |
| Minor / Outdated | 4 |
| Metadata Issues | 1 |
| Accurate (no issues) | 26 |

**Overall assessment:** The quiz is solid and well-constructed. Most questions, answers, and explanations are technically accurate. However, there are 2 clear factual errors that should be corrected, 3 items that are misleading or debatable enough to warrant revision, and 4 minor items that could be improved for precision.

---

## Errors (Must Fix)

### Q4 — Auto-inflate Explanation Incorrectly Mentions Processing Units

**File:** `questions/04-harborview-eventhub-scaling.json`
**Issue:** The explanation states Auto-inflate scales "Throughput Units (TUs) or Processing Units (PUs)." This is factually incorrect. Auto-inflate only applies to **Throughput Units (TUs) on the Standard tier**. Processing Units (PUs) are used in the Premium tier, which does **not** support Auto-inflate — PU counts must be adjusted manually.

**Current text:**
> "Auto-inflate enables you to automatically scale the number of Throughput Units (TUs) or Processing Units (PUs)..."

**Recommended fix:**
> "Auto-inflate enables you to automatically scale the number of Throughput Units (TUs) in a Standard-tier Event Hubs namespace to meet the needs of your workload when traffic exceeds the configured amount."

**Source:** [Microsoft Learn — Automatically scale up Azure Event Hubs throughput units](https://learn.microsoft.com/en-us/azure/event-hubs/event-hubs-auto-inflate)

---

### Q32 — APIM Policy Ordering: `<base />` Placement Is Not Fixed

**File:** `questions/32-apim-limit-call-rate.json`
**Issue:** The explanation states "`<base />` always comes first inside a policy section to inherit parent policies." This is incorrect. According to Microsoft documentation, `<base />` can be placed **before or after** any policy element in a section. Its position controls the **execution order** of inherited policies relative to custom policies. Placing it first is a common best practice, but it is not a hard rule, and there are legitimate scenarios where base should come after custom policies.

The "correct order" presented is one valid ordering, but the question implies it is the only correct one. This is misleading for an exam-prep context where understanding the flexibility of `<base />` placement is important.

**Current explanation excerpt:**
> "`<base />` always comes first inside a policy section to inherit parent policies."

**Recommended fix:**
> "`<base />` is typically placed first to ensure parent policies are evaluated before custom policies. However, `<base />` placement is flexible — its position within the section controls whether inherited policies run before or after your custom policies. In this scenario, placing it first ensures rate limiting from parent scopes is applied before the custom policies."

**Source:** [Microsoft Learn — Policies in Azure API Management](https://learn.microsoft.com/en-us/azure/api-management/set-edit-policies)

---

## Misleading / Debatable (Should Revise)

### Q8 — DefaultAzureCredential vs ManagedIdentityCredential

**File:** `questions/08-ironclad-managed-identity-code.json`
**Issue:** The question asks which class to use "to automatically handle authentication using the service's assigned Managed Identity." The correct answer is marked as `DefaultAzureCredential`, with `ManagedIdentityCredential` dismissed because "DefaultAzureCredential is preferred as it supports both local dev and production without code changes."

This is **debatable and potentially misleading**. Microsoft's own best-practices documentation for production explicitly states: *"In production, it's better to use something else [other than DefaultAzureCredential]... replace DefaultAzureCredential with a specific TokenCredential implementation, such as ManagedIdentityCredential."*

`DefaultAzureCredential` tries a chain of ~10 credential types and can introduce subtle issues in production (e.g., unexpectedly using environment credentials). When the question specifically states the service uses Managed Identity, `ManagedIdentityCredential` is arguably the more precise and production-appropriate answer.

**Recommendation:** Either accept `ManagedIdentityCredential` as the correct answer (or at minimum as equally valid), or reword the question to emphasize convenience across dev/prod environments to make `DefaultAzureCredential` unambiguously correct. For example: "Which class provides the most seamless authentication experience across both local development and Azure-hosted environments?"

**Source:** [Microsoft Learn — Azure Identity best practices](https://learn.microsoft.com/en-us/dotnet/azure/sdk/authentication/best-practices)

---

### Q8 — ChainedTokenCredential Description

**File:** `questions/08-ironclad-managed-identity-code.json`
**Additional minor issue:** The explanation describes `ChainedTokenCredential` as "a base class used to chain multiple credentials together." It is not a base class — it is a **concrete class** that accepts an ordered list of `TokenCredential` instances and tries each one in sequence. This is a minor but technically incorrect characterization.

**Recommended fix:**
> "ChainedTokenCredential — A class that chains multiple credential types together, trying each in order until one succeeds. While flexible, it requires manual configuration."

---

### Q17 — Cosmos DB Lazy Indexing "Being Phased Out"

**File:** `questions/17-cosmos-indexing-policy.json`
**Issue:** The explanation states "Lazy indexing is no longer recommended and is being phased out in favor of Consistent indexing." This is partially accurate but overstated. Lazy indexing is **restricted for new containers** (you must contact Microsoft for an exemption), and it is discouraged, but Microsoft has not announced a formal deprecation timeline or "phase out." Existing containers using lazy mode continue to work.

**Recommended fix:**
> "Lazy mode — Lazy indexing performs index updates at a lower priority, which can produce inconsistent or incomplete query results. New containers are restricted from using lazy indexing. Microsoft recommends Consistent indexing with proper path inclusion/exclusion instead."

---

## Minor / Outdated (Nice to Fix)

### Q23 — Availability Tests: "Multi-step" Is Deprecated

**File:** `questions/23-app-insights-availability.json`
**Issue:** The explanation mentions "Standard or Multi-step" availability tests. Multi-step web tests were **deprecated by Microsoft in 2024**. The current availability test types are URL ping tests and Standard tests (plus custom TrackAvailability via SDK).

**Current text:**
> "Availability Tests (Standard or Multi-step)..."

**Recommended fix:**
> "Availability Tests (such as URL ping tests and Standard tests)..."

---

### Q30 — Redis Persistence: Incomplete for Enterprise Tier

**File:** `questions/30-redis-data-persistence.json`
**Issue:** The explanation states persistence data is "stored in an Azure Storage account linked to the cache." This is accurate for the **Premium tier** but not for the **Enterprise/Enterprise Flash tiers**, which use **managed disks** attached to the cache instance rather than customer-managed storage accounts. For AZ-204 exam scope (which focuses on Premium tier), this is acceptable, but adding a brief note about Enterprise would improve completeness.

---

### Q9 — ACI YAML Schema Path Notation

**File:** `questions/09-ironclad-aci-deployment.json`
**Issue:** The correct answer is `properties.containers.resources.requests`. This appears to reference the **ARM REST API schema** path rather than the actual YAML file structure. In the YAML file used with `az container create --file`, the path is simply `containers[].resources.requests` (no `properties` prefix). The answer is technically valid if interpreted as the ARM schema, but could confuse test-takers who look at actual YAML deployment files.

**Recommendation:** The question and answer are acceptable as-is for exam purposes, but consider clarifying in the explanation that this refers to the ARM/REST API resource schema.

---

### Q18 — Flex Consumption Plan Not Mentioned

**File:** `questions/18-functions-scaling-plans.json`
**Issue:** The question and answer are correct (Premium plan is the right choice). However, Azure has since introduced the **Flex Consumption plan**, which offers serverless billing with VNet integration support. While not directly relevant to the correct answer, the explanation could be future-proofed by briefly noting this alternative in the "Why not" section for Consumption plan.

---

## Metadata Issues

### Q2 — Filename/ID Mismatch

**File:** `questions/02-novabridge-managed-identity.json`
**Issue:** The file is named `02-novabridge-managed-identity.json` but the internal ID is `az204-02-novabridge-appservice-auth`. While the filename accurately describes the correct answer (Managed Identity), the ID references "appservice-auth" which could cause confusion in code that references question IDs. Not a user-facing issue, but worth noting for consistency.

---

## Verified Accurate (No Issues Found)

The following questions were reviewed and found to be technically accurate with correct answers, sound distractors, and clear explanations:

- **Q1** — Cosmos DB Strong consistency (correct)
- **Q2** — Managed Identity for Key Vault access (correct, despite metadata note above)
- **Q3** — allkeys-lru eviction policy (correct)
- **Q5** — daysAfterModificationGreaterThan lifecycle property (correct)
- **Q6** — Partitions for parallel processing (correct)
- **Q7** — Azure RBAC for Key Vault (correct; "legacy" label for access policies is accurate)
- **Q10** — Service Bus Sessions for FIFO (correct)
- **Q11** — Correlation Filters on subscriptions (correct)
- **Q12** — Redis Sets for unique tags (correct)
- **Q13** — Deployment Slots Traffic Routing (correct)
- **Q14** — Custom Connector for Logic Apps (correct)
- **Q15** — Fan-out/fan-in Durable Functions pattern (correct)
- **Q16** — User Delegation SAS (correct)
- **Q19** — Application Insights Sampling (correct)
- **Q20** — cache-lookup in inbound policy (correct)
- **Q21** — Authorization Code Flow with PKCE (correct)
- **Q22** — Event Grid Dead-lettering (correct)
- **Q24** — az acr build command (correct)
- **Q25** — APIM Developer Portal (correct)
- **Q26** — Subject Filtering in Event Grid (correct)
- **Q27** — Event Grid + Azure Function for secret rotation (correct)
- **Q28** — App Service Diagnostics (correct)
- **Q29** — local.settings.json for dev config (correct)
- **Q33** — userId as partition key (correct)
- **Q34** — Log Streaming for real-time logs (correct)
- **Q35** — Input/Output Bindings for Functions (correct)
- **Q36** — Key Vault Secrets User role (correct)
- **Q31** — VNet Integration (Regional) (correct)

---

## Case Study Scenarios Review

All four case study scenarios were reviewed and found to be **accurate and well-constructed**:

- **NovaBridge Software** — Realistic multi-tier architecture with App Service, Functions, Cosmos DB, and Redis. All technology choices and requirements are valid.
- **HarborView Analytics** — IoT sensor processing pipeline using Event Hubs and Functions is a standard Azure pattern. Lifecycle management requirements are realistic.
- **Ironclad Security Solutions** — Security-focused scenario with Key Vault, Managed Identity, and ACI is well-designed for testing security concepts.
- **Wavelength Media** — Service Bus with Topics/Subscriptions for content delivery is a valid messaging pattern. Redis for metadata caching is appropriate.

---

## Recommendations Summary

1. **Fix Q4 explanation** — Remove "Processing Units (PUs)" from Auto-inflate description
2. **Fix Q32 explanation** — Correct the claim that `<base />` must always come first
3. **Revise Q8** — Either accept ManagedIdentityCredential or reword the question
4. **Update Q23** — Replace "Multi-step" with current availability test types
5. **Minor updates** — Q17 lazy indexing wording, Q8 ChainedTokenCredential description, Q30 Enterprise tier note
