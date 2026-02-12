# Tailwind Traders – Azure Storage & Web Architecture Case Study

## Case Study Question

Tailwind Traders has the following types of data:

- Media files: Product photos (JPEG) and feature videos (MP4)
- Marketing literature: Sales flyers, customer stories, sizing charts, manufacturing information (PDF)
- Internal corporate documents: Sensitive and non-sensitive Word and Excel files

Currently, users access this content over the internet through a web application hosted on a website.

### Design a storage and access solution in Azure that addresses:

- The types of data represented
- Performance, scalability, and availability
- Security and identity-based access
- Cost optimization
- Support for both external and internal access patterns
- Web application integration

---

## Data Classification

| Data Type | Examples | Classification |
|-------------|----------|----------------|
| Media Files | JPEG, MP4 | Unstructured binary object data |
| Marketing Literature | PDFs | Unstructured document data |
| Internal Documents | Word, Excel | Semi-structured business documents (sensitive) |

---

## Option A — Cloud-Native & Collaboration-Optimized Architecture

### Architecture Overview

- Azure Front Door / Azure CDN
- Azure App Service (Web Application)
- Azure Blob Storage (Media + Marketing content)
- SharePoint Online / OneDrive (Internal Word & Excel documents)
- Microsoft Entra ID for authentication and authorization
- Optional Immutable Blob Storage for compliance workloads

### Design Rationale

#### Media & Marketing Content
- Stored in Azure Blob Storage
- Delivered via Azure Front Door / CDN for global performance
- Blob access tiers (Hot, Cool, Archive) for cost optimization
- Lifecycle management policies for automated tier transitions

#### Internal Corporate Documents
- Stored in SharePoint Online / OneDrive
- Native integration with Microsoft Office
- Supports co-authoring, versioning, and collaboration
- Built-in compliance features (DLP, retention, eDiscovery)

#### Security & Identity
- Microsoft Entra ID for all user authentication
- Managed Identity for App Service to access Blob Storage
- Role-Based Access Control (RBAC)
- HTTPS-only access

### Strengths

- Best-in-class Office collaboration experience
- Strong compliance and governance capabilities
- Lower operational overhead
- More cloud-native and future-proof
- Lower long-term total cost of ownership (TCO)

### Limitations

- Not suitable for legacy applications requiring SMB access
- Less friendly for lift-and-shift file server migrations
- Requires organizational readiness for SaaS collaboration tools

---

## Option B — Enterprise Web + Lift-and-Shift Friendly Architecture (Author’s Approach)

### Architecture Overview

- Azure Application Gateway (with WAF)
- Azure App Service (Web Application)
- Azure Blob Storage (Media + Marketing content)
- Azure Files (Internal Word & Excel documents)
- Microsoft Entra ID for authentication and authorization
- Blob access tiers and lifecycle management
- Private Endpoints for storage access

### Design Rationale

#### Web Security
- Azure Application Gateway provides:
  - Layer 7 load balancing
  - Web Application Firewall (WAF)
  - SSL termination
  - OWASP Top 10 protection

#### Media & Marketing Content
- Stored in Azure Blob Storage
- Access tiers (Hot, Cool, Archive) for cost optimization
- App Service acts as a gatekeeper for storage access

#### Internal Corporate Documents
- Stored in Azure Files
- SMB-based access model
- Supports lift-and-shift from on-prem file servers
- NTFS-style permissions and file share semantics

#### Security & Identity
- Microsoft Entra ID authentication
- Managed Identity for App Service
- Azure Files + Entra ID + NTFS ACLs
- Private Endpoints for secure, private access

### Strengths

- Strong perimeter security using WAF
- Easy migration from on-prem file servers
- Supports legacy and SMB-based applications
- Centralized Azure infrastructure ownership
- Strong zero-trust networking posture

### Limitations

- Poor collaboration experience for Office documents
- No native co-authoring
- Increased operational complexity (SMB, NTFS, networking)
- Higher infrastructure and management overhead
- Less cloud-native, higher long-term technical debt

---

## Service-by-Service Tradeoffs

### Edge & Web Security

| Service | Option A | Option B |
|----------|----------|----------|
| Edge Security | Azure Front Door / CDN | Azure Application Gateway (WAF) |
| Focus | Global performance | Enterprise perimeter security |
| Global Acceleration | Yes | No |
| WAF | Optional (Premium) | Built-in |

---

### Media & Marketing Storage

| Area | Option A | Option B |
|------|----------|----------|
| Service | Azure Blob Storage | Azure Blob Storage |
| Delivery | CDN-optimized | App-controlled |
| Access Tiers | Hot/Cool/Archive | Hot/Cool/Archive |
| Lifecycle Mgmt | Yes | Yes |

---

### Internal Corporate Documents

| Area | Option A | Option B |
|------|----------|----------|
| Service | SharePoint Online | Azure Files |
| Access Model | Web + Office apps | SMB file shares |
| Co-authoring | Yes | No |
| Versioning | Rich | Basic |
| Legacy App Support | No | Yes |
| Lift-and-Shift | Poor | Excellent |
| Cloud-Native | High | Low |

---

### Identity & Access

| Area | Option A | Option B |
|------|----------|----------|
| Identity Provider | Microsoft Entra ID | Microsoft Entra ID |
| App Auth | Managed Identity | Managed Identity |
| Storage Auth | RBAC | RBAC + NTFS ACLs |
| Complexity | Lower | Higher |

---

### Cost & Operations

| Area | Option A | Option B |
|------|----------|----------|
| Ops Overhead | Lower | Higher |
| Network Complexity | Lower | Higher |
| Long-term TCO | Lower | Higher |
| Legacy Support | Low | High |

---

## Executive Summary

Option A is a cloud-native, collaboration-first architecture optimized for modern Microsoft 365 workloads. It provides superior user experience, governance, and lower operational overhead, making it better for long-term cloud maturity.

Option B is an enterprise, lift-and-shift-friendly architecture optimized for organizations migrating from on-premises environments. It offers stronger perimeter security and legacy compatibility but introduces higher operational complexity and reduced collaboration capabilities.

The appropriate option depends on Tailwind Traders’ cloud maturity, legacy dependencies, and long-term modernization strategy.

---

## Key Architectural Insight

There is no single “best” architecture. Mature solution design involves evaluating tradeoffs across security, collaboration, cost, operations, and modernization readiness.

Architect-level decisions are based on context, not just best practices.









# Azure Governance Case Study – Tailwind Traders

## Case Study Overview

### Business Structure

Tailwind Traders has two main business units:
- Apparel
- Sporting Goods

Each business unit has three departments:
- Product Development
- Marketing
- Sales

### Governance Requirements

#### Cost and Accounting
- Each business unit and department must track their Azure spend.
- Enterprise IT must provide company-wide Azure cost reporting.

#### New Development Project (Customer Feedback Project)
- CFO wants all project-related costs tracked.
- Testing workloads must run on lower-cost virtual machines.
- Virtual machines must be named to indicate they belong to the project.
- Non-compliant resources must be automatically identified.

---

## Task 1 – Cost and Accounting

### Option 1: Management Group per Business Unit and Department

### Hierarchy Design

Tenant Root Group
├── Apparel (Management Group)
│ ├── Apparel-ProductDev (MG)
│ │ └── Sub-Apparel-ProductDev
│ ├── Apparel-Marketing (MG)
│ │ └── Sub-Apparel-Marketing
│ └── Apparel-Sales (MG)
│ └── Sub-Apparel-Sales
│
└── SportingGoods (Management Group)
├── SG-ProductDev (MG)
│ └── Sub-SG-ProductDev
├── SG-Marketing (MG)
│ └── Sub-SG-Marketing
└── SG-Sales (MG)
└── Sub-SG-Sales


### Why This Works

- Clear ownership per department
- Cost reports can be generated per subscription
- Enterprise IT can report at:
  - Business unit level (MG)
  - Company-wide (root MG)

### Trade-offs

Pros:
- Strong isolation
- Clear cost ownership
- Easier chargeback/showback

Cons:
- More subscriptions to manage
- Higher operational overhead

---

### Option 2: Subscription per Business Unit + Resource Groups per Department

### Hierarchy Design

Tenant Root Group
├── Sub-Apparel
│ ├── RG-ProductDev
│ ├── RG-Marketing
│ └── RG-Sales
│
└── Sub-SportingGoods
├── RG-ProductDev
├── RG-Marketing
└── RG-Sales


### Why This Works

- Simpler structure
- Fewer subscriptions
- Cost tracked using:
  - Resource groups
  - Tags (Department, CostCenter)

### Trade-offs

Pros:
- Easier to manage
- Lower subscription sprawl
- Faster to set up

Cons:
- Weaker isolation
- Less granular governance
- Harder to apply different policies per department

---

### Final Recommendation for Cost and Accounting

**Option 1 is recommended** if Tailwind Traders needs:
- Strong governance
- Department-level accountability
- Clear separation of responsibilities

**Option 2 is acceptable** if:
- Organization is smaller
- Simplicity is prioritized
- Departments do not need strict isolation

---

## Task 2 – New Development Project (Customer Feedback)

### Requirement: Track All Project Costs

#### Option A: Dedicated Subscription for Project

(Placed under appropriate Business Unit MG or Shared Projects MG)

Sub-CustomerFeedback-DevTest


All project resources live in this subscription.

Pros:
- 100% cost isolation
- CFO can easily track total project spend
- Strong governance

Cons:
- Additional subscription to manage

---

#### Option B: Shared Subscription + Project Resource Group + Tags

Existing Subscription
└── RG-CustomerFeedback-DevTest


Tags applied:
- Project = CustomerFeedback
- Environment = Test

Pros:
- No new subscription required
- Faster to deploy

Cons:
- Risk of cost leakage
- Harder to enforce strict isolation

---

### Final Recommendation for Project Cost Tracking

**Option A (Dedicated Subscription)** is recommended because:
- CFO requirement for full cost capture
- Strong financial accountability
- Cleaner reporting

---

## Enforcing VM Sizing and Naming Compliance

### Requirement
- Use lower-cost VMs for testing
- VM names must indicate project
- Automatically detect non-compliance

---

### Option 1: Azure Policy (Recommended)

Policies:
- Allowed VM SKUs (e.g. B-series, small D-series)
- VM naming convention:
  - Must start with: `CFB-`
- Deny or Audit non-compliant deployments

Examples:
- Allowed sizes: Standard_B2s, Standard_B1ms
- Naming rule: Name must match `CFB-*`

Pros:
- Automatic enforcement
- Prevents non-compliant deployments
- Scales across subscriptions

---

### Option 2: CI/CD + Scripts + Manual Review

- Naming enforced in deployment pipelines
- Cost reviewed manually
- Alerts on large VM sizes

Pros:
- Flexible
- Works without Azure Policy

Cons:
- Easy to bypass
- Not consistent
- Higher operational risk

---

### Final Recommendation for Compliance

**Azure Policy is the best solution** because it provides:
- Automatic enforcement
- Central governance
- Consistent compliance

---

## Well-Architected Framework Alignment

### Cost Optimization
- Right-size VMs (lower-cost SKUs)
- Dedicated subscription for project cost visibility
- Cost allocation using management groups and tags

### Operational Excellence
- Azure Policy for automated governance
- Standardized naming conventions
- Consistent subscription structure

### Security
- Policies inherited via management groups
- Central IT visibility
- Reduced risk of misconfiguration

### Reliability
- Standardized environments
- Controlled deployment patterns

### Performance Efficiency
- Appropriate VM sizing for test workloads
- Avoid over-provisioning

---

## Key Learnings

- Management Groups are for governance and policy inheritance.
- Subscriptions are the primary cost and isolation boundary.
- Resource Groups are for organizing workloads.
- Tags are for cross-cutting cost and business reporting.
- Azure Policy is critical for enforcing standards at scale.
- Landing Zones provide a scalable foundation for enterprise Azure adoption.

---

## Summary

This governance design enables Tailwind Traders to:
- Track costs per business unit and department
- Provide enterprise-wide reporting
- Enforce compliance for new projects
- Align with Azure best practices and the Well-Architected Framework



